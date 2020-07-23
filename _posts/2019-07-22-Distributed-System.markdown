---
layout: post
title:  "Distributed System"
description: An introduction of what Distributed System is.
date:   2020-07-22 20:03:36 +0530
---
This post demonstrate the concepts of Distributed System.

### Overview

 In a distributed system, a group of independent computers is presented to the user as a **unified whole**, as if it were a system. The System has a variety of common physical and logical resources, dynamic assignment of tasks, decentralized physical and logical resources through the computer network to achieve information exchange.There is a **distributed operating system** that manages computer resources in a **global manner**. Typically, distributed systems have only one model or paradigm for users. Over the operating system sits a layer of **software middleware** that implements this model. A famous example of a distributed system is the **World Wide Web**, where everything looks like a document (a Web page). 
 
 In computer networks, such uniformity, models, and software do not exist.The user sees the actual machines, and the computer network does not make the machines seem uniform. If these machines have different hardware or different operating systems, then these differences are completely visible to the user.If a user wants to run a program on a remote machine, he must log in to the remote machine and then run the program on that machine.

 The common point between distributed system and computer network system is that **most distributed system is built on computer network**, so distributed system and computer network are basically the same in physical structure.
  
 The difference is that distributed operating systems are designed differently from network operating systems, which determines their **structure, way of working, and function**. Network operating system for Internet users in the use of network resources must first understand the network resources, network users must know the various functions of the computer configuration, software resources, network file structure, etc. In the network, if the user wants to read a Shared file, the user must know the file is in which computer and in which directory; Distributed operating systems manage system resources globally. They can schedule network resources for users at will, and the scheduling process is transparent. When a user submits a job, the distributed operating system can select the most suitable processor in the system according to needs, submit the user's job to the handler, and pass the result to the user after the processor completes the job. During this process, the user is not aware that there are multiple processors, and the system acts like a single processor.

 **Cohesiveness** means that each database distribution node is **highly autonomous** and has a local database management system.Transparency means that each database distribution node is transparent to the user's application, whether it is local or remote. In a distributed database system, the user does not feel that the data is distributed, that is, the user does not have to know whether the relationship is segmented, whether there is a copy, which site the data is stored in and which site the transaction is executed on.

### Classfication

 The architecture of distributed computer system can be described by **coupling degree between processors**. Coupling degree is the close degree of interconnection between system modules, which is a comprehensive reflection of **performance indexes** such as **data transmission rate**, **response time** and **parallel processing capacity**, and it mainly depends on the interconnection topology structure and communication link type of the selected architecture. 

 According to the measurement of coupling degree of geographical environment, distributed system can be divided into intra-organism system, intra-building system, inter-building system and regional system with different geographical range, etc. Their coupling degree is determined from high to low in turn according to the nature of the application field, and can be divided into three categories: 

 1. The first is **distributed parallel computer system** and **distributed multi-user computer system** which are oriented to computing tasks. They require the highest possible coupling degree so that they can be developed to share the work of large computer and time-sharing computer system. 

 2. The second is a **distributed data processing system** for managing information. Coupling degree can be reduced appropriately.

 3. The third type is the **distributed computer control system** for process control.The coupling requirements are moderate, although for some real-time applications, the coupling requirements may be high.

 ### Characteristics

 Distributed system is a **loosely coupled system** composed of *multiple processors* interconnected by communication lines. From the point of view of one processor in the system, the other processors and corresponding resources are remote, and only its own resources are local. Up to now, the definition of distributed system has not formed a unified view.It is generally believed that distributed systems should have the following four characteristics: 

1. **Distribution**. A distributed system consists of multiple computers that are geographically dispersed and can be distributed across a unit, a city, a country, or even on a global scale.The function of the whole system is distributed on each node, so the distributed system has the distribution of data processing.

2. **Autonomy**. Each node in the distributed system contains its own processor and memory, and each node has the function of processing data independently.Usually, they are on an equal footing and can work autonomously and use Shared communication lines to transmit information and coordinate tasks.

3. **Parallelism**. A large task can be divided into subtasks that are executed on different hosts.

4. Overall, In a distributed system, there must be a single, **global process communication mechanism** that allows any process to communicate with other processes, and does not distinguish between local and remote communication. At the same time, there should be a **global protection mechanism**. There is a uniform set of system calls on all machines in the system, which must be adapted to a distributed environment. **Running the same kernel on all Cpus** makes coordination easier.

### Advantages

1. **Resource sharing**. A number of different nodes are interconnected through a communication network, and users on one node can use resources on other nodes. For example, distributed systems allow devices to be Shared, allowing many users to share expensive external devices, such as color printers. Allow data sharing, so that many users access to the common database;You can share remote files, use remote specific hardware devices such as high-speed array processors, and perform other operations.

2. **Speed up calculation**. If a particular computing task can be divided into several sub-tasks running in parallel, the sub-tasks can be spread across different nodes and run simultaneously on these nodes to speed up the computation. In addition, distributed systems have the capability of **computing migration**, *if the load on one node is too heavy, some of the jobs can be moved to other nodes for execution*, thereby reducing the load on that node. This job migration is called **load balancing**.

3. **High reliability**. Distributed systems have high reliability. **If one of the nodes fails, the rest of the nodes can continue to operate without the entire system crashing** because of the failure of one or a few nodes.Therefore, distributed system has a good fault-tolerant performance. 

The system must be able to detect node failures and take appropriate measures to recover from them. Once the system has identified the node where the fault is, it is no longer used to provide service until it returns to normal functioning. If the function of the failed node can be completed by other nodes, the system must ensure the correct implementation of the function transfer. When a failed node is restored or repaired, the system must integrate it smoothly into the system.

4. **Convenient and fast communication**. In a distributed system, the nodes are connected together through a **communication network**. Communication network is composed of communication lines, modems, communication processors, etc. Users of different nodes can exchange information easily. At the low level, systems communicate by passing messages, similar to the messaging mechanism in single-CPU systems. All high-level messaging functions in a single-CPU system, such as file transfer, login, mail, Web browsing, and Remote Procedure Call (RPC), can be implemented in a distributed system.

The distributed system realizes the long-distance communication between nodes, which provides great convenience for the information exchange between people. Users from different regions can jointly complete a project by transmitting project files, remotely logging into the other party's system to run programs, such as sending E-mail, and coordinate their work.

### Disadvantages

Although distributed systems have many advantages, they also have their own disadvantages, a**mainly the lack of available software**, **relatively few system software**, **programming languages**, **applications, and development tools**. In addition, there are issues of communication network saturation or information loss and network security, and convenient data sharing also means that confidential data is **vulnerable to theft**. Although distributed systems have these potential problems, their advantages far outweigh their disadvantages, and they are being overcome. Therefore, distributed system is still the direction of people's research, development and application.

### The Differences Betweem Internet

Distributed computer system has similarities and differences with computer network, and the main similarities and differences are as follows:

1. In a computer network, each user or task usually USES only one computer, and remote registration is required to take advantage of another computer in the network. In a distributed computer system, user processes are **dynamically scheduled** on each computer in the system, and machines are dynamically and transparently assigned to user processes or tasks by the distributed operating system according to their running conditions.

2. In a computer network, users know where their files are stored and transfer the files between machines using the file transfer command displayed.In a distributed computer system, the placement of files is managed by the operating system and users can access all files in the system in the same way regardless of where they are located.

3. In the computer network, each node computer has its own operating system, resources are locally owned and controlled, and process scheduling in the network is realized through process migration and data migration. In a distributed computer system, a **local operating system is run at each field, and the tasks performed may be independent, a part of a task, or (part of) tasks at other fields, and the fields cooperate with each other to balance the load within the system**.

4. In the computer network, the system is almost fault-tolerant. In distributed computer system, there are **automatic system reconstruction**, moderate degraded use and error recovery functions.

5. The degree and level of transparency are different between the two.

6. As far as resource sharing is concerned, computer networks and distributed computer systems are similar.