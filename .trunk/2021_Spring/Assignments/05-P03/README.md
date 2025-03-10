## Cpu Scheduling - Simulation
#### Due: 04-07-2021 (Wednesday @ 2:30 p.m.)

Cpu scheduling is a classic and contemporary computer science problem that started when early computers had processors that remained idle much of the time. The goal initially was to get multiple programs loaded into memory, so they could run back to back. This was still inefficient and needed improvement.  That soon evolved into multiple programs loaded into memory and when one process blocked itself, the cpu would work on another available process (multi-programming). This, of course, kept evolving into our multi-threaded / multi-processor world. Yet, we still need to be cognizant of keeping the cpu(s) busy! So what does a scheduler do?

### Scheduler

A scheduler makes choices in order to minimize or maximize a set of criteria, where the criteria really can be simplified into this one concept: "minimize the time any process is doing nothing". However we measure that, or label it, its the crux of the scheduling problem.

#### Goals

1. Maximizing throughput (the total amount of work completed per time unit)
2. Minimizing wait time (time from work becoming ready until the first point it begins execution) (We define it slightly differently)
3. Minimizing latency or response time (time from work becoming ready until it is finished)
4. maximizing fairness (equal CPU time to each process, or more generally appropriate times according to the priority and workload of each process).
   
- In practice, these goals often conflict (e.g. throughput versus latency), thus a scheduler will implement a suitable compromise. 
- Preference is measured by any one of the concerns mentioned above, depending upon the user's needs and objectives.

The scheduler will attempt to accomplish the above goals by moving a process around through a series of states. Of course each process has its own unique set of needs (IO intensive, CPU intensive for example), and the scheduler cannot change what the process needs or when it needs it. Its power is in determining when a process gets access to a resource, the most important of which is the CPU. If it's smart about the order in which it allows access, then the above goals can be met. 

As I mentioned, the scheduler moves processes around through a series of states, based on the processes needs. Our simulation will be implementing the most basic set of states, in which there are five (see below). 

### Job State
<center>
<img src="https://cs.msutexas.edu/~griffin/zcloud/zcloud-files/job_process_state_diagram.png" width="800">
</center>

- NEW - The process is being created, and has not yet begun executing.
- READY - The process is ready to execute, and is waiting to be scheduled on a CPU in the "ready queue".
- RUNNING - The process is currently executing on a CPU.
- WAITING (BLOCKED) - The process has temporarily stopped executing, and is waiting on an I/O request to complete.
- TERMINATED - The process has completed.

## Scheduling Algorithms

#### First-Come First-Serve Scheduling, FCFS

- FCFS is very simple - FIFO simply queues processes in the order that they arrive in the ready queue.
- This is the simplest scheduling algorithm. 
- This is a **non-preemptive** scheduling algorithm.
- Context switches only occur upon cpu burst termination.

#### Shortest-Job-First Scheduling, SJF

- Shortest job first (SJF) also known as Shortest job next (SJN) or shortest process next (SPN), is a scheduling policy that selects for execution the waiting process with the smallest execution time.
- SJF is a **non-preemptive** algorithm
- A disadvantage of using shortest job next is that the total execution time of a job must be known before execution. But we know, so we can implement it.
- Total Execution Time = Sum(cpuBurst<sub>**1**</sub>+cpuBurst<sub>**2**</sub>+cpuBurst<sub>**3**</sub>+...+cpuBurst<sub>**n**</sub>)

#### Shortest-Remaining-Time Scheduling, SRT

- Shortest remaining time, also known as shortest remaining time first (SRTF), is a scheduling method that is a **preemptive version** of shortest job next scheduling. 
- In this scheduling algorithm, the process with the smallest amount of time remaining until completion is selected to execute. 
- Since the currently executing process is the one with the shortest amount of time remaining by definition, and since that time should only reduce as execution progresses, the process will either run until it completes or get preempted if a new process is added that requires a smaller amount of time.
- Execution Time Remaining = Sum of remaining cpuBursts. 

#### Priority Scheduling, RR

- The operating system assigns a fixed priority rank to every process, and the scheduler arranges the processes in the ready queue in order of their priority. 
- Lower-priority processes get interrupted by incoming higher-priority processes.
- This is a **preemptive scheduling** algorithm.
- Runs into possibility of starving processes with low priorities.

#### Round Robin Scheduling

- The scheduler assigns a fixed time unit per process known as a time-slice or time-quantum, and cycles through each process equally. 
- If the process completes within that time-slice it gets terminated otherwise it is rescheduled after giving a chance to all other processes.
- This is a **preemptive scheduling** algorithm.

#### Multiple-Processor Scheduling
- When multiple processors are available, then the scheduling gets more complicated, because now there is more than one CPU which must be kept busy and in effective use at all times.
- Load sharing revolves around balancing the load between multiple processors.
- Multi-processor systems may be **heterogeneous**, ( different kinds of CPUs ), or **homogenous**, ( all the same kind of CPU ). Even in the latter case there may be special scheduling constraints, such as devices which are connected via a private bus to only one of the CPUs. This book will restrict its discussion to homogenous systems.
- Approaches to Multiple-Processor Scheduling
  - One approach to multi-processor scheduling is **asymmetric multiprocessing**, in which one processor is the master, controlling all activities and running all kernel code, while the other runs only user code. This approach is relatively simple, as there is no need to share critical system data.
  - Another approach is **symmetric multiprocessing**, **SMP**, where each processor schedules its own jobs, either from a common ready queue or from separate ready queues for each processor.

### Requirements

- This program is not a true system program, it is just a typical user application that requires no spawning of processes, no timer interrupt handling, no I/O interrupt handling, etc. It is a **simulation**
- Your program must use some form of "visual presentation" to show, at least, the following four components:
  - A CPU
  - A ready queue showing all the processes waiting to be dispatched to use the CPU
  - An I/O device
  - An I/O queue showing all the processes waiting to use the I/O device
- Your choice of "visual presentation" can be either a text-mode "ASCII art" using something NCurses or a GUI program with something like [DearPyGui](https://github.com/hoffstadt/DearPyGui)
  
- The description of a simulated process includes the following information:
  - Arrival time (t<sub>a</sub>) of the process
  - Process ID
  - Number of CPU bursts (N)
  - CPU burst durations (c<sub>i</sub>, i = 1, 2, ..., N), 
  - I/O burst durations (d<sub>j</sub>, j = 1, 2, ..., N-1)
 - This will all be written in one line of the input file (the name of the input file will be passed as the first command-line argument to your program) in the following order:
    - t<sub>a</sub> N c<sub>1</sub> d<sub>1</sub> c<sub>2</sub> d<sub>2</sub> ... c<sub>N-1</sub> d<sub>N-1</sub> c<sub>N</sub>
- A process always begins and ends with a CPU burst. 
- All the numbers are integers.
- A Time quantum (integer) used in the Round Robin simulation is given as the second command-line parameter.
- The simulator shall print an appropriate message when a simulated process changes its state. 
- We are using the 5-state model (New, Ready, Running, Waiting, Terminated). 
- For instance, it shall print a message when it performs one of the following actions:
  - Starts a new process
  - Schedules a process to run
  - Moves a process to the I/O Waiting (Blocked) State
  - Preempts a process from the Running State
  - Moves a process back into the Ready State (due to I/O completion)
  - Starts servicing a process' I/O request
- Each message shall be prefixed with the current simulation time.
- When a simulated process is interrupted (because its current CPU burst is longer than the quantum) the process is preempted and re-enters the ready queue
- When a simulated process completes its current CPU burst, it will then use its I/O burst, the simulator change the process' state to Blocked. At this point, the CPU becomes idle and the dispatcher may select another process from the ready queues.
- The simulated system can have more than one CPU and more than one I/O device (we will discuss this in class). The I/O request of a process will be performed only if the I/O device is available. Otherwise, the process requesting the I/O operation will have to wait until the device is available. I/O is handled by the simulated device on first-come-first-serve basis.
- Upon completion of its I/O burst, a process will change from Blocked state to Ready and join the Ready Queue again.
- A process entering the ready queue can be one of the following:
  - a new process,
  - a process returning from Blocked state, or
  - a process preempted from the CPU
- When these three events happen at the same time, the new process will enter the Ready Q first, followed by process returning from Blocked state, and finally by the preempted process.
- When a simulated process terminates, the simulator then outputs a statement of the form:
  - Job %d terminated: TAT = %d, Wait time = %d, I/O wait = %d
- where 
  - "TAT" is Turn Around Time, 
  - "Wait time" is the total time spent by a process in the Ready Queue, 
  - "I/O wait" is the total amount of time the process had to wait for the I/O device.
- At the end of simulation, the simulator shall display the percentage of CPU utilization, average TAT, average wait time, and average I/O wait time.


- Sources:
  - https://en.wikipedia.org/wiki/Scheduling_(computing)