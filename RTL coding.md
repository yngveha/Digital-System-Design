# Best practices in Register Transfer Level coding #
In this sections we present practices relevant when coding synthesizable VHDL 
## Rationale 
When coding RTL, the main focus should be creating logically correct code in a way that is verifiable. 
Verification can be done at different levels, and each level require some afterthought. 

### Verification by reading
In general, code should be understood by humans. 
In particular RTL code should be easy to understand paired with other documentation such as specifications and various diagrams. 
While human readability will vary with the human reading the code, and what style they are used to, there are elements that are common in most cases, and there are conventions that will lead to better readability when followed broadly. 
Having a course in digital design with several student assistants reading code, there are patterns we see in what constitutes code that is easy to correct. 

### Verification by tools
When writing RTL code we believe that it is important, and possible, to write code in ways that will have the tools generate useful feedback at the earliest possible stage (compilation). 
In VHDL there are several ways to write code that will expose or avoid weaknesses or faults that frequently occur when humans create code. 
Using AI to create code rarely helps in this matter, because of the poor availability of well written code in terms of verifiability openly online. 

## When to avoid best practices? 
There may be times where one may need to tweak code to achieve a better result in synthesis or faster simulation. 
We will attempt to address some of these issues, however tweaking code should never be considered best practice on a general basis.

At some point, if we know that our code works well logically, we may create tweaked code for the same purpose. 
This code may then be formally proven equal to code generated using best practices. 
