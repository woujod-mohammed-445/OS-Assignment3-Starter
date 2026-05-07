# Assignment 3 - Complete Documentation

**Student Name**: Woujod Mohammed Alwadee
**Student ID**: 445052707
**Date Submitted**: 7/may/2026

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - May 6, 2026, 8:30 PM
**What I implemented**: 

I updated the student ID in the SchedulerSimulationSync.java file and started understanding the existing multithreaded CPU scheduler code. I also identified the shared variables that may cause race conditions.
**Challenges encountered**: 

At first, I was confused about how multiple threads could access the same variables at the same time and why incorrect values appeared during execution.
**How I solved it**: 
I reviewed the synchronization concepts from the lecture and read the comments inside the starter code carefully to understand where synchronization was needed.
**Testing approach**: 
I ran the program multiple times before adding synchronization and observed inconsistent results in counters and execution logs.
**Time spent**: 1 hour

---

### Entry 2 - [May 7, 2026, 4:00 PM]
**What I implemented**: 
I added ReentrantLock to protect shared counter variables such as contextSwitchCount, completedProcessCount, and totalWaitingTime.
**Challenges encountered**: 
I forgot to unlock the lock in one section, which caused the program to stop responding correctly.
**How I solved it**: 
I used try-finally blocks to ensure the lock is always released even if an exception occurs.
**Testing approach**: 
I executed the program several times and compared the output before and after synchronization to confirm that the counter values became consistent.
**Time spent**: 
2 hours
---

### Entry 3 - [7, 2026, 9:15 PM]
**What I implemented**: 
I implemented synchronization for the execution log using ReentrantLock and added a binary Semaphore to control CPU access between threads.
**Challenges encountered**: 
I initially placed the semaphore acquire/release statements in the wrong location, which reduced thread performance.
**How I solved it**: 
I adjusted the semaphore placement to protect only the critical CPU execution section.
**Testing approach**: 
I tested the scheduler multiple times with different runs and confirmed that no race conditions or ConcurrentModificationException errors appeared.
**Time spent**: 
1.5 hours
---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?
**Your Answer**:

One race condition in the original code occurred when multiple threads updated shared counter variables such as contextSwitchCount and completedProcessCount at the same time. Since the increment operation is not atomic, two threads could read the same value simultaneously and overwrite each other’s updates. This could produce incorrect counter results, such as missing increments or inaccurate statistics.

Another race condition occurred in the shared execution log (ArrayList). Multiple threads attempted to add log entries concurrently without synchronization. Concurrent access to the ArrayList could cause inconsistent log data or even throw a ConcurrentModificationException. For example, two threads writing to the log at the same time might corrupt the internal structure of the list or produce missing log entries.
[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?
**Your Answer**:

ReentrantLock is used for mutual exclusion to protect critical sections of code where shared data is modified. It allows only one thread at a time to access the protected resource and gives more control than the synchronized keyword. In my code, I used ReentrantLock to protect shared counters and the execution log because these resources needed safe updates from multiple threads.

A Semaphore controls access to a limited number of resources using permits. Unlike a lock, a semaphore can allow multiple threads depending on the number of permits. I used a binary semaphore with one permit to control CPU access, ensuring that only one process thread could use the simulated CPU at a time. This matched the real CPU scheduling concept where only one process executes on the CPU simultaneously.
[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
Deadlock is a situation where two or more threads wait forever for resources held by each other, causing the program to stop progressing. This usually happens when locks are acquired in inconsistent ways or not released properly.

One prevention technique is using try-finally blocks to guarantee that locks are always released even if an exception occurs. In my code, I placed every lock() operation inside a try block and released it inside finally.

Another prevention technique is maintaining consistent lock ordering and minimizing the time locks are held. I avoided nested locks whenever possible and kept critical sections short. This reduced the possibility of threads waiting on each other and improved overall program safety.

[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
For Task 1, I used separate locks for each counter variable, which is considered a fine-grained locking approach. I chose this design because the three counters (contextSwitchCount, completedProcessCount, and totalWaitingTime) are independent resources and do not need to be updated together.

Using separate locks improves concurrency because different threads can update different counters simultaneously without blocking each other unnecessarily. For example, one thread can update completedProcessCount while another updates totalWaitingTime.

The alternative approach is coarse-grained locking, where one lock protects all counters together. This approach is simpler to implement and easier to manage, but it reduces concurrency because all threads must wait even when accessing unrelated counters.

Fine-grained locking provides better performance and scalability in this case because the counters are independent. However, it also increases code complexity since multiple locks must be managed carefully to avoid synchronization mistakes.

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, and totalWaitingTime
**Why they need protection**: 
These variables are shared between multiple threads. If two threads update the same variable simultaneously, race conditions may occur and produce incorrect values such as lost updates or inconsistent statistics.
**Synchronization mechanism used**: 
Fine-grained ReentrantLock
**Code snippet**:
public static final ReentrantLock contextSwitchLock = new ReentrantLock();

public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}

**Justification**: 
I used separate locks for each counter because the counters are independent resources. This fine-grained locking approach improves concurrency by allowing different threads to update different counters at the same time without unnecessary blocking.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList<String>)
**Why it needs protection**: 
ArrayList is not thread-safe. Multiple threads adding log entries simultaneously can cause inconsistent log data or ConcurrentModificationException.
**Synchronization mechanism used**: 
ReentrantLock (logLock)
**Code snippet**:
public static final ReentrantLock logLock = new ReentrantLock();

public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}
**Justification**: 
The lock guarantees that only one thread can modify the execution log at a time. This prevents corruption of the ArrayList and ensures all log entries are stored correctly and safely.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
The semaphore controls access to the simulated CPU and ensures that only one process thread executes inside the CPU critical section at a time.
**Number of permits and why**: 
I used a binary semaphore with 1 permit because a CPU can execute only one process at a time in this simulation.
**Where implemented**: 
Inside the run() and runToCompletion() methods of the Process class.
**Code snippet**:
public static final Semaphore cpuSemaphore = new Semaphore(1);

SharedResources.cpuSemaphore.acquire();
try {
    // CPU execution section
} finally {
    SharedResources.cpuSemaphore.release();
}
**Effect on program behavior**: 
The semaphore prevents multiple threads from executing CPU operations simultaneously. This creates controlled CPU access, avoids conflicts between process threads, and makes the scheduling simulation behave more realistically.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

**Results**: 
The program produced stable and correct results in every run. The number of completed processes always matched the total number of generated processes, and the execution log size remained consistent.
**Why synchronization is necessary**: 
Without synchronization, multiple threads could update shared counters simultaneously, causing incorrect totals or lost increments. The shared executionLog could also become corrupted due to concurrent modifications. Synchronization ensures safe access to shared resources and prevents unpredictable thread behavior.
**Conclusion**: 
The synchronization mechanisms successfully prevented race conditions and ensured consistent program execution across multiple runs.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
I repeatedly executed the program while multiple process threads added entries to the shared execution log concurrently.
**Results**: 
No ConcurrentModificationException or log corruption occurred during testing.
**What this proves**: 
This proves that the logLock successfully protected the shared ArrayList from unsafe concurrent access.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Completed processes should equal the total number of generated processes.
Context switch count should increase correctly during process execution.
Waiting time should always be non-negative.
**Actual values**: 
The output matched the expected behavior in all runs. All processes completed successfully and the statistics were calculated correctly.
**Analysis**: 
The synchronization implementation maintained data consistency and ensured accurate statistics throughout program execution.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
Running the scheduler with different randomly generated burst times and priorities.
**Purpose**: 
To verify that synchronization still works correctly under varying execution conditions.
**Results**: 
The scheduler handled all scenarios correctly without deadlocks, race conditions, or inconsistent outputs.
**What I learned**: 
I learned that synchronization is essential in multithreaded applications because thread scheduling changes between executions. Proper locking and semaphore control ensure reliable and predictable behavior.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

Through this assignment, I learned that synchronization is essential in multithreaded programming to prevent race conditions and inconsistent data. I understood how multiple threads can access shared resources simultaneously and cause incorrect program behavior if protection mechanisms are not used. I learned how ReentrantLock provides controlled access to critical sections and why try-finally blocks are important for safely releasing locks. I also learned the difference between locks and semaphores and how semaphores can control access to limited resources such as the CPU. One challenge I faced was understanding where synchronization should be applied without blocking unnecessary parts of the program. I discovered that fine-grained locking improves concurrency because independent resources can be accessed simultaneously. I also learned how synchronization helps avoid exceptions such as ConcurrentModificationException. Overall, this assignment improved my understanding of thread safety and concurrent programming design.
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems use synchronization when multiple users access the same bank account simultaneously. Synchronization prevents incorrect balance updates and ensures transaction consistency.
**Example 2**: 
Operating systems use synchronization in CPU scheduling and memory management to control how multiple processes access shared hardware resources safely.
---

### How I would explain synchronization to others:

I would explain synchronization as a traffic control system for threads. Imagine multiple cars trying to cross a one-lane bridge at the same time. Without traffic rules, cars could crash or block each other. Synchronization works like traffic lights or security gates that allow threads to access shared resources safely and in an organized way.

In Assignment 1, threads could access variables freely, which could lead to incorrect results. In this assignment, we added locks and semaphores to make sure only the correct thread can use a resource at the right time. A ReentrantLock acts like a room key where only one person can enter at a time, while a semaphore acts like a limited number of tickets that control how many threads may access a resource simultaneously.
---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/woujod-mohammed-445/OS-Assignment3-Starter
**Number of commits**: 13 

**Commit messages**: 


1. Set my student id : 445052707
2. Add Semaphore and ReentrantLock imports
3. Implement synchronization with ReentrantLocks
4. Add semaphore for CPU control and context switch method
5. Implement locks for thread safety in critical sections add method
6. Implement CPU semaphore acquisition in run method
7. Release CPU semaphore in finally block
8. Implement CPU semaphore acquisition in runToCompletion
9. Release cpuSemaphore in finally block
10. Update SchedulerSimulationSync.java
11. Update ASSIGNMENT_DOCUMENTATION.md
12. Update GitHub repository URL in documentation
13. Modify GitHub repository information section


---

## Summary

**Total time spent on assignment**: 
Approximately 6–7 hours
**Key takeaways**: 
1. Synchronization is essential for preventing race conditions in multithreaded programs.
2. ReentrantLock and Semaphore provide safe and controlled access to shared resources.
3. Using try-finally blocks is important to prevent deadlocks and ensure locks are always released.

**Most challenging aspect**: 
The most challenging part was understanding where synchronization should be applied without reducing program performance. It was also difficult at first to understand how race conditions happen between threads during concurrent execution.
**What I'm most proud of**: 
I am most proud of successfully implementing fine-grained locking and semaphore synchronization while keeping the program stable and free from race conditions and exceptions. I also improved my understanding of thread safety and concurrent programming concepts through practical implementation.
---

**End of Documentation**
