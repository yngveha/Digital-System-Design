# Use conditional statements restrictively! #
When making conditional statements, the most restrictive statement should be preferred. 

## Rationale
Conditional statements that do not define all output targets for all conditions may result in unwanted behavior. 
Typical unwanted behavior is latches or registers created from behavior meant to be combinational. 

By using the most restrictive conditional statements, we limit the possibility of creating latches. 

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

The with-select statement is the most restrictive and cannot create latches unless you actively feed back the signal to its assignment. 
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
  a when high_pri_flag = '1' else
  b when low_pri_flag else
  (others => '0');

-- latched when-else due to implicit behavior:
my_latch <= input when '1'; 

-- case not causing latches , without default statements
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

-- case not causing latches , with default statements
process(all) is 
begin 
  a <= '0';
  b <= '0';
  c <= '0';
  case state is 
    when state_a =>
      a <= '1';
    when state_b =>
      b <= '1';
    when state_c => 
      c <= '1';
    when others  =>    
      null; -- is telling reviewer we chose default values
  end case;
end process;

-- case causing latches (by forgetting default values)
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

-   When in doubt...
    -   Try **'with...select**'. This will force you to make visible
        choices.
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
The width-select statement is probably the most under-used statement in VHDL. 
This is both because of its late arrival, and general laziness.  

