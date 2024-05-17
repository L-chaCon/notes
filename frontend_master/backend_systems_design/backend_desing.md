# Backend Systems Design
2024-05-16

## What are Backend Architecture Design? (WHAT)
quote from AI:
> "Success begins with understanding. Before we lay a single brick, it is crucial that we fully grasp the blueprint, for a thorough comprehension of the foundation leads to a structure that stands the test of time."

The process of defining the modularity, interface, and data flow for a system to satisfy specified requirements
- Modularity: how the system is divided into components
- Interfaces: in and out of the systems. 
- Data Flow: how information routs through the system. 
> You have to be confertoble to build things in papper first
### Importance
- Facilitate communication between among team members
- Improve efficiency and productivity by reducing redundancy and facilitating reuse
- Insuring the final pruduct meets the requirements
> **tools:** [lucid](https://www.lucidchart.com/pages/?), [exadrwa](https://excalidraw.com/)
### Key Principles
1. Modularity
2. Scalability
3. Robustness
4. Flexibility
#### Modularity
Each component of the system should have a specific task
#### Scalability
System should be desinged to handle growth of traffic.You want to make sure every module can scale
#### Robustness
System should be able to handle errors or unexpected situations
> Build your own error lenguage. Create your own error codes. Make way easer to debug and trace, eaven to customers service
#### Flexibility
Systems should be designed to acomeadate changes or future features
### Key Challenges
1. Complexity
2. Adaptability
3. Security
4. Tecnology
5. Resources
6. Stakeholders
#### Complexity
Handeling the intricate details and indepencies in a system can be complex
> Make something that is shearable, make it simple. If you can't explain it to your team you are doing it wrong

Question to ask yourself:
- What probelme are you trying to solve?
- How complex you want to solve it?
> The biggest thing you want to do is to make is easy. Also this save money. How much do you want to explore new things? This depends on the time that you have. If you dont have any deadline, explore what you want. Have a structure plan of lealing.
#### Adaptability
Disigning system that can adapt to changing business need is challenging.
> The exaple is diving into tears (as an example). In this case there were 5 tears: frontend, backend, gateways, runners, and workers.
#### Security
Ensuring the security of data and processes withing the system is a constant concern
> Rotating pass words. (this is not escalable, but can be a solution)
#### Technology choices
Choosing the right technology stack for the system is crucial and can be difficult
#### Resources management
Allocating right resources for the system's development and maintenance is a challenge
this can be:
- Time
- Money
- Developers
#### Stakeholders
Getting stakeholders to agree on the system's design is crucial
> Don't do changes without saying it

## When to use Backend Architecture Design? (WHEN)
quote from AI:
> "The right time to act is not simply found, but felt. It arises at the intersection of preparation and intuition, where opportunity's whisper becomes too compelling to ignore."

> Take time to think what you need. 
### Understanding when: 
- When starting a new project, as "greenfield" scenarios allows for structured approach from the beginning.
- When scaling a an existing system, system design can be used to identify bottlenecks and areas of imporvement.
- When there are complex problems that need to be broken down into manageable parts.
- When there is a need for improve efficiency and productivity - system design can help in reducing redundancy and facilitating reuse.
- When there is a need for better communication among team members - system design provide a common language and reference point.
### Factoring when:
#### Project Requirements
The functionality, performance, security, and scalability needs the project
#### Team Expertise
The experience and skill set of the development team can influence the design
#### Budget
Certain design may requere more resources, afecting the cost of the project
#### Time constraints
Some design may take logner to implement.
#### Project size and complexity
Larger and more complex projects may benefit from certain design
#### Maintenance
The ease of maintenance and updating the system can influence the choice of design
### Considering when:
#### Tecnology Choices
Keep track of the last tecnologies and trends but no need to use them all
#### User Feedback
User feedback can help in identifying areas of improvement and imporve the system design
#### Market Trends
Understanding the demand and trends of the market also afect the system design
#### Legal and Regulatory Requirements
Compliance with laws and regulations may dictate the system design.
> What is a good sign that I have to redisgn the system?
Or should it be change entirely?
First find evidence. Start with a small change and see how it goes. if the result are good, then maybe is a good idea to change the entire system. 
## How to implement Backend Architecture Designs? (HOW)
quote from AI:
> "Mastering something isn't a matter of precise science; it's a craft honed through the persistent trial of experience, learning and adapting as we navigate the ever-unfolding journey."

### Design stages
1. Research
2. Implement
3. Iterate

### Research
This should take between a week to a month.
#### Setting requirements
- Identify the primary purpose and scope of the system.
> Everyone have a voice. But you don't want to be the guy that scream that everythiong is on fire. Be patient
- Understand the needs and expectations of the end-users of the system.
> In general is or a Developer or a user 
- Determine the functionality that the system needs to provide to meet the end-users' needs.
> This is a good situation to remember not going out of scope
- Specify the performance levels that the system needs to achieve.
- Identify any constraints or limitations, such as budget, resources, or time.
> Challenge yourself. before someone else does, Be a sales man. Be prepere to answer a question, or lead to a question that you can answer
- Consider the scalability needs of the system - how it can grow and adapt to increased demand.
- Determine any security requirements to protect data and processes within the system.
> You need to think in data protection!!
- Identify any regulatory or compliance requirements that the system needs to meet.
- Specify the requirements for integrating the new system with existing systems.
> This is a good moment to see what you had before. Check your own documentation

Remember that this is just thinking not implementing
#### Exploration
Is good to practice, but depend of time. ALWAYS AFTER THE REQUIREMENTS!!
- Research and testing of new technologies.
- Experimenting with new methodologies and tools.
> Maybe is a good time to see if you may need to change your mantence methodology, or interfaces
- Determine if the goal is to improve the system design entirely (innovation) or make small changes where needed (iteration).
> This may be the more important part.
#### Technical Documents
- Used for understanding possible explored solutions or ideas which keep the project on path.
> This is the moment to write the documentation.
- Provide detailed descriptions of the explored systems, including architectures, modules, interfaces, and data relevant to the project.
> Treat it as a journal. For a new world
- Reference for the development team and other stakeholders.
- Facilitate communication and collaboration.
### Implementing Systems Designs
#### key steps:
1. Chose the appropriate design
2. Define the architecture
3. Develop the architecture
4. Test the architecture
5. Deploy the architecture
6. Maintain the architecture
#### Chosing the appropriate design
Choose the systems design that best fits the project requirements, team expertise, budget, and time constraints outlined in technical documents mentioned.
> If you still don't know what to do. Go back. This should be a no brainier
#### Define the architecture
Document the system’s architecture, including its modules, interfaces, and data.
> Here is where you put everything in a paper, here is where workflows diagrams works well. Here is where you want to move things 
#### Develop the architecture
Begin the development process, ensuring that each component of the architecture is working on its specific task.
> The golden rule is that you can give this to another developer and he will know what to do. The developer can eaven pick the tecnologuie that he wants to use. You should be confident at this point.
#### Test the architecture
Conduct rigorous testing to identify and fix any bugs or issues and to ensure that the architecture meets the project requirements.
> Here can be integration test, E2E test, 
#### Deploy the architecture
Once the architecture passes all tests, deploy it in a controlable production environment.
#### Maintain the architecture
Regularly update and maintain the architecture to ensure its ongoing effectiveness and to accommodate any changes in project requirements or business needs.
> This part is when you actually going to realice how everything works
### Best Practices
- Involve all stakeholders from the beginning to ensure their needs and expectations are met.
> This is in the research part
- Use a modular approach to keep the system manageable and understandable.
- Plan for scalability from the beginning to prepare for future growth.
> This is more for the Testing phase
- Design systems to be robust and flexible to handle errors or unexpected inputs and accommodate changes.
> Have alway a way out. If you are sceard of a part, modularize it to be able to change it with another service.
- Consider security needs throughout the design and development process.
> Include this team at the beggining
- Keep up with the latest technological trends to make informed design decisions.
- Incorporate feedback from users to improve the system design.
> This should be in all phases
## Common Backend System Architectures
Three commonly used architectures
1. Monolithic
2. Distributed (service-oriented)
3. Serverless

### Monolithic
#### Definition
Monolithic architecture is a model where all the necessary code and components for a software application are combined into a single unit.
![Monolithic](https://github.com/L-chaCon/images/blob/main/frontend_master/backend_systems_design/Monolithic.png?raw=true)
#### Overview
- Monolithic architecture has all functionalities tightly coupled, running in the same system.
- This architecture is simple to develop, test, and deploy due to its unified system.
##### Pros
- Simplicity: Easier to develop, test, and deploy due to unified system.
- Consistency: Allows for uniformity in handling requests as every module uses the same set of procedures.
- Efficiency: Since all the functionalities are interconnected, it can be more efficient in terms of inter-process communication.
##### Cons
- Limited Scalability: Scaling specific functions of a system is not possible. The entire system needs to be scaled.
- Lack of Flexibility: Changes to a single component can require the entire system to be redeployed.
- Complexity: The system can become too complex and hard to manage as the application grows.
#### Use Cases
- Small-scale applications: Given its simplicity, a monolithic architecture is often suitable for small-scale applications or startups where the application's scope is clear and unlikely to drastically change or scale.
- Applications with simple, well-defined business processes: Monolithic architecture can be beneficial in scenarios where the business processes are simple and unlikely to require significant changes or additions.
- Applications where high performance is critical: Since all functionalities are interconnected, a monolithic architecture can provide faster inter-process communication compared to other architectures.
### Distributed (service-oriented)
#### Definition
Generic services and/or microservices architecture is a method of developing software systems that are loosely coupled and independently deployable smaller services, which run in their own processes.
#### Overview
- This architecture allows for continuous delivery and deployment of large, complex applications. It also enhances an organization's capability to innovate and reduces the time to market for new features.
#### Distributed (generic services)
##### Definition
- "Generic services" often refers to a component of an application that provides specific functionality for the platform.
- "Generic services" could be part of a monolithic application where all services run within the same process, or it could be part of a distributed system where services may run in separate processes or on separate machines.
![Generic](https://github.com/L-chaCon/images/blob/main/frontend_master/backend_systems_design/Distributed%20(generic%20services).png?raw=true)
#### Distributed (microservices)
##### Definition
- "Microservices" are a specific style of service-based architecture. In a microservices architecture, each service is small, independent, and loosely coupled.
- "Microservices" run in their own process and communicate with other "services" using protocols such as HTTP/REST or messaging queues. They can be developed, deployed, and scaled independently, which offers more flexibility than traditional service architectures.
![Microservices](https://github.com/L-chaCon/images/blob/main/frontend_master/backend_systems_design/Distributed%20(microservices).png?raw=true)
#### Pros
- Independent Development: Each service can be developed independently by a team that is focused on that service.
- Independent Deployment: Services can be deployed independently.
- Fault Isolation: A process failure should not bring the whole system down.
- Mixed Technology Stack: Different services can use different technologies.
#### Cons
- Distributed System Complexity: Developers must deal with the additional complexity of creating a distributed system.
- Development and Testing: Writing and testing applications is more difficult due to it being a distributed system.
- Data Management: Managing data consistency can be challenging.
#### Use Cases
- E-commerce platforms: They often need to handle high volumes of transactions and user interactions, which can be efficiently managed through independent microservices.
- Social media platforms: The various functionalities like posting, messaging, and notifications can be divided into separate microservices.
- Streaming services: Microservices can help handle the heavy load and deliver smooth streaming experience.
- Online gaming platforms: They can use microservices to manage game logic, player data, and real-time multiplayer interactions separately.
- Large-scale IoT systems: Each device or sensor type can be managed by a dedicated microservice.
### Serverless
#### Definition
Serverless architecture refers to applications that significantly depend on third-party services (backend-as-a-service or "BaaS") or on custom code that's run in ephemeral containers (function as a service or "FaaS").
#### Overview
- Serverless architectures, the app logic is still run on servers, but all the server management is done by the cloud provider (AWS, Azure, GCP, etc). You just need to run your code, and the cloud provider takes care of the rest.
![Serverless](https://github.com/L-chaCon/images/blob/main/frontend_master/backend_systems_design/Serverless.png?raw=true)
#### Pros
- No server management is required.
- Costs based on usage, not on pre-purchased capacity.
- Automated scaling.
#### Cons
- The architecture can be more expensive for long-term applications.
- Testing can be difficult due to the environment's reliance on the Internet.
- Troubleshooting and debugging is also more complex.
#### Use Cases
- Real-time file processing: As soon as a file is uploaded in S3, AWS Lambda can trigger a function to process it.
- Real-time stream processing: Perform real-time analytics on data streams using Kinesis.
- Extract, transform, load (ETL): Perform ETL on demand. For example, when a new file is uploaded to S3, AWS Lambda can trigger to process the data and load it into a database.
- Websites: Websites that are purely static (HTML, CSS, JavaScript) can be served from S3 directly.
## Evolution of Backend System Architectures
### Start with Monolith
Most companies and/or projects start with a monolith design for the same benefits mentioned earlier:
- Simplicity
- Consistency
- Efficiency
### Split to Services
After a company or project grows there begins the need to "split out" functional logic into units for the following benefits:
- Independent Development
- Independent Deployment
- Fault Isolation
### Save with Serverless
After starting with a monolith, then separating logic with services, a system can further benefit from serverless for:
- No infrastructure management (saves operations costs)
- Costs based on usage (saves resource costs)
- Automated scaling (saves scaling challenges)
## Who uses Backend System Architectures?
### Amazon
Utilizes a microservices architecture to manage its vast operations. Each microservice functions independently, facilitating teams to modify their services without affecting others. This approach has propelled Amazon's scalability and innovation.
![Amazon](https://github.com/ALT-F4-LLC/fem-bsda-presentation/raw/main/aws-intelligent-supply-chain-retail.png)
### Netflix
Netflix employs a hybrid of serverless and microservices architectures to provide streaming services to millions of users globally. This architecture enables Netflix to manage enormous traffic surges and deliver a smooth user experience.
![Netflix](https://github.com/ALT-F4-LLC/fem-bsda-presentation/raw/main/netflix-streaming.webp)
#### Netflix Cosmos
Cosmos is a computing platform that combines the best aspects of microservices with asynchronous workflows and serverless functions.
### Google
Google's search backend engine uses concepts such as monolithic architecture, service-oriented architecture (SOA), and microservices, each of which represents stages in the evolution of backend architectures.
### Uber
As it expanded worldwide, Uber transitioned from a monolithic to a microservices architecture. Each service, such as ride-hailing, food delivery, and driver-partner services, is managed by a separate microservice. This has enabled Uber to scale and customize its services for different markets.
![Uber](https://github.com/ALT-F4-LLC/fem-bsda-presentation/raw/main/uber.avif)
## Conclusions
### Importance of Systematic Planning
Effective architecture design is foundational to project success. Emphasize the necessity of thorough planning and understanding of both stakeholder needs and technological capabilities before initiating development.
- This ensures alignment with business objectives and user requirements, optimizing both functionality and efficiency.
### Flexibility and Scalability are Key
In the ever-evolving landscape of technology and business, designing architecture with flexibility and scalability at its core is crucial.
- These allow a system to adapt to changes in user demand and technological advancements without requiring complete redesigns, thereby saving time, resources, and facilitating sustained growth.
### Continuous Learning and Adaptation
The field of architecture design is dynamic, with new challenges and solutions emerging regularly. Embrace a mindset of continuous learning and openness to innovation.
- This approach will not only keep your skills relevant but also enable you to design systems that are robust, adaptable, and forward-thinking.

