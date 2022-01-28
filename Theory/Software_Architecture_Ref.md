# Chapter 3

## Study List
- As-designed vs As-implemented architecture
- Prescriptive vs Descriptive Architecture
- Architectural Evolution
- Architectural Degradation: drift and erosion
- Architectural Recover
- Deployment
- Components
- Connectors
- Configurations
- Architectural Styles
- Architectural Patterns
- Three-Tiered Pattern
- Models, Views, Vizualizations
- Stakeholders

## Terms

**Architecture**: a software system's set of principal design decisions. It is the blueprint for a software system's construction and evolution

**Reference Architecture**: a set of principal design decisions that are simultaneously applicable to multiple related systems. It is arachitecture that references previous architectures. This comes up in product family architectures and is used by many companies that sell product lines such as TVs and Computers.

**System Structure**: How the architectural elements are organized and composed

**Principal**: what the stakeholders define as the system goals. It is usually what determines a design decision. Not all principal design decisions are architectural, for example the algorithms and data structures used does not necessarily affect a system's architecture

**Prescriptive Architecture**: captures the design decisions made prior to the system's construction. It is the as-conceived or as-intended architecture

**Descriptive Architecture**: Describes how the system has been built. It is the as-implemented or as-realized architecture

**Architectural Evolution**: The notion that as software evolves its prescriptive architecutre is the first to be modified. In practice the descriptive architecture is often directly modified.

**Architectural Drift**: A type of architectural degradation. It is the insertion of principal design decisions inot a system's descriptive architecture that were not included in the prescriptive architecture but do not violate any of the prescriptive architecture's design decisions

**Architectural Erosion**: Similar to architectural drift, except the prescriptive architecture's design is violated.

**Architectural Recovery**: When architectural drift happens, you may need to recover the sysetm's architecture, that is re-architect your system from its implementation-level artifacts like source code, executables, and class files

**Architectural Perspectives**: A nonempty set of types of architectural design decisions.

**Deployment**: How to deliver software with considerations for how fast it needs to be delivered and what is needed to achieve this.

**Component**: A smaller unit of the software architecture that deals with processing data in the system's architecture. It can be as simple as a few lines of code or as complex as an entire system.

**Connector**: Manages interaction between software components by transmitting and recieving data.

**Configuration**: a set of specific associations between the components and connectors of a software system's architecture.

**Architectural Style**: a named collection of architectural design decisions taht are applicable ina given development context, consttrain architectural design decisions taht are specific to a particular system within that context, and elicit beneficial qualities in each resulting system

**Architectural Pattern**: a set of architectural design decisions that are applicable to a recurring design problem and parameterized to account for different software development context in which that problem appears.

**Three-Tiered Pattern**: the pattern that divides a complex software application into a front tier, middle tier, and back tier. The front-tier is the client side or front-end. The middle-tier is the communication interface that recieves user requests from the front and processes data from the back. The back tier handles all data storage and access and is computationally very powerful.

**Model** an artifact that captures some or all of the design decisions that comprise a system's architecture.

**Visualization**: a way of depicting some or all of the architectural design decisions about a system to a stakeholder

**View**: A subset of related architectural design decisions

# Chapter 4: Creating Architectures

## Study List
- Engineering design process: feasibility, prelim, detail
- Standard, Cyclic, Parallel, Adaptive, Incremental design
- Abstraction and Simple Machines
- Separation of Concerns
- Domain Specific Software Architectures
- Architectural Patterns
- Three-tiered Pattern
- Model View Pattern
- Sense Compute Control
- Architectural Styles
- Style Analysis Dimensions
- Common Styles

## Standard Design
- In many ways, designing software is like desining any other architectural or engineering project.

- A common four-stage design process known as the **stanard model** or the **linear model** is detailed below. It is used in processes such as the waterfall and spiral methods of development and is consistent with the Unified Process, Jackson System Development, and Microsoft Code Complete.

1. Feasibility stage: identifying what needs to be done, how it can be done, if it can be done, and the methods to do it

2. Preliminary stage: commit to one method generated in stage one and develop a design plan

3. Detail stage: start discussing technical details about the design

4. Planning stage: evaluate and alter the design plan to suit the requirements of production, distribution, and consumption.

- It is important to note that this four-step design process is only one of many possible choices for constructing your software's architecture. There are many other philosophies of design including Incremental, Parallel, and Adaptive.

- This four-step method fails in cases where the first step takes too long or is never resolved. Such a detailed planning stage may also be subject to frequent revision and degradation for complex software projects.

## Alternatives
- `Cyclic`: Process can revert to an earlier stage if problems occur at stages 2 or 4

- `Parallel`: After stage 1, multiple independent attempts at design are initiated, usually one or a compromise between the attempts is chosen

- `Adaptive`: Design as you go. Decide whether to move on or not at the end of every stage

- `Incremental`: Each stage is treated as a task of incrementally improving the existing design

## Feasibility Stage
- Exactly *how* do we identify a viable way to carry out our project?

- The answer is through experience, which is why many software architects in companies are considered senior level employees.

## Abstraction
- In software architecture, we typically start up at the conceptual level and gradually work down (ie to the implementation level)

- Simple machines can be seen as foundational units to system design and can be used to help make a first conception of the design. For example, hypertext and composite documents are the `simple machines` of web pages.

## Separation of Concerns
- The subdivision of a problem into individual parts

- For instance, separation of front end and back end in web development. the visual interface for an ATM that the customer interacts with is independent from the logic used to actually process transactions. Another common example is the separatoin of computational classes from connectors (classes that pass data).

- Only rarely can concerns be truly separated however, and there are many tradeoffs to this strategy.

## DSSA
- domain specific software architectures

- it is the combination of a reference architecture, a library of software components, and a method of choosing and configuring components to work within the reference architecture. All of these comprise DSSA which represents the most valuable type of exeprience useful for identifying a feasible design

- DSSAs are only valuable however if one exists for the domain where the engineer is tasked to build a new application

## Architectural Patterns
- An architectural pattern is a set of architectural design decisoins that are applicable to a recurring design problem.

- Patterns are similar to DSSAs but are applied at a lower level with much more narrow scope

- The `three-tier` pattern separates state logic and display. The display is the user interface, the logic is the code that controls the responses to the user inputs, and the state is contained within a database

- The `model-view-controller` is the dominante pattern in GUI applications that separates between information, presentation, and user interaction. In web, the model consists of resources like html files, the view is the HTML rendering agent in the browser, the controller is the code that the browser uses to respond to user interactions.

## Main Program and Subroutines
- One of the oldest styles influenced by the C programming language. You have a main method that delegates user input to other methods

- Difficult to scale large applications and does not make enough use of data structures

## Object Oriented Style
- Models real world objects and his highly flexible. Supports abstraction and secure data manipulation.

- As a style it is not the most useful to understand systems. Objects can be very tighlty coupled when objects call methods on other objects.

## Virtual Machines
- Is a layered style that shows up in networks and OS systems. Data is passed between layers, usually unidirectionally. Layers are only aware of the layers they pass data to.

- Changes in a layer affect at most the adjacent two layers

## Client-Server
- a layered style with two layers. Is suitable for networked programs where centralization of data is required and where processing and data storage benefit from a high-capacity machine.

## Batch-Sequential
- a dataflow style, one of the oldest styles. Used in processing bank transactions in the 80s. Information is handed off sequentially to a factory line of tasks.

## Pipe and Filter
-  another dataflow style that transforms input streams into output streams. Data is filtered as it is passed from method to method. Linux pipe commands using grep are a good example

- Filters do not share state and can be hooked together. They are highly reusable and adaptable and are concurrent friendly.

- Poor for GUI based applications. Also, performance of the system is based on the least efficient filter.

## Blackboard
- Imagine a group of problem solvers sitting around a blackboard that contains a list of problems to solve. All data is held in the blackboard. The blackboard behaves as a central data structure

- programs poll the blackboard through queries or procedure calls. 

- Common in artificial intelligence applications. You do not need a pre-planned solution as problems can be solved as they come and go. Also used in IDEs.

## Rule Based
- similar to blackboard, the central memory is a database that contains facts and production rules which consist of if then clauses over the set of variables. Components are user interface, inference engine, or knowledge based

- useful for when the problem can be understood by repeatedly resolving a set of predicates. Rules and behavior can be easily modified

- a virtual advice or chat bot will run on this type of engine.

- can be difficult to troubleshoot if bad rules are injected into the system. There may also be unprecidented results from rules interacting.

## Interpreter Style
- Interpreter parses and executes input commands, updating the state maintained by the interpreter. Components niclude a command interpreter or user interface

- Highly dynamic behavior possible, wher the set of commands is dynamically modified

- Great for user programmability. Lisp and Scheme follow the interpreter style

- Macros like Microsoft word and excel

- All Java programs are compiled into java bytecode by the JVM. Java bytecode are instructions that are input to the machine. 

## Mobile Code
 - A data element is dynamically transformed into a data processing component. 

 - 

## Publish Subscribe

## Event Based

## P2P



