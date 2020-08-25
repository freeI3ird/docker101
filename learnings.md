# Docker

### Important Links
1. Official Tutorial: https://docs.docker.com/get-started/


### Concepts
https://docs.docker.com/engine/reference/builder/
### Docker Image
1. Docker image captures the private filesystem that your containerized processes will run in and other things that you mention in the Docker File.
2. Docker Image is build from a Docker File.
3. Every Instruction in the Docker File except `CMD`, runs in a separate image(we call it layer) then instruction is executed and layer is committed with the previous image.
4. All the packages that we install or the Directory structure we create will be build in the image(things actually created in the image, if any package is installed in any instruction than that package is installed in the image)
5. All the commands in DockerFile except `CMD` are used to build image.
   1. `CMD` instruction is run on Container startup from image.
   2. There can be only one `CMD` instruction.
6. Do not confuse RUN with CMD. RUN actually runs a command and commits the result; CMD does not execute anything at build time, but specifies the intended command for the image(command which runs when image is run as container).

### Common Commands
1. Build an image from Docker file
   1. `docker build --tag <image-name>:<tag> --file <path/to/DockerFile`
2. List docker images
   1. `docker images`
3. Run Container from the image
   1. `docker run -p <local-port>:<container-port> --name <container-name> -d <image-name> `
      1. `-d` detach mode, running the container in background
      2. `-p` port forwarding
4. List docker processes(containers)
   1. `docker ps -a`
5. Stop, Start, Remove Container
   1. `docker start <container-name>`
   2. `docker stop <container-name>`
   3. `docker rm <container-name>`
6. Docker Logs
   1. `docker logs --follow <container-name>`
      1. Show the logs container producing in detached state.
7. Push images to docker hub
   1. `docker push <docker-id>/<image-name>:<tag>`
8. Tag an image with docker id
   1. `docker tag <image-name> <docker-id>/<image-name>`
9. Pull the image
   1. `docker pull <docker-id>/<image-name>:<tag>`
10. Remove image from local
   1. `docker rmi <docker-id>/<image-name>:<tag>`

### docker-compose
WIP

### General
1. Logical view
   1. Refers to a user's view of the way data or systems are organized. The opposite of logical is physical, which refers to the real organization of a system. For example, a logical description of a file is that it is a collection of data stored together. This is the way files appear to users. Physically, however, a single file can be divided into many pieces scattered across a disk.

2. shim: In computer programming, a shim is a library that transparently intercepts API calls and changes the arguments passed, handles the operation itself or redirects the operation


# Cloud Terms and Technologies

1. Monolithic Application(Monolith)
   1. A large system which is build as a single application.
   2. Different components and layers of this application are tightly coupled and can't be separated.
   3. It runs on a single system. As application grows cost of infrastructure and resources increases, we need a system with more memory, storage and computing power.
   4. Difficult to port such application.
   5. When a new feature is added, testing, re-compile, re-start of all system/application is to be done which take times.
   6. The complete application need to be down when fixing something or adding new feature, potentially affecting the business of organisation.
   7. Can't take advantage of modern Technologies like cloud.
2. Microservices
   1. Instead of building system as a large single application, now system is composed of several Microservices which communicate with each other through network via API calls.
   2. A Microservice is a light-weight( requires less resources), easly portable application that runs in an isolated environment with its all libraries and dependencies.
   3. It is packaged together with its dependencies.
   3. It doesn't interfare with rest of system.
   4. Need of isolated environment ?
      1. If we run two microservice applications on a single system then two applications can have different dependencies, which can interfare with each other and running a single application on single machine/server is not economical, that's why need isolated environment.
   5. How to achieve isolated environment.
3. Containers
   1. Container provides a isolated environment which encapsulates the application and its library & dependencies, wwithout interference from other running applications.
   2. Container is basically a process.
   3. It is best suited for Microservices.
4. Container Images
   1. Images bundle the application code, libraries and dependencies in a pre-defined format.
   2. These images are used to create real containers.
5. Orchestration
   1. It means automating any work-flow such as deployment, scaling, management which includes multiple-steps.
6. Container Orchestration
   1. Automating the deployment,management and scaling of the containerized applications on a cluster.
7. Container orchestrator
   1. It is controller/management unit/tool, which automates the deployment, management & scaling of the containerized applications on a Cluster.
   2. Kubernetes is example of container orchestrator

## Kubernetes
1. Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

## Kubernetes Architecture
1. Master Node
   1. Control plane
      1. It manages the state of Kubernetes cluster
      2. Responsible for all operations in the cluster(destroying container, starting container, saving state, intercepting user requests etc)
      3. Components
         1. api-server
            - It coordinates all administrative tasks
            - It intercept RESTful calls from users or external agents and process them.
            - To process request, it connects with 'etcd' to get the state of cluster and after the execution of request, save the state back to 'etcd'.
            - It is the only component which can connect with 'etcd'. Other agents get cluster configuration data from etcd via api-server.
         2. scheduler
            - In the user request, new requirement came for starting some new containers. So scheduler collects Kubernetes cluster state info, resource usage of its worker nodes (from etcd using api-server ) and takes into account the requirements that came and efficiently assign new objects (objects = pods ==> collection of containers).
         3. Controller Managers
            1. It run controllers to manage/regulate the state of Kubernetes cluster.
               - Controllers are watch-loops continously running and comparing the desired state with the current state of cluster. In case of mismatch, corrective actions are taken until both are not matched.
               - Desired state: Provided all the requested objects' configuration data.
               - Current state: Obtained from etcd
               - State = No. of nodes currently active, no. of pods active.
            2. Kube-controller-manager
               - It runs the controllers responsible to act when a node becomes unavailable, to ensure pod counts are as expected.
               - It creates end-points, API access token.
            3. Cloud-controller-manager
               - It runs the controllers responsible to interact with the underlying infrastructure of the cloud service provider when a node becomes unavailable.
               - Manage Load balancing and routing **( How ??)**
         4. etcd
            1. Distributed Key-value store
            2. Used to persist Kubernetes cluster state and to store the configuration details.
            3. Can be stacked on master node or run on separate cluster.
            4. Only api-server can contact it.
      4. Control plane must be running all the time, its failure can cause loss in business( Failure of control plane --> no one managing the state of cluster --> If some containers/Pod failed --> service become down --> loss in business).
      5. To provide resiliency(recover from difficult situation) to control plane, replicas of master node are created and different components of control plane remain in sync.
      6. At one time only one master node is in control, in case of failure other take control.
2. Kubernetes Terms  
   1. Client Applications: They are Containerized Microservices.
   2. Pods
      1. Logical collection of one or more containers that are scheduled together.
      2. Pods are scheduled on worker nodes.
      3. Pod is the smallest scheduling unit in Kubernetes.
2. Worker Node
   1. Pods are scheduled on worker nodes. On worker nodes Pods(logical collection of containers) get the memory,compute and storage resources.
   2. Components
      1. Container runtime
         1. Kubernetes itself don't have capability to handle containers. It requires container runtime to run and manage container life-cycle.
         2. Kubernetes support many container-runtime
            - Docker, containerd etc
      2. Kubeletes
         1. ![Name](./CRI.png)
         2. **Kubelet as Agent**, it communicates with master node and control plane components running on master nodes.
         3. It recieves pod definition(which group of containers to run) from api-server, then it connects with container-runtime to run the actual containers associated with Pod.
         4. It also monitors the health of Pods'.
         5. **CRI** Container Runtime Interface
            - Act as interface between Kubelet and container runtime.
            - CRI shim act as *grpc server* and Kubelet as *grpc client*
            - CRI shim runs 2 services
              - ImageService: Image related operations
              - RuntimeService: Pod related operations
      3. Kube-proxy
         1. **Kube-proxy a Network Agent** runs on each node responsible for dynamic updates and maintenance of all networking rules on the node.
         2. It abstracts the details of Pods networking and forwards connection requests to Pods.
      4. Addons
3. etcd: Distributed key-value store
   1. All cluster configuration data/cluster state data/cluster related data is stored in etcd.
   2. No client related data is stored.
   3. It can be configured alongside the control plane in the master nodes or in a separate external cluster.
4.
