## **Processes and Multi Processing System**

- A **process** has a ==**self-contained execution environment**==
- A process generally has a ==**complete, private set of basic run-time resources**==; in particular, ==**each process has its own memory space**==
- ==**Multi**== **Processing/** ==**Multi**== **Tasking System: System allows** ==**many processes executing concurrently**==
- A ==**thread**== is a ==**path of execution**== ==**within**== an ==**executable application**==
- By implementing ==**additional threads**==, we can ==**build more responsive**== (_but ==**not necessarily faster executing**==_) applications

  

## **Concurrency in Operating System**

- Concurrency is the ==**execution of the multiple instruction sequences at the same time**==. It happens in the operating system when there are ==**several process threads running in parallel**==
- The running process threads always ==**communicate with each other**== through ==**shared memory or message passing.**== Concurrency results in ==**sharing of resources**== result in problems like ==**deadlocks and resources starvation**==
- Concurrency helps in techniques like ==**coordinating execution**== of processes, ==**memory allocation**== and ==**execution scheduling**== for maximizing throughput

  

### **Advantages of Concurrency**

- ==**Running of multiple applications**==: It enable to run ==**multiple applications**== at the ==**same time**==
- ==**Better resource utilization**==: It enables that the resources that are unused by one application can be ==**used for other applications**==
- ==**Better average response time**==: Without concurrency, each application ==**has to be run to completion before the next one can be run**==
- ==**Better performance**==: It enables the ==**better performance**== by the operating system. When one application uses only the processor and another application uses only the disk drive then the time to run both applications concurrently to completion will be ==**shorter**== than the time to ==**run each application consecutively**==

  

### **Issues of Concurrency**

- ==**Non-atomic(nhiều thread truy cập cùng thời điểm có thể thay đổi thuộc tính, không có cơ chế nào để bảo vệ thuộc tính)**====:== Operations that are non-atomic but interruptible by multiple processes can cause problems
- ==**Race conditions**==: A race condition occurs of the outcome depends on which of several processes gets to a point first
- ==**Blocking**==: Processes can block waiting for resources. A process could be blocked for long period of time waiting for input from a terminal. If the process is required to periodically update some data, this would be very undesirable
- ==**Starvation**====:== It occurs when a process does not obtain service to progress
- ==**Deadlock**==: It occurs when two processes are blocked and hence neither can proceed to execute

  

## **.NET Application Domains**

- Under the .NET ==**executables are not hosted directly within a Windows**== process that executables are hosted by a ==**logical partition**== within a process called an ==**Application Domain**==
- There are several benefits as follows :
    - AppDomains are a ==**key aspect of the OS-neutral nature of the .NET Core platform**==, given that this logical division abstracts away the differences in how an underlying OS represents a loaded executable
    - AppDomains are ==**far less expensive in terms of processing power and memory than a full-blown process**==. Thus, the CoreCLR is able to load and unload application domains much quicker than a formal process and can drastically improve scalability of server applications
- Under the .NET platform, there is ==**not a direct one-to-one correspondence**== between application domains and threads that a given AppDomain can have numerous threads executing within it at any given time
- To programmatically gain access to the AppDomain that is hosting the current thread, using the static _==**Thread.GetDomain()**==_ method
- A single thread may also be moved into a particular execution context at any given time, and it may be relocated within a new execution context at the whim of the CoreCLR
- The ==**CoreCLR**== is the entity that is ==**in charge of moving threads into (and out of) execution contexts**==

==AppDomain có mục đích tạo một môi trường cách ly nằm bên trong một process, tương tự như các process hoạt động trong cùng một hệ điều hành. Bạn có thể sử dụng AppDomain để nạp các assembly và thực thi các tác vụ một cách riêng biệt trong cùng process. Bài viết này sẽ cho bạn thấy cách thức sử dụng và các lợi ích mà AppDomain đem lại.==

==Trong .NET, một Application Domain (thường được gọi là AppDomain) là một cách để cung cấp cách ly và bảo mật cho các ứng dụng chạy trong cùng một quá trình. AppDomain hoạt động như một container cho code và các tài nguyên, cho phép .NET runtime tải và thực thi mã trong môi trường cách ly, giúp quản lý việc tải và gỡ bỏ các assembly, cũng như cung cấp ranh giới bảo mật giữa các ứng dụng.==

  

## **Interacting with Processes Using .NET**

- The System.Diagnostics namespace defines a ==**number of types**== that allow us to ==**programmatically interact**== with ==**processes**== and various ==**diagnostic-related types**== such as the ==**system event log**== and ==**performance counters**==
- The System.Diagnostics.==**Process**== class allows us to ==**analyze**== the ==**processes running on a given machine**== (==**local**== or ==**remote**==) and also provides members to ==**programmatically start**== and ==**terminate processes**==, ==**view**== (or ==**modify**==) a ==**process’s priority level,**== and obtain a ==**list of active threads**== and/or ==**loaded modules within a given process**==

  

## **System.Threading Namespace**

- The System.Threading namespace provides a ==**number of types**== that enable the ==**direct construction**== of ==**multithreaded**== applications
- It provides types that allow us to interact with a particular ==**CoreCLR**== ==**thread**==, this namespace defines types that allow ==**access to the CoreCLR-maintained ThreadPool**==, a simple ==**(non-GUI-based) Timer class**==, and numerous types used to provide ==**synchronized access**== to ==**shared resources**==

![[/Untitled 133.png|Untitled 133.png]]

Wait/Sleep/Join: These are states where the thread is not actively running but is waiting for some condition:

- Wait: The thread is waiting for a shared resource or for a signal from another thread.
- Sleep: The thread is in a timed waiting state.
- Join: The thread is waiting for another thread to complete.

  

Stop → Start → Running

  

It can be aborted in:

- WSJ State
- Suspended state (Hoãn)
- Running

  

In running state, it can be:

- Suspend
- Abort
- Wait/Sleep/Join

  

In suspended state, it can be:

- Resume
- Or abort

In WSJ State, it can:

  

- Abort
- Or interrupt and continue running

  

## **System.Threading.Thread Class**

- The ==**most primitive**== of all types in the System.Threading namespace is ==**Thread**==
- It represents an ==**object-oriented wrapper**== around a given path of execution within a particular AppDomain
- It also defines several methods (both static and instance level) that allow us to create new threads within the current AppDomain, as well as to suspend, stop, and destroy a particular thread

  

## **Manually Creating Secondary Threads**

Steps to create a thread:

1) Create a method to be the ==**entry point**== for the new thread

2) Create a new ==**ParameterizedThreadStart**== (or ThreadStart) delegate,    passing the address of the method defined in **Step 1** to the constructor

3) Create a ==**Thread object**==, passing the ParameterizedThreadStart/ThreadStart delegate as a constructor argument

4) ==**Establish**== any ==**initial thread**== characteristics (name, priority, etc.)

5) Call the ==**Thread.Start() method**==. This starts the thread at the method referenced by the delegate created in **Step 2** as soon as possible

  

## **Foreground Threads and Background Threads**

- **Foreground threads** have the ability to ==**prevent**== the ==**current application**== from ==**terminating**==. The ==**Core CLR**== will ==**not shut down**== an application (which is to say, unload the hosting AppDomain) ==**until all foreground threads have ended**==
- **Background threads** (sometimes called ==**daemon threads**==) are ==**viewed by the Core CLR as expendable paths of execution**== that ==**can be ignored**== at ==**any point in time**== (even if they are currently laboring over some unit of work)
- Thus, ==**if all foreground threads have terminated**==, any and ==**all background threads**== are ==**automatically killed**== when the application domain ==**unloads(shut down)**==

  

## **Working with the  Timer Callbacks**

- Many applications have the need to ==**call a specific method**== during ==**regular intervals of time**==:
    - ==**Display the current time**== on a status bar via a given helper function.
    - Perform noncritical background tasks such as ==**checking for new e-mail messages**==
- Use the **System.Threading.Timer** type in conjunction with a related delegate named **TimerCallback**

  

## **Working with the ThreadPool**

- A thread pool is a ==**pool of worker threads**== that have ==**already been created**== and are ==**available for apps to use them as needed**==. Once thread pool threads ==**finish**== executing their tasks, they ==**go back to the pool**==
- The thread pool manages threads efficiently by ==**minimizing**== the ==**number of threads**== that must be ==**created, started, and stopped**==
- By using the thread pool, we can ==**focus on our business problem**== rather than the ==**application’s threading infrastructure**==
- The ThreadPool class has several static methods including the ==**QueueUserWorkItem**== that is ==**responsible for calling a thread pool worker thread when it is available**==. If no worker thread is available in the thread pool, ==**it waits until the thread becomes available**==