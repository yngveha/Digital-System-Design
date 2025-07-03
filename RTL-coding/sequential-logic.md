# Separate registers from combinational logic! #
In RTL-modules, register assignment should be performed in a separate process from anything generating combinational logic. 

Register reset should be stated where the registers are assigned. 
Reset should normally be performed synchronously to the clock. 

## Rationale 
By separating registers from combinational logic you will reduce the risk of creating more registers than you planned to do. 

By keeping combinational logic in processes that are solely combinational, you will get the full benefit of warnings from the toolchain if you accidentally create latches or registers within the code.
The errors that follow when registers or latches are created by forgetting to state all possible outcomes of combinational circuits, often occur at seemingly random times, which make them difficult to test for and debug.  

Reset is for clearing registers to have them in a predictable state.
Although synchronous reset can be stated as combinational logic, the use of reset is setting registers, not interfering with the combinational code function. 
Keeping it in one place, makes it easier to check that registers are reset the way the hardware in use demands. 

Synchronous reset should normally be preferred to asynchronous reset, to avoid glitches causing register reset, or having reset cause metastability propagating through modules.
Since every register capable of asynchronous reset technically may be reset synchronously as well, there are few reasons not to use synchronous reset except when synchronizing the reset signal.

### Example and discussion
```vhdl
REGISTERS: process(clk) is 
  begin 
    if rising_edge(clk)then 
      if reset then 
        r_valid <= '0';
        r_sum   <= (others => '0');
        r_count <= 0;
      else
        r_valid <= next_valid;
        r_sum   <= next_sum;
        r_count <= next_count;
      end if;
    end if;
  end process;
  
COMBINATIONAL_LOGIC:
next_valid <= valid_a and valid_b;
next_sum   <= std_logic_vector("0" & unsigned(a) + "0" & unsigned(b));
next_count <= 0 when r_count = MAXCOUNT else r_count + 1; 
```


## Counterexamples and limitations
### Single process state machines, process(clk) with combinational logic.
Single processes state machines, using variables rather than signals simulate faster. 
They simulate faster because there are fewer checks on changes to signals, which means less information is available during verification. 
Having less information available during verification may prove more time consuming to debug, especially when errors are made using variables that normally are hidden during simulation.  
 
Code being used in libraries or IPs that are intended to be used without modification, may benefit from being tweaked to achieve performance during simulation. 
If this is the case, it is reasonable to code and verify the part first using separate registers and combinational logic. 
Once the verification process is complete, equivalent - single process logic may be built.
The single-process solution may then be formally verified (proven) equivalent to the thoroughly verified version. 
By doing it this way we maintain a chain of verifiability along the route to code that otherwise is harder to verify and understand.  

### Asynchronous reset
Asynchronous reset is useful and may be required when synchronizing reset signals propagating from one clock domain to another. 
