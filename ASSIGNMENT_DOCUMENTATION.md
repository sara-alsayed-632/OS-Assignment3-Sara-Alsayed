# Assignment 3 - Complete Documentation

**Student Name**: [Sara Mohamed Alsayed]  
**Student ID**: [444052896]  
**Date Submitted**: [7 may]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [(https://drive.google.com/file/d/1ytTZGgQnN4-kbAYseYGAXhy5KOq3A9JL/view?usp=sharing)]

**Video filename**: `[444052896]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 1, 2026 – 6:30 PM]
**What I implemented**: 
I started by setting up the development environment for the assignment. I created a GitHub repository, made it public, and renamed it according to the assignment instructions. Then, I opened the project in Visual Studio Code and initialized a local Git repository. I also connected the project to my GitHub repository and made my first commit after setting my student ID in the code.
**Challenges encountered**: 
I had some difficulty understanding how to connect Visual Studio Code with GitHub and how to push changes correctly.
**How I solved it**: 
I followed the assignment instructions step by step and used Git commands to link my local project to the remote repository.
**Testing approach**: 
I verified the setup by making a small change and pushing it to GitHub, then checked that the change appeared in my repository online.
**Time spent**: 
2 hours
---

### Entry 2 - [May 2, 2026 – 7 PM]
**What I implemented**: 
I started by understanding the given code and identifying the shared resources such as counters and execution log. I analyzed where race conditions might occur.
**Challenges encountered**: 
It was difficult to understand how multiple threads access shared variables at the same time.
**How I solved it**: 
I reviewed the concept of race conditions and checked each method that modifies shared data.
**Testing approach**: 
I ran the program without any synchronization to observe inconsistent behavior.
**Time spent**: 
1 hours
---

### Entry 3 - [ May 3, 2026 – 8:00 PM]
**What I implemented**: 
I added a ReentrantLock to protect shared counters like contextSwitchCount, completedProcessCount, and totalWaitingTime.
**Challenges encountered**: 
I was not sure where exactly to place the lock and unlock statements.
**How I solved it**: 
I used try-finally blocks to ensure locks are always released.
**Testing approach**: 
I tested by running the program multiple times and checking if values remain consistent.
**Time spent**: 
2.5 hours
---

### Entry 4 - [May 4, 2026 – 9 PM]
**What I implemented**: 
I added synchronization to the execution log (ArrayList) using the same lock.
**Challenges encountered**: 
I learned that ArrayList is not thread-safe and may cause errors.
**How I solved it**: 
I wrapped the log operation inside a lock to prevent concurrent modification.
**Testing approach**: 
I tested for ConcurrentModificationException and ensured it does not occur.
**Time spent**: 
1.5 hours
---

### Entry 5 - [May 5, 2026 – 9:00 PM]
**What I implemented**: 
I added a Semaphore to control CPU access and limit execution to one process at a time.
**Challenges encountered**: 
Understanding how semaphore permits work was confusing at first.
**How I solved it**: 
I used a binary semaphore (1 permit) to simulate a single CPU.
**Testing approach**: 
I verified that only one process runs at a time.
**Time spent**: 
2 hours
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[One race condition occurs in the contextSwitchCount variable, where multiple threads increment the counter simultaneously. Since increment is not atomic, two threads may read the same value and overwrite each other’s update. This results in an incorrect total count. Another race condition occurs in the executionLog list. Multiple threads may try to add elements to the ArrayList at the same time, which can lead to inconsistent state or even ConcurrentModificationException. These problems occur because shared resources are accessed without synchronization.
Code Example:
public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}
]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock is used to provide mutual exclusion, meaning only one thread can access a critical section at a time. It is mainly used to protect shared resources from race conditions. In this assignment, I used ReentrantLock to protect the shared counters and the execution log.
SharedResources.cpuSemaphore.release();
A Semaphore is different because it controls access to a limited number of resources using permits. Instead of locking a single critical section, it limits how many threads can execute simultaneously. In my implementation, I used a binary semaphore (Semaphore(1)) to simulate CPU scheduling, allowing only one process to execute at a time.
public static final Semaphore cpuSemaphore = new Semaphore(1);
SharedResources.cpuSemaphore.acquire();

]

---

### Question 3: Deadlock Prevention
**Q**:  What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock occurs when two or more threads wait indefinitely for resources held by each other, causing the program to stop progressing. One prevention technique is using try-finally blocks to guarantee that locks and semaphores are always released, even if an exception occurs. I implemented this approach throughout the assignment to prevent resources from remaining locked accidentally.

Another prevention technique is minimizing the duration of critical sections and avoiding nested locks whenever possible. In my implementation, each counter uses its own independent lock, which reduces lock contention and lowers the risk of deadlocks. I also ensured that every semaphore acquisition has a matching release operation.
code example
contextSwitchLock.lock();
try {
    contextSwitchCount++;
} finally {
    contextSwitchLock.unlock();
}]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[For Task 1, I chose the fine-grained locking approach by using separate locks for each counter variable. I created individual locks for contextSwitchCount, completedProcessCount, and totalWaitingTime. I selected this approach because the counters are independent resources, so multiple threads should be able to update different counters simultaneously without blocking each other.

The main advantage of fine-grained locking is improved concurrency and better performance because threads experience less waiting time. In contrast, coarse-grained locking uses a single lock for all counters, which is simpler but creates unnecessary contention. For example, a thread updating completedProcessCount would block another thread updating contextSwitchCount even though the operations are unrelated.

The trade-off is that fine-grained locking requires more code and slightly increases implementation complexity. However, since the counters are independent, fine-grained locking provides better scalability and demonstrates a stronger understanding of synchronization concepts.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime
**Why they need protection**: 
They are shared among multiple threads and can be updated simultaneously.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
// Paste your implementation here
lock.lock();
try {
    contextSwitchCount++;
} finally {
    lock.unlock();
}
```

**Justification**: 
Ensures only one thread updates the counters at a time, preventing incorrect values.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList)
**Why it needs protection**: 
ArrayList is not thread-safe and concurrent access can cause errors.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
// Paste your implementation here
lock.lock();
try {
    executionLog.add(message);
} finally {
    lock.unlock();
}
```

**Justification**: 
Prevents concurrent modification and ensures data consistency.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To control how many processes can access the CPU at the same time
**Number of permits and why**: 
1 permit to simulate a single CPU
**Where implemented**: 
Inside run() and runToCompletion() methods
**Code snippet**:
```java
// Paste your implementation here
cpuSemaphore.acquire();
try {
    // process execution
} finally {
    cpuSemaphore.release();
}
```

**Effect on program behavior**: 
Ensures that only one process executes at a time, preventing overlap.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
Ran the program 5 times using:
java SchedulerSimulationSync
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
All runs produced consistent values for counters and waiting time.

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
 Without synchronization, shared variables may be updated incorrectly due to race conditions.

**Conclusion**: 
Synchronization ensures correct and consistent results.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
Tested execution log operations under concurrent threads.
**Results**: 
No ConcurrentModificationException occurred.
**What this proves**: 
Synchronization successfully protects shared data.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Correct counts of completed processes and context switches
**Actual values**: 
Matched expected values
**Analysis**: 
The synchronization mechanisms ensured accurate results.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
Different time quantum values
**Purpose**: 
To test scheduler behavior under different conditions
**Results**: 
Program behaved correctly in all scenarios
**What I learned**: 
Synchronization works regardless of scheduling variations.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[I learned that synchronization is essential in multithreaded programs to avoid race conditions. Shared variables must be protected to ensure data consistency. I understood how ReentrantLock provides mutual exclusion and how semaphores control resource access. Using try-finally is critical to avoid deadlocks. I also learned the importance of testing concurrent programs multiple times. Debugging synchronization issues requires careful observation. Overall, this assignment improved my understanding of thread safety.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems where multiple users access and update account balances
**Example 2**: 
Operating systems managing processes and CPU scheduling
---

### How I would explain synchronization to others:

[Synchronization is like allowing only one person to use a shared resource at a time to avoid conflicts. For example, if multiple people try to write on the same paper simultaneously, the result will be messy. Locks act like a key that only one person can hold at a time. Semaphores act like a ticket system that limits how many people can enter a room. This ensures everything works correctly without errors.]

---

## Part 6: GitHub Repository Information

**Repository URL**: 
https://github.com/sara-alsayed-632/OS-Assignment3-Sara-Alsayed
**Number of commits**: 
6
**Commit messages**: 
1. Set my student ID: 444052896
2. Task 1 (444052896): Added ReentrantLock for counter protection
3. Task 2 (444052896): Added ReentrantLock for execution log
4. Task 3 (444052896): Implemented semaphore for CPU control

---

## Summary

**Total time spent on assignment**: 
8 h
**Key takeaways**: 
1. Importance of synchronization
2. Preventing race conditions
3. Safe multithreading practices

**Most challenging aspect**: 
Understanding how threads interact with shared data
**What I'm most proud of**: 
Successfully implementing synchronization and achieving consistent results
---

**End of Documentation**
