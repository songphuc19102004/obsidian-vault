#### Software Design Notation
-> Graphical or texture

### PSM (Platform-specific model)
-> mapped to a precise platform

### What is a software architecture
-> Overall structure of a software system

### Two kinds of UML interaction diagram
-> Sequence diagram and communication diagram
(Interaction -> communication; sequence also interact with multiple entities)

### Deployment diagram depict (show)
-> In terms of physical nodes

### Interaction Diagram depict (show)
-> Objects and messages

### Goal of software validation
-> System right
(Verification -> Right system)

### White box testing
-> Test with knowledge

### Evolutionary prototyping
-> Incremental development (Evolution = tien hoa = incremental)

### Interface of a class
-> specification of operations provided by the class

### Data abstraction
-> Encapsulating data so that its structure is hidden

### What is carried out during design modeling
-> Developing software architecture (design = architecture)

### What is carried out during analysis modeling
-> Developing static and dynamic models (analysis = static and dynamic)

### What is carried out during requirement modeling
-> in terms of actors and use cases

### What is a use case
-> sequence of interactions between the user and the system

### What is an alternative sequence in a use case
-> sequence that is different from the main sequence

![[Pasted image 20250403180459.png]]
![[Pasted image 20250403180510.png]]

![[Pasted image 20250403194732.png]]
![[Pasted image 20250403194737.png]]

![[Pasted image 20250403195244.png]]

![[Pasted image 20250403195821.png]]

![[Pasted image 20250403202250.png]]

![[Pasted image 20250404033506.png]]

client ***knows the service required but not the location***, are referred to as ***white page brokering***. A different brokering pattern is ***yellow page brokering***, analogous to the yellow pages of the telephone directory, in which the client ***knows the type of service required but not the specific service.***

connector join: required -> provided
delegation connector join: provided -> inner
a control task -> a task that executes a statechart
centralized control -> overall control and sequencing of the system.
Distributed control -> divided
hierarchical control -> coordinating

not a case of even synchronization -> user event

optional use case: required by some product line members but not others

how are feature conditions used in a SPL state machine -> to identify if a feature is selected or not in the state machine

testability -> before deployment
reusability -> software (not implementation) is capable of being reused