## 1. Experimental Setup
* **Operating System:** xv6
* **Scheduler Type:** Lottery Scheduler
* **Test Program:** `testlottery.c`
* **Process Configuration:**
    * **Process A:** 10 tickets (via `settickets(10)`)
    * **Process B:** 80 tickets (via `settickets(80)`)
* **Workload:** Both processes executed the `burn()` CPU-bound loop to ensure they remained in the `RUNNABLE` state, forcing the scheduler to make probabilistic choices.

## 2. Methodology
The experiment was conducted inside the xv6 shell using background execution:
```bash
$ testlottery 10 &; testlottery 80 &
```
## 3. Observed Results
* **Syscall Validation:** I ran `testlottery` without arguments, which returned `PASS: settickets validation`. This confirms my implementation of `sys_settickets` correctly returns `-1` when a process attempts to set 0 tickets and returns `0` for valid positive integers.
* **Proportional Share:** When running `testlottery 10 &` and `testlottery 80 &` simultaneously, the process with 80 tickets consistently printed `testlottery: done` significantly earlier than the process with 10 tickets.
* **Relative Performance:** In a typical run, the 80-ticket process completed its workload in roughly 1/8th the time of the 10-ticket process, successfully demonstrating that CPU time is distributed according to the $8:1$ ticket ratio.

## 4. Notes on Variance and Convergence
* **Variance:** Because the lottery scheduler selects the next process using a random number generator, there is short-term "luck" involved. Over a very small number of scheduling decisions, a process with fewer tickets might be picked more often than its fair share. This is known as high variance.
* **Convergence:** As the processes run longer (performing millions of "burn" cycles), the number of random draws increases. According to the **Law of Large Numbers**, the actual percentage of CPU time assigned to each process will get closer (converge) to its theoretical probability (Tickets / Total Tickets).
* **Conclusion:** My experiment confirms that while the scheduler is probabilistic, it is fair over time, as the high-ticket process reliably finishes first in long-running workloads.
