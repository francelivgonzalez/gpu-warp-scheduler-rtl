# GPU Warp Scheduler in SystemVerilog

**Status:** Planned
**Projected timeline:** May-June 2027
**Hardware target:** Digilent Cora Z7-07S
**Tools:** SystemVerilog, AMD Vivado, and C for reading results from the board

## What I’m building

I’ll design a GPU-style scheduler that chooses which group of threads, called a warp, gets to issue an instruction next. My design will track up to 32 warps and allow one to issue per clock cycle.

**I want to learn how one scheduling rule changes two things: how quickly the whole workload finishes and how long an individual warp has to wait.
**

## The two rules I’ll compare

- **Round-robin:** After a warp issues, start the next search at the following warp.
- **Sticky round-robin:** Let the current warp keep issuing while it is ready. When it stalls or finishes, move to the next ready warp.

I’ll start with four warps so I can follow each decision in a waveform. Then I’ll scale the same design to 32.

## What I’ll test

1. **All warps ready.** Check the order in which each policy gives them a turn.
2. **Only one warp ready.** Check that the scheduler selects it regardless of its position.
3. **No warps ready.** Check that the scheduler leaves the issue slot idle.
4. **Memory stalls.** Give each warp a short program with compute instructions and a load. Start the load delay when that warp actually issues it.
5. **Long waits.** Record when a ready warp keeps getting passed over and whether it eventually gets a turn.

## How I’ll use the Cora Z7

The scheduler, workload generator, and counters will run in the FPGA. A small C program on the board’s Arm processor will select a policy, start a run, and read the counters afterward. I’ll replay the same per-warp programs under both policies.

## Timeline

- **May:** Write the interface, build the four-warp scheduler, and verify both policies in Vivado simulation.
- **June:** Expand to 32 warps, run the workloads on the Cora Z7, and compare the results.

## Results I plan to publish

For each workload, I’ll report total cycles, instructions issued, idle cycles, and the longest ready wait. I’ll include the FPGA resource and timing reports and one waveform showing the exact cycle where the two policies make different choices.

## Planned folders

- `rtl/` - scheduler and policy logic
- `tb/` - testbench and correctness checks
- `workloads/` - per-warp instruction sequences
- `fpga/` - Cora Z7 design, counters, and constraints
- `sw/` - Arm program that starts runs and reads results
- `results/` - measurements, waveforms, and Vivado reports
