# Module: FENCEI

## Feature: Fetching 

### Sub-feature: 000_Fetching

#### Item: 000

* **Requirement location:** CVA6 User Manual
* **Feature Description**
  
  The FENCE.I instruction is used to synchronize the instruction and data streams. RISC-V does not guarantee that stores to instruction memory will be made visible to instruction fetches on the same RISC-V hart until a FENCE.I instruction is executed.   
    
  Pseudocode: Fence(Store, Fetch)
* **Verification Goals**
  
  The instruction immediately before FENCE.I, the FENCE.I instruction and the instruction immediately following FENCE.I are fetched into the CVA6. Instruction data for the next PC must be fetched after the FENCE.I instruction has executed (because only then can data-side stores have completed and caches have been updated).  
  Check that after a FENCE.I instruction retires then instr-side AXI fetches the next instruction to be executed. (in both cases; with and without a FENCE.I instruction inserted)
* **Pass/Fail Criteria:** Self-Check
* **Test Type:** Other
* **Coverage Method:** Functional Coverage
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F000_S000_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
## Feature: StoresVisible

### Sub-feature: 000_StoresVisible

#### Item: 000

* **Requirement location:** The RISC-V Instruction Set Manual
Unprivileged ISA
Chapter 3
* **Feature Description**
  
  The FENCE.I instruction is used to synchronize the instruction and data streams. RISC-V does not guarantee that stores to instruction memory will be made visible to instruction fetches on a RISC-V hart until that hart executes a FENCE.I instruction. To make a store to instruction memory visible to all RISC-V harts, the writing hart has to execute a data FENCE before requesting that all remote RISC-V harts execute a FENCE.I.
* **Verification Goals**
  
  After a FENCE.I instruction has been executed, all preceding store instructions shall have their effects visible to the Instruction Fetch of the instructions that are to be executed after the FENCE.I instruction.  
  Do a FENCE.I, but right before the fencei do a store to the instruction following the FENCE.I, then see that the newly stored value is executed instead of the old instruction (e.g. change addi to use a different immediate).
* **Pass/Fail Criteria:** Check RM
* **Test Type:** Constrained Random
* **Coverage Method:** Functional Coverage
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F001_S000_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
## Feature: MultiCycle

### Sub-feature: 000_MultiCycle

#### Item: 000

* **Requirement location:** CVA6 User Manual
* **Feature Description**
  
  [Not in specification] Given zero stalls on neither instr-side and data-side AXI, the execution of the FENCE.I should take a fixed number of cycles.
* **Verification Goals**
  
  Check that, given ideal conditions, the cycle count of FENCE.I is as expected.
* **Pass/Fail Criteria:** Assertion
* **Test Type:** ENV Capability
* **Coverage Method:** Assertion Coverage
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F003_S000_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
## Feature: StoresComplete

### Sub-feature: 000_StoresComplete

#### Item: 000

* **Requirement location:** The RISC-V Instruction Set Manual
Unprivileged ISA
Chapter 3
* **Feature Description**
  
  A FENCE.I instruction ensures that a subsequent instruction fetch on a RISC-V hart will see any previous data stores already visible to the same RISC-V hart.
* **Verification Goals**
  
  Any store instruction that is successfully executed before a FENCE.I will fully complete and have its effect visible (this is not about syncronization with instruction fetch, but rather seeing that the stores are not aborted)  
  Check that all stores (either to next pc or other places) preceding a fence.i will complete on the bus (excluding exceptions/interrupts/etc) and be readable afterwards (particularly, ensure that the write buffer isn't just purged).
* **Pass/Fail Criteria:** Self-Check
* **Test Type:** Directed SelfChk
* **Coverage Method:** N/A
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F004_S000_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
## Feature: Flush

### Sub-feature: 000_Flush

#### Item: 000

* **Requirement location:** The RISC-V Instruction Set Manual
Unprivileged ISA
Chapter 3
* **Feature Description**
  
  The FENCE.I instruction was designed to support a wide variety of implementations. A simple implementation can flush the local instruction cache and the instruction pipeline when the FENCE.I is executed.
* **Verification Goals**
  
  When FENCE.I is executed, then any prefetched instructions shall be flushed; meaning that pipeline stages are flushed, prefetcher is flushed, write buffer is flushed, and mem_data_req_o (wt_dcache) is eventually supressed.  
  Check that a FENCE.I will cause flushing of the pipeline, prefetcher, write buffer, and mem_data_req_o.
* **Pass/Fail Criteria:** Assertion
* **Test Type:** ENV Capability
* **Coverage Method:** Assertion Coverage
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F005_S000_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
### Sub-feature: 001_OptimisedFlushing

#### Item: 000

* **Requirement location:** The RISC-V Instruction Set Manual
Unprivileged ISA
Chapter 3
* **Feature Description**
  
  If instruction and data caches are kept coherent in this way, or if the memory system consists of only uncached RAMs, then just the fetch pipeline needs to be flushed at a FENCE.I.
* **Verification Goals**
  
  Conditioned flushing; check that the flushing is executed when necessary! When FENCE.I is executed but the subsequent instruction is data_side independent, the flushing does not have to operate.  
  Do a FENCE.I followed by an instruction independent to the one preceding the FENCE.I, and check if the flushing is ignored
* **Pass/Fail Criteria:** Self-Check
* **Test Type:** NDY (Not Defined Yet)
* **Coverage Method:** Assertion Coverage
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F005_S001_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
### Sub-feature: 002_FlushFlow

#### Item: 000

* **Requirement location:** CVA6 User Manual
* **Feature Description**
  
  [Not in specification] When a FENCE.I invoke the fluching request, we should make sure that it's well acknowledged.
* **Verification Goals**
  
  Check that when executing a FENCE.I instruction there will be a fence_i_commit_controller request by the controller responding to the commit stage.
* **Pass/Fail Criteria:** Assertion
* **Test Type:** NDY (Not Defined Yet)
* **Coverage Method:** N/A
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F005_S002_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
## Feature: UnusedFields

### Sub-feature: 000_UnusedFields

#### Item: 000

* **Requirement location:** The RISC-V Instruction Set Manual
Unprivileged ISA
Chapter 3
* **Feature Description**
  
  The unused fields in the FENCE.I instruction, imm[11:0], rs1, and rd, are reserved for finer-grain fences in future extensions. For forward compatibility, base implementations shall ignore these fields, and standard software shall zero these fields.
* **Verification Goals**
  
  Try giving random values in those fields and see that all else works as expected
* **Pass/Fail Criteria:** Check RM
* **Test Type:** Constrained Random
* **Coverage Method:** Functional Coverage
* **Applicable Cores:** CV32A6_v0.1.0, CV32A6-step2, CV64A6-step3
* **Unique verification tag:** VP_FENCEI_F006_S000_I000
* **Link to Coverage:** 
* **Comments**
  
  *(none)*  
  
