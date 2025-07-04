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
   STATEMENT_SELECTION: process(targets, conditions, nested) is 
    begin 
      case targets is 
        when single => 
          statement <=                                    
            "case"      when nested else
            "when_else" when conditions > 1 else
            "selected";
        when multiple =>   
          with conditions select statement <= 
            "case" when conditions = 1, 
            "if"   when others;
      end case;
    end process;
  ```
* [](./.md)


