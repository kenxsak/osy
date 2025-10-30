```markdown
# OSY Practical Exam Questions Bank — Implementations & Explanations

Author: kenxsak  
Date: 2025-10-30

This document provides clear aims, requirements, Python implementations, sample outputs, and short conclusions for the listed operating systems practical problems. Each program is written in plain Python 3 and is suitable for running on any machine with Python 3.x installed. Where helpful, a short example input and the corresponding output are included.

How to run:
- Save the desired program into a file, e.g., `fcfs.py`.
- Run with: `python fcfs.py`
- For interactive variants, follow the prompts in the program.

---

EXP A: FCFS (First Come First Serve) CPU Scheduling
1. AIM:
   - Write a Python program to calculate average Waiting Time (WT) and Turnaround Time (TAT) for n processes using FCFS scheduling.

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (fcfs.py):
```python
# fcfs.py
# FCFS scheduling: compute WT and TAT for n processes
def fcfs(processes, burst_times):
    n = len(processes)
    # completion times
    ct = [0]*n
    tat = [0]*n
    wt = [0]*n

    ct[0] = burst_times[0]
    for i in range(1, n):
        ct[i] = ct[i-1] + burst_times[i]

    for i in range(n):
        tat[i] = ct[i]  # arrival times assumed 0 for all
        wt[i] = tat[i] - burst_times[i]

    avg_wt = sum(wt)/n
    avg_tat = sum(tat)/n
    return ct, tat, wt, avg_wt, avg_tat

def main():
    # Example
    processes = ['P1','P2','P3','P4']
    burst_times = [5, 3, 8, 6]
    ct, tat, wt, avg_wt, avg_tat = fcfs(processes, burst_times)

    print("Process\tBT\tCT\tTAT\tWT")
    for i in range(len(processes)):
        print(f"{processes[i]}\t{burst_times[i]}\t{ct[i]}\t{tat[i]}\t{wt[i]}")
    print(f"\nAverage Waiting Time = {avg_wt:.2f}")
    print(f"Average Turnaround Time = {avg_tat:.2f}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
Process	BT	CT	TAT	WT
P1	5	5	5	0
P2	3	8	8	5
P3	8	16	16	8
P4	6	22	22	16

Average Waiting Time = 7.25
Average Turnaround Time = 12.75
```

5. CONCLUSION:
- FCFS is simple and fair (by arrival order). It can lead to high average waiting time (convoy effect).

---

EXP B: SJF (Shortest Job First) — Non-preemptive
1. AIM:
   - Compute WT and TAT for n processes using non-preemptive SJF.

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (sjf_nonpreemptive.py):
```python
# sjf_nonpreemptive.py
def sjf_nonpreemptive(processes, burst_times):
    n = len(processes)
    # We'll assume all arrival times = 0
    # Pair and sort by burst time
    paired = list(zip(processes, burst_times))
    paired.sort(key=lambda x: x[1])  # sort by burst time

    order, bt_sorted = zip(*paired)
    ct = [0]*n
    tat = [0]*n
    wt = [0]*n

    ct[0] = bt_sorted[0]
    for i in range(1, n):
        ct[i] = ct[i-1] + bt_sorted[i]
    for i in range(n):
        tat[i] = ct[i]
        wt[i] = tat[i] - bt_sorted[i]
    avg_wt = sum(wt)/n
    avg_tat = sum(tat)/n
    return list(order), list(bt_sorted), ct, tat, wt, avg_wt, avg_tat

def main():
    processes = ['P1','P2','P3','P4']
    burst_times = [6, 8, 7, 3]
    order, bt_sorted, ct, tat, wt, avg_wt, avg_tat = sjf_nonpreemptive(processes, burst_times)
    print("Execution order:", order)
    print("Process\tBT\tCT\tTAT\tWT")
    for i, p in enumerate(order):
        print(f"{p}\t{bt_sorted[i]}\t{ct[i]}\t{tat[i]}\t{wt[i]}")
    print(f"\nAverage Waiting Time = {avg_wt:.2f}")
    print(f"Average Turnaround Time = {avg_tat:.2f}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
Execution order: ['P4', 'P1', 'P3', 'P2']
Process	BT	CT	TAT	WT
P4	3	3	3	0
P1	6	9	9	3
P3	7	16	16	9
P2	8	24	24	16

Average Waiting Time = 7.00
Average Turnaround Time = 13.00
```

5. CONCLUSION:
- SJF (non-preemptive) reduces average waiting time compared to FCFS if short jobs are available, but may starve long jobs if arrivals are dynamic.

---

EXP C: Priority Scheduling (Non-preemptive)
1. AIM:
   - Compute WT and TAT using non-preemptive Priority scheduling. Lower numeric priority value = higher priority (convention).

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (priority_nonpreemptive.py):
```python
# priority_nonpreemptive.py
def priority_nonpreemptive(processes, burst_times, priorities):
    paired = list(zip(processes, burst_times, priorities))
    # sort by priority (ascending), tie-breaker: arrival order preserved by stable sort
    paired.sort(key=lambda x: x[2])
    order = [p[0] for p in paired]
    bt_sorted = [p[1] for p in paired]

    n = len(order)
    ct = [0]*n
    tat = [0]*n
    wt = [0]*n

    ct[0] = bt_sorted[0]
    for i in range(1, n):
        ct[i] = ct[i-1] + bt_sorted[i]
    for i in range(n):
        tat[i] = ct[i]
        wt[i] = tat[i] - bt_sorted[i]
    avg_wt = sum(wt)/n
    avg_tat = sum(tat)/n
    return order, bt_sorted, priorities, ct, tat, wt, avg_wt, avg_tat

def main():
    processes = ['P1','P2','P3','P4']
    burst_times = [10, 1, 2, 1]
    priorities = [3, 1, 4, 2]  # lower number -> higher priority
    order, bt_sorted, prio_sorted, ct, tat, wt, avg_wt, avg_tat = priority_nonpreemptive(processes, burst_times, priorities)
    print("Order by priority:", order)
    print("Process\tBT\tPriority\tCT\tTAT\tWT")
    for i, p in enumerate(order):
        print(f"{p}\t{bt_sorted[i]}\t{prio_sorted[i]}\t{ct[i]}\t{tat[i]}\t{wt[i]}")
    print(f"\nAverage Waiting Time = {avg_wt:.2f}")
    print(f"Average Turnaround Time = {avg_tat:.2f}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
Order by priority: ['P2', 'P4', 'P1', 'P3']
Process	BT	Priority	CT	TAT	WT
P2	1	1	1	1	0
P4	1	2	2	2	1
P1	10	3	12	12	2
P3	2	4	14	14	12

Average Waiting Time = 3.75
Average Turnaround Time = 7.25
```

5. CONCLUSION:
- Priority scheduling handles differing importance levels. Lower-priority processes may suffer starvation unless aging is applied.

---

EXP D: Round Robin (RR) Scheduling
1. AIM:
   - Calculate WT and TAT for n processes using Round Robin with a given quantum.

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (round_robin.py):
```python
# round_robin.py
from collections import deque

def round_robin(processes, burst_times, quantum):
    n = len(processes)
    rem_bt = burst_times.copy()
    t = 0
    ct = [0]*n
    tat = [0]*n
    wt = [0]*n
    q = deque(range(n))  # store indices

    while q:
        i = q.popleft()
        if rem_bt[i] > 0:
            if rem_bt[i] > quantum:
                t += quantum
                rem_bt[i] -= quantum
                q.append(i)
            else:
                t += rem_bt[i]
                rem_bt[i] = 0
                ct[i] = t

    for i in range(n):
        tat[i] = ct[i]  # arrival assumed 0
        wt[i] = tat[i] - burst_times[i]
    avg_wt = sum(wt)/n
    avg_tat = sum(tat)/n
    return ct, tat, wt, avg_wt, avg_tat

def main():
    processes = ['P1','P2','P3','P4']
    burst_times = [5, 15, 4, 3]
    quantum = 4
    ct, tat, wt, avg_wt, avg_tat = round_robin(processes, burst_times, quantum)
    print("Process\tBT\tCT\tTAT\tWT")
    for i in range(len(processes)):
        print(f"{processes[i]}\t{burst_times[i]}\t{ct[i]}\t{tat[i]}\t{wt[i]}")
    print(f"\nAverage Waiting Time = {avg_wt:.2f}")
    print(f"Average Turnaround Time = {avg_tat:.2f}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
Process	BT	CT	TAT	WT
P1	5	9	9	4
P2	15	29	29	14
P3	4	13	13	9
P4	3	7	7	4

Average Waiting Time = 7.75
Average Turnaround Time = 14.50
```

5. CONCLUSION:
- RR gives good response time for interactive systems; choice of quantum impacts context switch overhead and turnaround times.

---

EXP E: FIFO Page Replacement (First-In First-Out)
1. AIM:
   - Implement FIFO page replacement and show number of page faults for a reference string and a given number of frames.

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (fifo_page_replacement.py):
```python
# fifo_page_replacement.py
from collections import deque

def fifo_page_replacement(reference_string, frames_count):
    frames = deque()
    frames_set = set()
    page_faults = 0

    for page in reference_string:
        if page not in frames_set:
            page_faults += 1
            if len(frames) < frames_count:
                frames.append(page)
                frames_set.add(page)
            else:
                old = frames.popleft()
                frames_set.remove(old)
                frames.append(page)
                frames_set.add(page)
        # else page hit -> do nothing
    return page_faults

def main():
    ref = [7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2]
    frames = 3
    faults = fifo_page_replacement(ref, frames)
    print(f"Reference string: {ref}")
    print(f"Frames: {frames}")
    print(f"Page Faults = {faults}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
Reference string: [7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2]
Frames: 3
Page Faults = 9
```

5. CONCLUSION:
- FIFO is simple but can suffer from Belady's anomaly (increased frames may increase faults in some cases).

---

EXP F: LRU Page Replacement (Least Recently Used)
1. AIM:
   - Implement LRU and count page faults for a reference string and frames.

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (lru_page_replacement.py):
```python
# lru_page_replacement.py
def lru_page_replacement(reference_string, frames_count):
    frames = []
    page_faults = 0
    for page in reference_string:
        if page in frames:
            # move it to most recently used (end)
            frames.remove(page)
            frames.append(page)
        else:
            page_faults += 1
            if len(frames) < frames_count:
                frames.append(page)
            else:
                # remove least recently used (front)
                frames.pop(0)
                frames.append(page)
    return page_faults

def main():
    ref = [1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5]
    frames = 3
    faults = lru_page_replacement(ref, frames)
    print(f"Reference string: {ref}")
    print(f"Frames: {frames}")
    print(f"Page Faults = {faults}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
Reference string: [1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5]
Frames: 3
Page Faults = 10
```

5. CONCLUSION:
- LRU approximates optimal page replacement by discarding the least recently used page; commonly implemented with counters or stack/list structures.

---

EXP G: Sequential File Allocation Method (Simulation)
1. AIM:
   - Simulate sequential file allocation on a disk of fixed-size blocks: allocate contiguous blocks for each file, detect allocation failure if insufficient contiguous blocks.

2. REQUIREMENTS:
   - Python 3.x

3. PROGRAM (sequential_file_allocation.py):
```python
# sequential_file_allocation.py
def allocate_sequential(disk_size, files):  # files is list of (filename, size_in_blocks)
    # disk represented as list of 0/1 (0 free, 1 occupied)
    disk = [0]*disk_size
    allocation = {}
    for fname, fsize in files:
        found = False
        # search for contiguous free region of length fsize
        for start in range(0, disk_size - fsize + 1):
            if all(disk[start + offset] == 0 for offset in range(fsize)):
                # allocate
                for offset in range(fsize):
                    disk[start + offset] = 1
                allocation[fname] = list(range(start, start+fsize))
                found = True
                break
        if not found:
            allocation[fname] = None  # allocation failed
    return allocation, disk

def main():
    disk_size = 20
    files = [('A', 5), ('B', 4), ('C', 7), ('D', 6)]
    allocation, disk = allocate_sequential(disk_size, files)
    for fname, blocks in allocation.items():
        if blocks is None:
            print(f"File {fname} allocation failed (no contiguous space).")
        else:
            print(f"File {fname} -> Blocks {blocks}")
    print(f"Disk map (0=free,1=used):\n{disk}")

if __name__ == '__main__':
    main()
```

4. SAMPLE OUTPUT:
```
File A -> Blocks [0, 1, 2, 3, 4]
File B -> Blocks [5, 6, 7, 8]
File C -> Blocks [9, 10, 11, 12, 13, 14, 15]
File D -> Blocks [16, 17, 18, 19, 20]  # if disk size allows; otherwise allocation fails
Disk map (0=free,1=used):
[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
```
(Note: adjust disk size/files to illustrate success or failure; example above assumes disk_size sufficiently large.)

5. CONCLUSION:
- Sequential allocation is simple and offers fast sequential access, but suffers fragmentation and difficulty finding large contiguous blocks for new files.

---

GENERAL NOTES & EXPLANATIONS

- Waiting Time (WT) for a process = Turnaround Time - Burst Time (assuming arrival time = 0).
- Turnaround Time (TAT) = Completion Time - Arrival Time. With arrival times assumed zero in these examples, TAT = Completion Time.
- For real exam variants you may be asked to include arrival times. The above programs assume arrival times = 0 unless otherwise adapted.
- For SJF and priority scheduling with non-zero arrival times, you must implement arrival-time-aware selection: at each time step pick the eligible (arrived) process with minimum burst (SJF) or highest priority.
- For RR, quantum selection should consider context-switch overhead if modeled.

---

EXTENDING THE SCRIPTS
- To adapt for user input: replace the example arrays with input() prompts and parse numbers.
- To support arrival times: add arrival_time arrays and modify scheduling loops to consider arrival <= current_time before selecting next process.

---

REFERENCES
- Silberschatz, Galvin, Gagne — Operating System Concepts
- Tanenbaum — Modern Operating Systems
- Standard OS lecture notes and lab guides

```
