# Best practices in Register Transfer Level coding #
In this sections we present practices relevant when coding synthesizable VHDL 
## Rationale 
When coding RTL, the main focus should be creating logically correct code in a way that is verifiable. 
Verification should also be performed at the earliest possible stage, whether that is by reading, compilation or verification testing.
Code re-iteration that happens on an early stage generally cost a lot less effort than reiterating when errors are found later.

We prefer VHDL to Verilog or System Verilog, both for historical reasons and because we emphasize on synthesizable code[^1]. 
VHDL has built in strong type check which can be used to advantage, and VHDL-code simulation is well defined. 
This does not mean Verilog cannot be set up in much the same way, but it is known to be less safe without restrained use. 
VHDL can also be used in more or less safe manners, and the goal should be to achieve the highest degree of verifiability all the time. 

[^1]: For the sake of verification, the convenience of using any hardware description language can be debated. 
For the time being, we teach using co-simulation with cocotb and Python for verification. 
We believe that the discussion on what is best suited for synthesis is best kept separate to that of verification languages.

### Verification by reading
In general, code should be understood by humans. 
In particular RTL code should be easy to understand paired with other documentation such as specifications and various diagrams. 
While human readability will vary with the human reading the code, and what style they are used to. 
There are elements that are common in most cases, and there are conventions that will lead to better readability when followed broadly. 

### Verification by tools
When writing RTL code we believe that it is important to write code in ways that will have the tools generate useful feedback at the earliest possible stage.
If errors can be found at compile-time, that is a much better result than errors propagating to functional testing, synthesis or later.
In VHDL there are several ways to write code that will expose or avoid weaknesses or faults that frequently occur when humans create code. 
Using AI to create code rarely helps in this matter, because of the poor availability of well documented code. 

## When to avoid best practices? 
There may be times where one may need to tweak code to achieve a better result in synthesis or faster simulation. 
We will attempt to address some of these issues, however tweaking code should never be considered best practice on a general basis.

At some point, if we know that our code works well logically, we may create tweaked code for the same purpose. 
This code may then be formally proven equal to code generated using best practices. 
