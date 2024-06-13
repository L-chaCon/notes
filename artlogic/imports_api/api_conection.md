# CMS Import APP setup
## Context 
![imporap diagram](./assets/importapp_cms_diagram.jpg)
## Task
### General
![task](./assets/task_api_cms.png)
### Function structure

## TO DO:
- [ ] Create the SQLite that stores the process queue data
- [ ] Recive a request and proceses the data
- [ ] Respond json of object created
## Worked
### Functions created:
This funtion recive the request to create a new cms project 
#### `artlogicimportscmsapi/routes/process_queue.py`
```python
@process_queue_app.route("/process-queue/<string:process_code>/<string:external_request_uid>", methods=['POST'])
def process_queue_request(process_code, external_request_uid):
    request = flask.request 
    data = request.json
    process_queue = ProcessQueue.query.filter_by(external_process_uid=external_request_uid).first()
    if process_queue == None:
        # SETUP CMS
        if process_code == "setup_cms":
            if data.get('sitename'):
                sitename = data.get('sitename')
            else:
                return jsonify({'error_message': "NO SITENAME IN REQUEST"}), 400
            try:
                setup_cms_process = create_new_setup_cms_process(external_request_uid, sitename)
            except Exception as e:
                return jsonify({"error_message": "Sitename already exist: {}".format(e[0])}), 400
            process_queue = ProcessQueue.query.get(setup_cms_process.request_id)
            ## CREATE THE MARSHMELOW
            response = get_marshmallow_cms_process(setup_cms_process, process_queue)
            return jsonify(response), 201
            ## IMPORT DATA FROM BUCKET
        elif process_code == "import_data_from_bucket":
            return jsonify({'error_message':"import_data_from_bucket IS NOT IMPLEMENTED YET"}), 400
            ## CODE NOT IMPLEMENTED
        else: 
            return jsonify({'error_message': "PROCESS CODE '{}' IS NOT IMPLEMENTED".format(process_code)}), 400
    else: 
        # SETUP CMS
        if process_code == "setup_cms":
            setup_cms_process = SetupCMSProcess.query.filter_by(request_id=process_queue.request_id).first()
            ## CREATE THE MARSHMELOW
            response = get_marshmallow_cms_process(setup_cms_process, process_queue)
            return jsonify(response), 201
            ## IMPORT DATA FROM BUCKET
        elif process_code == "import_data_from_bucket":
            return jsonify({'error_message': "import_data_from_bucket IS NOT IMPLEMENTED YET"}), 400
            ## CODE NOT IMPLEMENTED
        else:
            return jsonify({'error_message': "PROCESS CODE '{}' IS NOT IMPLEMENTED".format(process_code)}), 400
```
##### funtions called 
###### lib/
- `artlogicimportscmsapi/lib/cms_setup.py`
```python
def create_new_setup_cms_process(external_process_uid, sitename):
    "This function returns new created object"
    process_queue_id = create_new_process_queue(external_process_uid, "setup_cms").request_id
    new_setup_cms_process = SetupCMSProcess(
        request_id = process_queue_id,
        sitename = sitename,
        status="",
        settings = ""
    )
    db.session.add(new_setup_cms_process)
    db.session.commit()
    return new_setup_cms_process
```
- `artlogicimportscmsapi/lib/process_queue.py`
```python
def create_new_process_queue(external_process_uid, process_queue_code):
    new_process_queue = ProcessQueue(
        external_process_uid = external_process_uid,
        status = "pending",
        process_queue_code = process_queue_code 
    )
    db.session.add(new_process_queue)
    db.session.commit()
    return new_process_queue
```
- `artlogicimportscmsapi/lib/marshmallow_response.py`
```python
def get_marshmallow_cms_process(setup_cms_process, process_queue):
    process_queue_schema = ProcessQueueSchema()
    setup_cms_process_schema = SetupCMSProcessSchema()
    process_queue_output = process_queue_schema.dump(process_queue).data
    setup_cms_process_output = setup_cms_process_schema.dump(setup_cms_process).data
    return {'process_queue': process_queue_output, 'setup_cms_process': setup_cms_process_output}
```
###### models/
- `artlogicimportscmsapi/models/setup_cms_process.py`
```python
class SetupCMSProcess(db.Model):
    setup_id = db.Column(db.Integer, primary_key=True)
    request_id = db.Column(db.Integer, db.ForeignKey('process_queue.request_id'), unique=True)
    sitename = db.Column(db.String(255), nullable=False, unique=True, index=True)
    settings = db.Column(db.String(255), nullable=False)
    status = db.Column(db.String(255), nullable=False)

    process_queue = db.relationship('ProcessQueue', backref=db.backref('setup_cms_process', uselist=False))

    def __repr__(self):
        return '<SetupCMSProcess %r - %r>' % (self.request_id, self.sitename)
```
- `artlogicimportscmsapi/models/process_queue.py`
```python
class ProcessQueue(db.Model):
    request_id = db.Column(db.Integer, primary_key=True)
    external_process_uid = db.Column(db.String(255), unique=True, nullable=False, index=True)
    process_queue_code = db.Column(db.String(255), nullable=False)
    status = db.Column(db.String(50), nullable=True)
    creation_date = db.Column(db.DateTime, nullable=False, default=datetime.utcnow)
    end_date = db.Column(db.DateTime, nullable=True)

    def __repr__(self):
        return '<ProcessQueue %r - %r>' % (self.request_id, self.external_process_uid)
```
> Status options -> ["pending", "processing", "failed", "completed"]
###### schema/
- `artlogicimportscmsapi/schema/process_queue_schema.py`
```python
class ProcessQueueSchema(ma.ModelSchema):
    class Meta:
        model = ProcessQueue
        load_instance = True
```
- `artlogicimportscmsapi/schema/setup_cms_process_schema.py`
```python
class SetupCMSProcessSchema(ma.ModelSchema):
    class Meta:
        model = SetupCMSProcess
        load_instance = True
```
### Testing Funtions
#### Config
- `tests/conftest.py`
```python
@pytest.fixture
def app():
    """Create and configure a new app instance for each test."""
    db_fd, db_path = tempfile.mkstemp()
    app = create_app({
        "TESTING": True,
        "SQLALCHEMY_DATABASE_URI": "sqlite:///{}".format(db_path),
        'DEBUG': True
    })

    with app.app_context():
        db.create_all
        from artlogicimportscmsapi.models.process_queue import ProcessQueue
        from artlogicimportscmsapi.models.setup_cms_process import SetupCMSProcess

        # POPULATE WITH TEST DATA
        populate_test_data(db)
        
        yield app
    
    os.close(db_fd)
    os.unlink(db_path)

@pytest.fixture
def client(app):
    """A test client for the app."""
    return app.test_client()
```
> `populate_test_data` is recommended to be a file. In the meantime is hardcode
```python
def populate_test_data(db):
    format = '%d/%m/%y %H:%M:%S'
    test_porcess_queue_data = [
        ProcessQueue(request_id=1, external_process_uid='existing_external_process_uid', status='completed', process_queue_code='setup_cms', creation_date=datetime.strptime('03/06/24 10:00:00', format), end_date=datetime.strptime('03/06/24 10:10:00', format)),
        ProcessQueue(request_id=2, external_process_uid='external_process_uid_2', status='completed', process_queue_code='setup_cms', creation_date=datetime.strptime('03/06/24 10:11:00', format), end_date=datetime.strptime('03/06/24 10:15:00', format)),
        ProcessQueue(request_id=3, external_process_uid='external_process_uid_3', status='failed', process_queue_code='setup_cms', creation_date=datetime.strptime('03/06/24 10:16:00', format), end_date=datetime.strptime('03/06/24 10:17:00', format)),
        ProcessQueue(request_id=4, external_process_uid='external_process_uid_4', status='processing', process_queue_code='setup_cms', creation_date=datetime.strptime('03/06/24 10:18:00', format)),
        # ProcessQueue(request_id=4, external_process_uid='external_process_uid_4', status='pending', process_queue_code='setup_cms', creation_date=datetime.strptime('03/06/24 10:18:00', format)),
        ProcessQueue(request_id=5, external_process_uid='external_process_uid_5', status='pending', process_queue_code='setup_cms'),
    ]
    db.session.bulk_save_objects(test_porcess_queue_data)
    test_setup_cms_process = [
        SetupCMSProcess(setup_id=1, status="completed", request_id=1, sitename='existing_sitename', settings = "{\"settings\":\"test_settings\"}" ),
        SetupCMSProcess(setup_id=2, status="completed", request_id=2, sitename='completed_sitename', settings = "{\"settings\":\"test_settings\"}" ),
        SetupCMSProcess(setup_id=3, status="failed", request_id=3, sitename='failed_sitename', settings = "" ),
        SetupCMSProcess(setup_id=4, status="", request_id=4, sitename='pending_sitename_1', settings = "" ),
        SetupCMSProcess(setup_id=5, status="", request_id=5, sitename='pending_sitename_2', settings = "" ),
    ]
    db.session.bulk_save_objects(test_setup_cms_process)
    db.session.commit()
```
###### tests/routes
- `tests/routes/process_queue_test.py`
```python 
class TestProcessQueue:
    def test_status(self, client):
        response = client.get('/status')
        assert response.status_code == 200
        assert b'OK' in response.data

    def test_process_queue_request_no_implemented_process_code(self, client):
        response = client.post(
            '/process-queue/no_implemented_process_code/this_is_an_importapp_UID',
            json = {} 
        )
        assert response.status_code == 400
        assert b"PROCESS CODE 'no_implemented_process_code' IS NOT IMPLEMENTED" in response.data

    def test_process_queue_request_setup_cms_empty_sitename(self, client):
        data = {
            "sitename": ""
        }
        response = client.post(
            '/process-queue/setup_cms/this_is_an_importapp_UID',
            json = data 
        )
        assert response.status_code == 400 
        assert b'NO SITENAME IN REQUEST' in response.data

    def test_process_queue_request_setup_cms_sitename_already_exist(self, client):
        data = {
            "sitename": "existing_sitename"
        }
        response = client.post(
            '/process-queue/setup_cms/external_process_uid_1',
            json = data 
        )
        assert response.status_code == 400
        assert b'Sitename already exist:' in response.data
    
    def test_process_queue_request_setup_cms(self, client):
        data = {
            "sitename": "new_sitename"
        }
        response = client.post(
            '/process-queue/setup_cms/this_is_an_importapp_UID',
            json = data 
        )
        assert response.status_code == 201
        assert b'process_queue' in response.data
```
###### test/lib
- `lib/cms_setup_test.py`
```python
class TestSetupCMSFromImportApp:
    def test_create_new_setup_cms_process(self, app):
        with app.app_context():
            last_setup_cms_process = SetupCMSProcess.query.order_by(-SetupCMSProcess.setup_id).first()
            target_id = last_setup_cms_process.setup_id + 1
            assert create_new_setup_cms_process("this_is_a_external_uid", "test_sitename").setup_id == target_id

    def test_create_new_setup_cms_process_sitename_already_exist(self, app):
        with app.app_context():
            with pytest.raises(Exception, match="UNIQUE constraint failed"):
                 create_new_setup_cms_process("this_is_a_external_uid", "existing_sitename")
```
- `lib/process_queue_test.py`
```python
class TestProcessQueue:
    def test_create_new_process_queue(self, app):
        with app.app_context():
            last_process_queue = ProcessQueue.query.order_by(-ProcessQueue.request_id).first()
            target_id = last_process_queue.request_id + 1
            assert create_new_process_queue("this_is_a_external_uid", "test_process_code").request_id == target_id

    def test_create_new_process_queue_exisitng_uid(self, app):
        with app.app_context():
            with pytest.raises(Exception, match="UNIQUE constraint failed"):
                create_new_process_queue("existing_external_process_uid", "test_process_code")
```
#### Extra
In the `requests` folder inside `tests` there are basic request to test functionalities. There is a better explication in `tests/requests/README.md`.
###### 
---
## Context & Notes
### Objective
1. Have a database that has at least this columns

#### Tables
##### ProcessQueue
|Key | Column Name | Data Type|
:---: | :---: | :---:
|PK| request_id | TEXT PRIMARY KEY |
|| external_process_uid | TEXT UNIQUE |
|| process_queue_code | TEXT |
|| status | TEXT |
|| creation_date | DATE |
|| end_date | DATE(null) |
###### Status
1. pending
2. processing
3. failed
4. completed
##### SetupCMSProcess
|Key | Column Name | Data Type|
:---: | :---: | :---:
|PK FK| request_id | INT |
|| sitename | TEXT UNIQUE |
|| settings | TEXT |

2. Give a responce right away with the status
3. Run at restart the server. (If the server fails, runs again at the restart)
4. Let know the import app when the "transaction" is completed
- extra: 
1. Recive a reply back
2. auth for flask
3. cahe for flask
##### File Structure
First, I need to create the funtion that recive the call:
```python
def cms_setup(sitename, importappUID):
    
    if importappUID:
        check_importUID(importappUID)
    else:
        return <error_request> -->  400 Bad Request – client sent an invalid request, such as lacking required request body or parameter
```

### Request handeling
#### FLASK
##### args
`flask.request.args`
```shell
CALL:
<url>/<path>?test=test
RESPONSE
{
  "test": "test"
}
```
- Also `flask.request.values` works the same way
##### [Authorization](https://werkzeug.palletsprojects.com/en/3.0.x/datastructures/#werkzeug.datastructures.Authorization)
`flask.request.authorization`
##### [Cach](https://werkzeug.palletsprojects.com/en/3.0.x/datastructures/#werkzeug.datastructures.RequestCacheControl)
`flask.request.cache_control`
##### Cookies
`flask.request.cookies`
##### Data
For data can be: `flask.request.data` or to get the raw data: `falsk.request.get_data()`

### Queue Managment


### Funtions that are used:
- this is new in the cms_setup
```python
def create_site_settings(site, settings_dict, publish_to_gcs=True):
    """Creates new website settings, throws exception if validation fails"""
    settings = WebsiteSettings(site, **settings_dict)
    if settings.errors:
        raise _ValidationError(settings.errors)
    settings.put(publish_to_gcs=publish_to_gcs)
```

