# Testbench structure
## Rationale
When starting an RTL design it is usually a good idea to start making a testbench that grows along with the design. 
By doing so, one can ensure that every feature added works one by one rather than finding a number of errors once a full module is completed which can be tough to parse. 

The pitfall of writing the testbench along with the module is that it is easy to make "quick and dirty" tests that scales poorly when the testbench grows. 
It is not necessarily wrong to do initial testing quick and dirty, but it does not take much before making things scalable pays off. 
Working with a poor structure can be both painful or boring and it is less confidence inspiring than when you know you are able to hit your test targets. 

By making a routine of writing testbenches that scale well - as early as possible - we both save time in the long run because the tests also document that we have achieved our specification. 

So, what distinguishes testbench code that scales well and that which scales poorly?

## What scales poorly
### Intertwined stimuli and checks

## What scales well?
By acknowledging that testbenches are software, whether it is written in VHDL, System Verilog, System C, C, Python or any other language, we accept that normal practices for good software also applies to testbenches. 
Without going into every detail in what entails good software, we focus on those things that is easy to forget when doing the initial testbenches.

### Every feature written once
Generally, every method should be written once. 
When applied multiple times 

#### Using triggers rather than called checks

### Using Queues to pass data

### Each check on its own

### Each model on its own. 

