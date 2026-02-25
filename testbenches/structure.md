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
Generally, every method in a testbench should be written once, and not more.
This means errors in the code can be located in one place, and it makes making modifications easier, as it does not require going through a whole file searching for where changes must be applied. 

When a method is used multiple times in the same context, this should be achieved using loops or some sort of triggering to reduce the amount of times we call upon the same method.  

#### Using triggers rather than called methods
One way of reducing the amount of calls to a method, is that the behaviour is triggered rather than called. 
By designing a trigger, we isolate the question on whether we were thorough enough in our description of the use to one place. 
Having a well thought through trigger means we understand that either stimulidata is applied at the correct time, or that we always check what is to be checked when appropriate.

Typical examples where triggering can be used is when describing
* Model behavior for stimuli
  * bus functional models
  * component model behavior
* Checker routines
  * Most checks in an RTL testbench can be made in a way that they stand alone and fires at the correct time, regardless of the number of uses.       

#### Using Queues to pass data

### Each check on its own

### Each model on its own. 

