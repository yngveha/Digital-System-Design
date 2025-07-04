# Use conditional statements restrictively! #
When making conditional statements, the most restrictive statement should be preferred. 

## Rationale
Conditional statements that do not define all output targets for all conditions may result in unwanted behavior. 
Typical unwanted behavior is latches or registers created from behavior meant to be combinational. 

By using the most restrictive conditional statements, we get tool feedback at the earliest point possible.  

## Discussion with examples
In VHDL there are many ways of describing the wanted conditional behavior of your design. 
To help with deciding which conditional statement is sufficient, look at this table overview and summary:

| Statement           | Targets  | Conditions | Process  | Prioritizes |
|---------------------|----------|------------|----------|-------------|
| **if**              | Multiple | Multiple   | Required | Yes         |
| **case**            | Multiple | Single     | Requires | No          |
| **when ... else**   | Single   | Multiple   | Optional | Yes         |
| **with ... select** | Single   | Single     | Optional | No          |

Conditional statement overview in VHDL

-   Target: How many signals/variables can be set if true?
-   Condition: Can the true condition be a statement?

<!-- -->

The **selected (with-select)** statement is the most restrictive and cannot create latches unless you actively feed back the signal to its assignment. 
With-select statement requires every option for the input to be covered
That is:
```vhdl
-- non-latch.  
with control select busdata <=
  a when 1,
  b when 2,
  c when 3,     
  (others => '0') when others;


 -- latch from with select requires explicit self-reference:
with enable select my_latch <= 
  input when '1', 
  my_latch when others;
```

All the other statements will create latches if simply one condition or target is omitted.
```vhdl
-- complete when-else (not latched) 
busdata <= 
  a when high_pri_flag else
  b when low_pri_flag else
  (others => '0');

-- TO BE AVOIDED: latched when-else due to implicit behavior:
my_latch <= input when enable;

-- Explicit latch using when-else:
-- By refering to self, we admit the purpose to whoever reviews the code. 
my_latch <= input when enable else my_latch; 
```

<sup>When-else examples. When else can be latched implicitly when omitting possible conditions</sup>

Examples with case; all examples uses the same type declaration:

```vhdl
-- <state type declaration for all case examples>
type statetype is (state_a, state_b, state_c, state_d);
signal state: statetype;
-- <...>

-- fully defined case, without default statements
process(all) is 
begin 
  case state is 
    when state_a =>
      a <= '1';
      b <= '0';
      c <= '0';
    when state_b =>
      a <= '0';
      b <= '1';
      c <= '0';
    when state_c => 
      a <= '0';
      b <= '0';
      c <= '1';
    when others  =>    
      a <= '0';
      b <= '0';
      c <= '0';
  end case;
end process;

-- case with default values
process(all) is 
begin
  -- default values:
  a <= '0';
  b <= '0';
  c <= '0';
  case state is 
    when state_a => -- b and c gets default value
      a <= '1';
    when state_b => -- a and c gets default value
      b <= '1';
    when state_c => -- a and b gets default value
      c <= '1';
    when others  =>    
      null; -- is telling reviewer we chose default values
  end case;
end process;

-- TO BE AVOIDED: case causing latches (by forgetting default values)
-- ie not setting all targets for all conditions
process(all) is 
begin 
  case state is 
    when state_a => -- b and c is latched
      a <= '1'; 
    when state_b => -- a and c is latched
      b <= '1';
    when state_c => -- a and b is latched
      c <= '1';
    when others  =>    
      a <= '0';
      b <= '0';
      c <= '0'; 
  end case;
end process;
```

<sup>Case based examples. Case requires every condition to be covered</sup>


```vhdl
-- TO BE AVOIDED: if causing latches (by not covering every condition)
-- a, b and c is latched in case of state = state_b or any other state except state_a and state_c 
process(all) is 
begin 
  if state = state_a then 
      a <= '1';
      b <= '0';
      c <= '0';
  elsif state = state_c then 
      a <= '0';
      b <= '0';
      c <= '1';
  end if;
end process;
```

<sup>Latched example where not all conditions are covered</sup>

-   When in doubt...
    -   Try **'with...select**'. It will never cause latches unless by intention.
    -   **case** is typically used for state-machines together with default values
    -   **when else** is reader friendly, and can replace most ifs except register assignment.
-   *Only* use **'if**'...
    -   When you need to prioritize conditions...
    -   and have multiple targets.
    -   Typically used for register assignment.
    ```vhdl
    REGISTER_ASSIGNMENT:
    process(clk) is 
    begin 
      if rising_edge(clk) then 
        if reset then 
          r_logic  <= '0';
          r_vector <= (others => '0');
          r_int    <= 0;
        else 
          r_logic  <= next_logic;
          r_vector <= next_vector;
          r_int    <= next_int;
        end if;
      end if;
    end process;
    ```
-   It is fine to use **'when...select**' or **'when...else**' inside
    **if** and **case** in processes.
    -   Do you need if inside if?
    -   Or case inside case?
    -   Readability suffers when nesting several levels of if or case.

**Whatever you choose, keep the following in mind:**

-   define:
    -   all output for
    -   all conditions

## Counterexamples and limitations
Technically, any conditional statement[^1] in VHDLcan be solved using a combination of concatenation and the selected statement. 
Using concatenation solely to allow the use of more restrictive statements means sacrificing readability. 
Doing so will result in constructs that are meaningless for any other purposes, and cannot be reccomended. 
Maintaining a structure that is meaningful is more important than using the most restrictive conditional statement. 

Having said this, over-use of the if-statement, largely due to the use of "single-process" methodology, is way more common mistake than over-use of the selected statement.
Except for register assignment, the use of if-statements can in many cases be reduced greatly, with added benefits in both verifiability and readability.  

[^1]: Without having tested, we cannot tell the outcome of using rising_edge() in a selected statement. 
Perhaps "non-clocked conditional" statement would be the correct phrase. 
