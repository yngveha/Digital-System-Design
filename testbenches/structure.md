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
```
<Apply stimuli "A1">
<wait for A1 to resolve>
<check "A1" response>

<Apply stimuli "B1">
<wait for B1 to resolve>
<check "B1" response>

<Apply stimuli "A2">
<wait for A2 to resolve>
<check "A2" response>

<Apply stimuli "C1">
<wait for C1 to resolve>
<check "C1" response>

<Apply stimuli "A3">
<wait for A3 to resolve>
<check "A3" response>

<Apply stimuli "B2">
<wait for B1 to resolve>
<check "B1" response>
...
<etc.>
...
```

If we look at the pseudo code above, it is obvious that the list easily grows tiresomly long, and that dependence between stimuli and data must be maintained manually. 
This increases the level of frustration for each change necessary, and it is hard to see when we reach or test or coverage targets looking at the code. 

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
The software backbone required to write standalone stimuli and checks often require ways of passing data within the testbench. 
Data fed into the DUT at some point usually ends up being compared to a golden model or some sort of validated results at a later stage. 
To avoid having to create structures that wait between each stimuli injected and its corresponding check, queues can be fed the data later used when the check is triggered. 
Having multiple data queues add very little structure compared to having to sort data upon arrival when checks may be required unordered. 
By using queues for data as the go-to solution, we alleviate scaling issues before they occur. 

The same _can be achieved_ using FIFO buffers. 
While this may be easy enough for a hardware engineer, adding hardware modules in a software module may increase complexity in ways that are not required. 
> [!NOTE]
> While the term FIFO (First in First out) technically does not need to mean hardware FIFO, this is the point where it makes sense to separate between hardware and software constructs.
> In this context we use the term Queue for software queues, which do not need the same sort of instantiation that a hardware FIFO component would.

Using queues we have to make sure the queues are created in a context that is available to all methods that use them. 
In a small scale test, queues may be global. 
For larger tests, queues may be passed as parameters to the test objects in use. 

#### Each check on its own
Rather than tying each check to specific data passed, it is generally more useful to apply a check when the correct situation occurs using a trigger. 
By having each check written separately, and in one spot, it is easy to understand whether we are able perform the checks according to our test specification. 

#### Each model on its own. 
When a system become a composition of several components, and we only want to test or make one, it is often useful to create models of the other components. 
This way we can focus on making sure each model does what it should, rather than creating a long list of intertwined stimuli data, where every new event must happen in coherence with the everything else. 

In software we can create model-components as object reacting more or less autonomous, or we can create an API for the model that we use when called for.   
When model behavior reaches the size of more than a couple methods, it may be useful to encapsulate the behavior and code into a separate class. 

## Example
Below is python pseudocode that mimics much behavior described above (not all). 
An executable example is on the IN3160 course github (available to UIO students and staff). 

```python
data_A = [<A1>, <A2>, <A3>, ...]
data_B = [<B1>, <B2>, ...]
data_C = [<C1>,  ...]

queue_A = Queue()
queue_B = Queue()
queue_C = Queue()
...
def stim_A(dut):
    for each in data_A:
        <perform model A behavior for each data>
        queue_A.add(each)
def stim_B(dut):
    for each in data_B:
        <perform model B behavior for each data>
        queue_B.add(each)
def stim_C(dut):
    ... <similar to A and B, but specific for C>
...
async def check_A(dut):
     while True:
         await <A's trigger>
         data_A = queue_A.get()
         assert <DUT status corresponds to data_A> ...

async def check_B(dut):
     while True:
         await <B's trigger>
         data_B = queue_B.get()
         assert <DUT status corresponds to data_B> ...

checks = [check_A(dut), check_B(dut), check_C(dut), ...]
stimulants = [stim_A(dut), stim_B(dut), stim_C(dut), ...]
...
@cocotb.test()
async def test_schedule(dut):
     for each_check in checks:
        start_soon(each_check)
     for each_stim in stimulants:
        start_soon(each_stim) 
     await Combine(stimulants) 
```

Keeping modeled behavior, stimuli and checks separate it is easy to later make modifications or adding behavior.
By letting the simulator run each check independently (start_soon), the simulator can perform each check at the appropriate time when the awaited triggers occur. 
The use of ```Combine``` will allow each task to complete fully, regardless of order. 

> [!NOTE]
> In the pseudocode the stimuli data, queues behavior and checks are global.
> 
> Make proper class encapsulations, file io, to suit the system complexity.
>
> The structure is not intended to be perfect or all-encompassing.
> It is an abstraction of an example used in our Bachelor level digital system design course at UIO. 

> [!NOTE]
> The checks and stimuli are applied much in the same way as if they had their own process in VHDL.
> 
> VHDL does not have a built-in queue, and while it is possible to achieve, using a well suited software language may be easier to work with. 
