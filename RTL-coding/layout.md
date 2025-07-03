# Let indenting match the code hierarchy! #
Indenting should match the hierarchy and complexity of the module. Use space, rather than tabs for indentation.

The best way to consistently achieve this is using block layout as the leading principle. 
Column layout can be added if it aids readability without breaking the block layout. 
Endline layout should be avoided entirely since it does not match hierarchy and complexity. 

Having indenting matching the hierarchy makes the code easiest to read, maintain and understand. 

## Rationale 
Indenting is moving the code different distances from the margin to make
it more readable. 
It is not necessary in order for the VHDL code to be understood by the compiler, but it is completely necessary so that other people can understand the code that has been written. 
Reviewing code that is not indented takes extra time, and it may well pay off making indentation while reviewing if it does not already exist[^1]. 

[^1]: The experience from correcting a number of exams, is that some code is easier to copy into a dedicated editor, correct the indentation and turn on context highlighting is faster than trying to twist your mind reading code as presented. This is typically true when there is no, or inconsequent indentation.    

Without indenting it is hard to understand where conditional statements start and end. This goes in particular when making complex decision trees, such as state machines. 

Considering that there is little cost in making the indentation from the start, there is really no compelling reason not to do so. 


### Example of un-indented and well-indented code:
**Poor (no) indenting**
```vhdl
REGISTERS: process (clk) is 
begin if rising_edge(clk) then 
count <= (others => '0') 
when reset else std_logic_vector(next_count); 
end if; end process;
```

Poor indenting makes it hard to identify the beginning and end of statements and processes, even with reasonable use of lines. 

**Good indenting** 
```vhdl
REGISTERS: process (clk) is
begin   
   if rising_edge(clk) then
     count <=  
       (others => '0') when reset else
       std_logic_vector(next_count);
   end if;
end process;
```

Good indenting and line use makes it easy to understand where each statement or process starts and ends.

### Tab/space for indenting

If you use the tab key to indent program lines, you normally insert the tab character in the document, rather than a certain number of spaces 
(as is done if you press the space bar). 
How the program is displayed then is up to the various editors. 
The use of tab has both advantages and disadvantages, but it can quickly look very strange in other editors if a program is written with both tabs and spaces indiscriminately. 
*It is wise to avoid mixing different types of indentation.*

**For VHDL, we recommend  using only spaces for indenting.**
2 spaces should be used for each level. 
The tab key can be set to insert a certain number of spaces instead of the tab character in the document. 
This is how you can avoid pressing the space bar twice. 

## Counterexamples and limitations
If you have compelling cases or reasons to not let indentation match the hierarchy, please let us know in the discussions!
