# Reset synchronously, and in one place! #
Register reset should be stated where the registers are assigned. 
Reset should normally be performed synchronously to the clock. 

## Rationale 
Reset is for clearing registers to have them in a predictable state.
Although synchronous reset can be stated as combinational logic, the use of reset is setting registers, not interfering with the combinational code function. 
Keeping it in one place, makes it easier to maintain if there are changes to the hardware used for implementation. 

Synchronous reset should normally be preferred to asynchronous reset, to avoid glitches causing register reset, or having reset cause metastability propagating through modules.
Since every register capable of asynchronous reset technically may be reset synchronously as well, there are few reasons not to use synchronous reset except when synchronizing the reset signal.
Using asynchronous reset in cases where the registers are not made for asynchronous use, such as in many DSP-blocks, will cause synthesis to use registers outside the DSP block. 
This can result in slow and more power hungry logic due to excess routing.  

### Example 
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

```


## Counterexamples and limitations
### Separate reset from register assignment?
Synchronous reset may be performed when assigning the next value: e.g. 

```vhdl
REGISTER_ASSIGNMENT:
  r_sum <= next_sum when rising_edge(clk);
COMBINATIONAL_LOGIC:
  next_sum <= (others =>'0') when reset else ("0" & unsigned(a) + "0" & unsigned(b)); 
```

There are, however, few compelling reasons we know to do this. 
At first glance it may seem like a good way to separate combinational logic and registers, but it will make it hard to modify the reset behavior, as it will have to be stated individually for each register, rather than as a group.   
Furthermore, the reset functionality makes the combinational logic statement longer and harder to read. 

### Asynchronous reset
Historically, asynchronous reset has been widely used in FPGA design, since the option has been available. 
Apart from synchronizing reset signals traversing clock domains, there are few circumstances where asynchronous reset make sense.  
There could be reasons for tweaking reset behavior in circuits that has the most extreme timing demands, however this cannot be seen as the normal and it has to be done with much more scrutiny to verify that timing meets every requirement. 

