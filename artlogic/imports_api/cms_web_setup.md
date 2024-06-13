# CMS Import APP setup
## Context 
![imporap diagram](./assets/importapp_cms_diagram.jpg)
## Task
![task](./assets/task_create_funtion_cms_setup.png)
### (*)Settings
```python
settings =  {
    'sitename': sitename,
    'server_group': server_group,
    'enable_websitebridge_on': [],
    'backup_every': 12, # hours
    'main_domain': '',
    'aliases': [],
    'redirects': [],
    'database': '{}_site'.format(sitename_lower),
    'production_port': production_port,
    'development_port': development_port,
    'product_type': 'ws-{}'.format(template_type),
    'local_proxy_type': 'caching',
    'cache_for': 300,
    'account_id': '',
    'client_name': '',
    'use_viewingrooms_subdomain': False,
}
```
## TO DO:
- [ ] Send request to create CMS from import app
- [ ] Get CMS name
- [ ] Get Ports
- [ ] Use create_template_website(*args) (Return settings)
- [ ] Let artlogic know a site has been created (Extra) 

## Worked
### Created functions:
- `importapp_cms_setup(database)` at [importapp_function](./importapp_function/importapp_cms_setup.py)
```python
def importapp_cms_setup(database):
    if database:
        sitename = check_sitename(database)  
    else:
        raise Exception("FAILED - Empty _database_ value")

    ports_available = get_next_available_port()
    if ports_available:
        development_port = ports_available.get('development_port')
        production_port = ports_available.get('production_port')
    else:
        raise Exception('FAILED - No port has been found')

    return create_template_website(sitename, development_port, production_port, 1)
```
> Still pending: Found out what is the `service_group` of `web-eu-staging-003`
- `check_sitename(sitename)` at [importapp_function](./importapp_function/importapp_cms_setup.py)
```python
def check_sitename(sitename):
    sitename_lower = sitename.lower()
    if sitename_lower in SITES_LOWERCASE.keys():
        raise Exception('FAILED - This site already exists in artlogicservers/sites and cannot be created. Use "s" to checkout the site instead.')
    else:
        return sitename
```
### Testing funtion:
- Had to install `mock` for the tests
```shell
pip install mock
```
#### Test if the funtion recive an empry `str`
- `test_importapp_cms_setup_empty_sitename(self)` at [importapp_cms_setup_test](./tests/importapp_cms_setup_test.py)
```python
def test_importapp_cms_setup_empty_sitename(self):
    with pytest.raises(Exception, match="FAILED - Empty _database_ value"):
        importapp_cms_setup("")
```
#### Test if the sitename already exist in `SITES` (This can be edited to call the dictionary, can be tested with importteam)
- `test_importapp_cms_setup_sitename_already_exists(self)` at [importapp_cms_setup_test](./tests/importapp_cms_setup_test.py)
```python
def test_importapp_cms_setup_sitename_already_exists(self):
    mock_sites_lowercase = {'exhibit_e': ''}
    with mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.SITES_LOWERCASE', mock_sites_lowercase):
        with pytest.raises(Exception, match='FAILED - This site already exists in artlogicservers/sites and cannot be created. Use "s" to checkout the site instead.'):
            importapp_cms_setup("exhibit_e")
```
#### Test if the port can't be found
- `test_importapp_cms_setup_port_dont_exist(self)` at [importapp_cms_setup_test](./tests/importapp_cms_setup_test.py)
```python
def test_importapp_cms_setup_port_dont_exist(self):
    with mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.get_next_available_port') as mock_empty_ports:
        mock_empty_ports.return_value = {}
        with pytest.raises(Exception, match='FAILED - No port has been found'):
            importapp_cms_setup("exhibit_e")
```
#### Test for the creation of CMS
For this test we have to have in mind that there are important functions that have been mocked. We are going to assume that `run_subproces()` or `create_website_project` are working correctly. Also this test don't consider anything that has to do with gitLab.
- `test_importapp_cms_setup_without_git(self)` at [importapp_cms_setup_test](./tests/importapp_cms_setup_test.py)
```python
def test_importapp_cms_setup_without_git(self, importapp_cms_setup_function_without_git_mocks):
    assert importapp_cms_setup("exhibit_e_new") == CMS_SETUP_RETURN_NEW_SITENAME
```
##### Values and mocks
```pyton
CMS_SETUP_RETURN_NEW_SITENAME = {
    'sitename': "exhibit_e_new",
    'server_group': 1,
    'enable_websitebridge_on': [],
    'backup_every': 12, # hours
    'main_domain': '',
    'aliases': [],
    'redirects': [],
    'database': 'exhibit_e_new_site',
    'production_port': 42069,
    'development_port': 42069,
    'product_type': 'ws-gt',
    'local_proxy_type': 'caching',
    'cache_for': 300,
    'account_id': '',
    'client_name': '',
    'use_viewingrooms_subdomain': False,
}
```
- Mocks:
```python
@pytest.fixture
def importapp_cms_setup_function_without_git_mocks():
    with mock.patch('os.path.exists') as mock_exist, \
            mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.get_next_available_port') as mock_ports, \
            mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.LINUX_DEV_SERVER', True), \
            mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.SITES_LOWERCASE', SITES_LOWERCASE_SAMPLE), \
            mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.create_website_project') as mock_create_website_project, \
            mock.patch('cms_importapp_setup.importapp_function.importapp_cms_setup.run_subprocess') as mock_run_subproces, \
            mock.patch('__builtin__.open', mock.mock_open(read_data="TEST")) as mock_file:
            
        mock_exist.return_value = False
        mock_ports.return_value = {
                'production_port': 42069,
                'development_port': 42069,
            }
        mock_create_website_project.return_value = {'test': 'test' }
        
        yield mock_exist, mock_ports, mock_create_website_project, mock_run_subproces, mock_file
```

>  WARNING: ALL THE FUNTIONS FIXTURES NEED TO BE EDITED TO THE REAL PATH AFTER APROVAL:
This means that they have to be in the correct path to the actual funtion

This are the functions that are being mocked:
- `get_next_available_port()` at `/home/artlogic/workspace/core/ArtlogicServers/artlogicservers/sites/__init__.py`
```python
def get_next_available_port():
    """
    python -c "from artlogicservers.sites import get_next_available_port;print(get_next_available_port())"
    """
    last_used_production_port = 0
    last_used_development_port = 0
    for site, data in SITES.items():
        production_port = data.get('production_port') or 0
        development_port = data.get('development_port') or 0
        if str(production_port).isdigit():
            if int(production_port) > last_used_production_port:
                last_used_production_port = int(production_port)
        if str(development_port).isdigit():
            if int(development_port) > last_used_development_port:
                last_used_development_port = int(development_port)
    print('Next available production port: %s' % (last_used_production_port + 1))
    print('Next available development port: %s' % (last_used_development_port + 1))
    print('Please double-check with Mission Control and log these in there. Commit as soon as possible.')
    return {
        'production_port': last_used_production_port + 1,
        'development_port': last_used_development_port + 1,
    }
```
- `create_website_project(website_name)` at `"/home/artlogic/workspace/core/flpy/flpy/gitlab/gitlab_api.py`
```python
def create_website_project(website_name):
    api = ArtlogicGitLabAPI(token = _WEBSITE_API_TOKEN)
    result = api.create_website_project(website_name)
    return result
```
- `run_subprocess(cmd, do_not_remove_temp_script_file = False)` at `/home/artlogic/workspace/core/flpy/flpy/utils.py`
```python
def run_subprocess(cmd, do_not_remove_temp_script_file = False):
    if isinstance(cmd, list):
        cmd = "\n".join(cmd)
    script_filepath = "/tmp/%s.sh" % unique_id()
    f = open(script_filepath, "w")
    f.write("#!/bin/bash\n\n# this script was created by the python flpy.utils.run_subprocess\n# command and may safely be removed.\n\n")
    if not do_not_remove_temp_script_file:
        f.write("rm %s\n" % script_filepath)
    f.write("\n%s\n\n" % cmd)
    f.close()
    args = shlex.split("/bin/bash %s" % script_filepath)
    return subprocess.call(args)
```
---
## Context (study)
### - Send request to create CMS
- Do I have to consider the new way that the servers are connecting between them (change from `ssh` to `IAP`)
#### Request examples in Artlogic
- `/home/artlogic/workspace/core/ArtlogicServers/artlogicservers/commandline/__init__.py`
```python
def request_db(args):
    """
    Request DB from a server admin via apiserver app
    on services-g1
    """
    from flpy.utils import include_url
    db, server = args
    f = open('/opt/artlogic/db-dump-secret', 'r')
    requester_key = ''
    try:
        requester_key = f.read().strip()
    except:
        pass
    finally:
        f.close()
    print('Requesting db \'%s\' from server \'%s\'...' % (db, server))
    try:
        json_result = include_url('https://apiserver-services-g1.artlogic.net/admin/request-db/%s/%s' % (db, server), {'requester_key': requester_key, })
        print('DB successfully requested.')
    except Exception, e:
        print('Requesting DB failed. Please ask someone for help.')
        raise
```
- `/home/artlogic/workspace/core/PylonsRoutes/routes/__init__.py`
```python
def request_config(original=False):
    """
    Returns the Routes RequestConfig object.
    
    To get the Routes RequestConfig:
    
    >>> from routes import *
    >>> config = request_config()
    
    The following attributes must be set on the config object every request:
    
    mapper
        mapper should be a Mapper instance thats ready for use
    host
        host is the hostname of the webapp
    protocol
        protocol is the protocol of the current request
    mapper_dict
        mapper_dict should be the dict returned by mapper.match()
    redirect
        redirect should be a function that issues a redirect, 
        and takes a url as the sole argument
    prefix (optional)
        Set if the application is moved under a URL prefix. Prefix
        will be stripped before matching, and prepended on generation
    environ (optional)
        Set to the WSGI environ for automatic prefix support if the
        webapp is underneath a 'SCRIPT_NAME'
        
        Setting the environ will use information in environ to try and
        populate the host/protocol/mapper_dict options if you've already
        set a mapper.
    
    **Using your own requst local**
    
    If you have your own request local object that you'd like to use instead of the default
    thread local provided by Routes, you can configure Routes to use it::
        
        from routes import request_config()
        config = request_config()
        if hasattr(config, 'using_request_local'):
            config.request_local = YourLocalCallable
            config = request_config()
    
    Once you have configured request_config, its advisable you retrieve it again to get the
    object you wanted. The variable you assign to request_local is assumed to be a callable
    that will get the local config object you wish.
    
    This example tests for the presence of the 'using_request_local' attribute which will be
    present if you haven't assigned it yet. This way you can avoid repeat assignments of the
    request specific callable.
    
    Should you want the original object, perhaps to change the callable its using or stop
    this behavior, call request_config(original=True).
    """
    obj = _RequestConfig()
    if hasattr(obj, 'request_local') and original is False:
        return getattr(obj, 'request_local')()
    else:
        obj.using_request_local = False
    return _RequestConfig()
```
### - Get CMS name
- From the funtion I can assume that it only check if it exist `:70`. The dictionary that contain all the SITENAMES is in `from artlogicservers.sites import SITES_LOWERCASE`.
```python
if sitename_lower in SITES_LOWERCASE.keys():
    # Website already exists, fail with an error
    raise Exception('FAILED - This site already exists in artlogicservers/sites and cannot be created. Use "s" to checkout the site instead.')
```
- Does a function that get the next sitename available exist? Something like this:
```python
get_next_template_sitename():
    last_numer = get_last_template_sitename(SITES_LOWERCASE)
    next_sitename = "ArtlogicSite{}".format(last_number)
    return next_sitename
```
- There is a function in `/home/artlogic/workspace/core/ArtlogicSettings/artlogicsettings/web/sites/__init__.py` But this is the same funtion that create `SITES_LOWERCASE`
```python
def get_sites_settings(clear_cache=False):
    global SITES, SITES_LOWERCASE, SITES_LOWERCASE_MAP, _DATA_SOURCE
    if SITES is None or clear_cache:
        SITES = _LazySettingsDict(WebsiteSettings)
        SITES_LOWERCASE = _LowercaseLazySettingsDict(SITES)
        SITES_LOWERCASE_MAP = _LowercaseLazySettingsKeyMap(SITES)
    return SITES, _DATA_SOURCE
```
If this is the only why of checking for a `sitename` then something like this:
```python
get_next_template_sitename():
    sites_numbers = []
    for sites in SITES_LOWERCASE:
        try:
            site_string = str(sites)
        except:
            continue
        if 'artlogicsite' in sites:
            try:
                number = int(''.join(filter(str.isdigit, site_string)))
                sites_numbers.append(number)
            except:
                continue

    if sites_number:
        max_number = max(sites_numbers)
        return "ArtlogicSites{}".format(max_number)
    else:
        return ''
```

> No need to get the number it can copy the name and check
### - Get Ports
- To check the next available port there is a funtion that is not accesed in the script `get_next_available_port`. Is this function deprecated? This function can be found in `/home/artlogic/workspace/core/ArtlogicServers/artlogicservers/sites/__init__.py`
```python
def get_next_available_port():
    """
    python -c "from artlogicservers.sites import get_next_available_port;print(get_next_available_port())"
    """
    last_used_production_port = 0
    last_used_development_port = 0
    for site, data in SITES.items():
        production_port = data.get('production_port') or 0
        development_port = data.get('development_port') or 0
        if str(production_port).isdigit():
            if int(production_port) > last_used_production_port:
                last_used_production_port = int(production_port)
        if str(development_port).isdigit():
            if int(development_port) > last_used_development_port:
                last_used_development_port = int(development_port)
    print('Next available production port: %s' % (last_used_production_port + 1))
    print('Next available development port: %s' % (last_used_development_port + 1))
    print('Please double-check with Mission Control and log these in there. Commit as soon as possible.')
    return {
        'production_port': last_used_production_port + 1,
        'development_port': last_used_development_port + 1,
    }
```
Runing `python -c "from artlogicservers.sites import get_next_available_port;print(get_next_available_port())"` return this
```txt
Next available production port: 61714
Next available development port: 51714
Please double-check with Mission Control and log these in there. Commit as soon as possible.
{'production_port': 61714, 'development_port': 51714}
```
- Matt has a function to get a port that is ot used.
### - Use create_template_website(sitename, development_port, production_port, server_group)
- For `server_group` static to `web-eu-staging-003` (This can be change after the import for the product)

- For testing I dont want to use the git?

`:59`
```python
git_commit = True
git_add = True
```
`:75`
```python
result = create_website_project(sitename) #NOTE: This has to do with gitLab
```
`:81`
```python
run_subprocess('cd %s;git clone %s' % (sites_path, ssh_url_to_repo))
f = open('%s/.gitignore' % sourcecode_path, 'w')
f.write(UNIVERSAL_GITIGNORE)
f.close()
run_subprocess(
    'cd %s;git add .gitignore;git commit -m gitignore;git push' % sourcecode_path
)
```
`:110`
```python
if git_add:
    cmds.append("""git add .""")
    if git_commit:
        cmds.append("""git commit -am 'UP command - committing initial files for %s'""" % sitename)
        cmds.append("""git push -u origin main""")

        run_subprocess(cmds)

    # check gitignore and add the data/ dir
    f = open('%s/.gitignore' % sourcecode_path, 'r')
    try:
        gitignore = f.read()
    except:
        pass
    finally:
        f.close()
    if not '\ndata/\n' in gitignore:
        f = open('%s/.gitignore' % sourcecode_path, 'a')
        try:
            f.write('\ndata/\n')
        except:
            pass
        finally:
            f.close()
        cmds += [
            "cd %s" % sourcecode_path,
            'git commit .gitignore -m "gitignore upd"',
            'git push',
        ]
```
- Does this get afected by the change of `ssh` to `IAP`?? 

`:79`
```python
ssh_url_to_repo = result.get('ssh_url_to_repo') or 'git@gitlab.com:artlogicmedia/websites/%s.git' % sitename  #NOTE: Does this get affected by the change of ssh
```
- Is this the part that creates the **CMS**?

`:98`
```python
cmds = [
    """echo "CREATING %s" """ % sitename,
    "cd %s" % sites_path,
    """echo " creating site in %s" """ % sites_path,
    """%s create -f --no-interactive --quiet -t "%s" %s development_port='%s' production_port='%s' website_name='%s' theme_name='%s' theme_version='%s' aol_standalone=%s""" % (path_to_paster, templatename, sitename, development_port, production_port, website_name, theme_name, theme_version, aol_standalone),

    # @TODO
    # Run default SVN commands
    "cd %s/%s" % (sites_path, sitename),
    "git add .gitignore",
]
```
#### Context
##### InstallerScripts (create-sites-for-prefilled-server.py)
```python
#!/opt/artlogic/bin/python
"""
UPDATE AND RUN A SITE

Usage:

# create a symlink to this script called 'up' and put it in your home directory...

./up BootrustSite  # run 'git pull' on the site and core modules WITHOUT running 'setup.py develop', then startup the site. If the site is installed for the first time a full update will be run.
./up BooktrustSite -s # start up the site without performing any updates
./up BooktrustSite -f # perform a full update, including dependencies. Use this if things are not working as expected, or if significant changes have occurred to core module dependencies.

"""

import sys
import os
import json
import ConfigParser
from flpy.utils import run_subprocess, echo, run_command, whoami, get_parent_dir
from flpy.db import execute_db_with_params
from flpy.gitlab import create_website_project, UNIVERSAL_GITIGNORE
from artlogicservers.servers import is_linux_dev_server, is_mac_dev_server_on_git
from artlogicservers.sites import SITES_LOWERCASE, get_next_available_port
from artlogicsettings.web.sites import create_site_settings



from flpy.database import execute
from datetime import datetime, timedelta
import time

LINUX_DEV_SERVER = is_linux_dev_server()
MAC_DEV_SERVER_ON_GIT = is_mac_dev_server_on_git()
HOME_FOLDER_ROOT = 'home' if LINUX_DEV_SERVER else 'Users'
PATH_WITHIN_WORKSPACE = '' if LINUX_DEV_SERVER else 'artlogic-git/'


def create_template_website(sitename, development_port, production_port, server_group):
    sitename_lower = sitename.lower()
    workspace_path = '/home/artlogic/workspace'# % (HOME_FOLDER_ROOT, user)
    source_root = workspace_path if LINUX_DEV_SERVER else ('%s/artlogic-git' % workspace_path)
    sites_path = "%s/websites" % source_root
    sourcecode_path = "%s/%s" % (sites_path, sitename)
    core_path = '%s/core' % source_root
    artlogiccms_path = '%s/ArtlogicCMS' % core_path
    template_type = 'gt'
    path_to_paster = '/opt/artlogic/virtualenvs/pylons/bin/paster'

    new_site = False
    crete_new_site = False
    show_artlogicservers_settings = False

    create_git_repository = True

    if not os.path.exists(sourcecode_path):

        fast_install = False

        git_commit = True
        git_add = True
        website_name = sitename
        aol_standalone = False
        theme_name = ''
        domain_name = ''
        theme_version = ''
        new_site = True


        crete_new_site = True
        if sitename_lower in SITES_LOWERCASE.keys():
            # Website already exists, fail with an error
            raise Exception('FAILED - This site already exists in artlogicservers/sites and cannot be created. Use "s" to checkout the site instead.')

        try:
            result = create_website_project(sitename)
        except Exception as e:
            print(e)
            result = {}
        ssh_url_to_repo = result.get('ssh_url_to_repo') or 'git@gitlab.com:artlogicmedia/websites/%s.git' % sitename
        print(ssh_url_to_repo)
        run_subprocess('cd %s;git clone %s' % (sites_path, ssh_url_to_repo))
        f = open('%s/.gitignore' % sourcecode_path, 'w')
        f.write(UNIVERSAL_GITIGNORE)
        f.close()
        run_subprocess(
            'cd %s;git add .gitignore;git commit -m gitignore;git push' % sourcecode_path
        )

        template_type = 'gt'
        templatename = 'templategallerysite'

        # Website name
        website_name = sitename

        show_artlogicservers_settings = True
        aol_standalone = True

        cmds = [
            """echo "CREATING %s" """ % sitename,
            "cd %s" % sites_path,
            """echo " creating site in %s" """ % sites_path,
            """%s create -f --no-interactive --quiet -t "%s" %s development_port='%s' production_port='%s' website_name='%s' theme_name='%s' theme_version='%s' aol_standalone=%s""" % (path_to_paster, templatename, sitename, development_port, production_port, website_name, theme_name, theme_version, aol_standalone),

            # @TODO
            # Run default SVN commands
            "cd %s/%s" % (sites_path, sitename),
            "git add .gitignore",
        ]

        if git_add:
            cmds.append("""git add .""")
        if git_commit:
            cmds.append("""git commit -am 'UP command - committing initial files for %s'""" % sitename)
            cmds.append("""git push -u origin main""")

        run_subprocess(cmds)

    # check gitignore and add the data/ dir
    f = open('%s/.gitignore' % sourcecode_path, 'r')
    try:
        gitignore = f.read()
    except:
        pass
    finally:
        f.close()
    if not '\ndata/\n' in gitignore:
        f = open('%s/.gitignore' % sourcecode_path, 'a')
        try:
            f.write('\ndata/\n')
        except:
            pass
        finally:
            f.close()
        cmds += [
            "cd %s" % sourcecode_path,
            'git commit .gitignore -m "gitignore upd"',
            'git push',
        ]

    settings =  {
        'sitename': sitename,
        'server_group': server_group,
        'enable_websitebridge_on': [],
        'backup_every': 12, # hours
        'main_domain': '',
        'aliases': [],
        'redirects': [],
        'database': '{}_site'.format(sitename_lower),
        'production_port': production_port,
        'development_port': development_port,
        'product_type': 'ws-{}'.format(template_type),
        'local_proxy_type': 'caching',
        'cache_for': 300,
        'account_id': '',
        'client_name': '',
        'use_viewingrooms_subdomain': False,
    }

    return settings


"""
When creating sites for a new prefilled server:
    * increment server_group by 1 - This should correspond to the server group number in sites/__init__.py
    * increase dev_port_base by 50
    * increase prod_port_base by 50
    * increase latest_site_number by 50

These should correspond to the ports and sites directly after the last listed on
https://mc.artlogic.net/documentation/artlogiccms/reserved-ports/

Edit this and add the details for the latest server
"""

if __name__ == '__main__':
    settings_list = []
    server_group = 147
    app_name_template = 'ArtlogicWebsite%04d'
    dev_port_base = 41950
    prod_port_base = 46950
    latest_site_number = 2050
    sites_list = []
    for i in range(1, 51, 1):
        app_name = app_name_template % (i + latest_site_number)
        sites_list.append(app_name)
        development_port = dev_port_base + i
        production_port = prod_port_base + i
        print(app_name, development_port, production_port)
        settings = create_template_website(app_name, development_port, production_port, server_group)
        create_site_settings(app_name, settings)
    print('\nRun the long command below on the server where these sites are meant to be setup.\n')
    print(';'.join(['setup-website %s' % i for i in sites_list]))
```
##### flpy (flpy/gitlab/gitlab_api.py | ArtlogicGitLabAPI)
```python
def create_website_project(self, website_name):
    project_data = self.get_website_project(website_name)
    if not project_data:
        headers = {'Private-Token': self.token}
        params = {
            'name': website_name,
            'namespace_id': 68408513,
            'visibility': 'private',
        }
        result = _post('https://gitlab.com/api/v4/projects', params=params, headers=headers)
        if result.status_code >= 400:
            raise Exception('ERROR %s' % result.status_code)
            project_data = _json.loads(result.text)
        return project_data
```

### Let artlogic know site has been created
- this is check in the `artlogicsetting`/


- Is in this place that all the sites are? `from artlogicservers.sites import SITES_LOWERCASE`?
> If this is the case this means that I need to append the new site to this dictionary and then commit?
---
## Extra:
### Check Domains
`/home/artlogic/workspace/core/ArtlogicServers/artlogicservers/sites/__init__.py`
```python
def check_domains_for_site(site):
    """
    python -c "from artlogicservers.sites import check_domains_for_site;r=check_domains_for_site('Lazinc');print(r)"
    """
    from artlogicservers.servers import get_server_ip, get_domain_target, server_name
    from artlogicservers.servers.permitted_ips import is_cloudflare_ip
    from flpy.route53 import get_tld
    site_lower = site.lower()
    site_data = SITES_LOWERCASE.get(site_lower)
    domains = ([site_data['main_domain'], ] if site_data['main_domain'] else []) + (site_data.get('redirects') or []) + (site_data['aliases'] or [])
    server_ip = get_server_ip()
    is_artlogic_net_only_site = True
    domains_pointed_here = {}
    for domain in domains:
        if domain and not 'artlogic.net' in domain:
            is_artlogic_net_only_site = False
    for domain in domains:
        domain_target = get_domain_target(domain)
        domain_ok = server_ip == domain_target
        if not domain_ok:
            is_cloudflare = True
            for ip in domain_target.split():
                if not is_cloudflare_ip(ip):
                    is_cloudflare = False
            if is_cloudflare:
                domain_ok = server_name().lower() == get_domain_target_from_sentry(domain)
        domains_pointed_here[domain] = domain_ok
    return domains_pointed_here
```
## Notes
### Dependencies
- Had to install `mock` for the tests
```shell
pip install mock
```

- Rory
> Creating the CMS is a manual proces

ArtlogicSite3030 -> ArtlogicSite3030_site

