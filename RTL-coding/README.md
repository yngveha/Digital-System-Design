# [Best practice in RTL coding](./RTL%20coding.md)
* [Let indenting match the code hierarchy!](./layout.md)
  _What is the purpose of indentation?_
* [Use unresolved types!](./type-resolution.md)
  _When is it right to use **std_logic**?_
* [Use functions!](./subprograms.md)
  _When is it best to use procedures and functions?_
* [Separate registers from combinational logic!](./sequential-logic.md)
  _Why is single processes combining registers and combinational a bad idea?_
* [Reset synchronously!](./reset.md)
  _Why not reset asynchronously?_
* [Use conditional statements restrictively!](./conditionals.md)  
  ```vhdl
  STATEMENT_SELECTION: process(targets, conditions) is 
  begin 
    case targets is 
      when 1 =>                                   
        with conditions select statement <=       -- single target
          selected_statement  when conditions = 1,--   single condition
          when_else_statement when others;        --   multiple condition
      when others =>                               
        with conditions select statement <=       -- multiple targets
          case_statement when conditions = 1,     --   single condition
          if_statement   when others;             --   multiple condition
    end case;
  end process;
  ```
* [](./.md)


