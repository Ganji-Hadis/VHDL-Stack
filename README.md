# VHDL-Stack

     library IEEE;
     use IEEE.STD_LOGIC_1164.ALL;
    ------------------------------------------------------------------------
    entity stack is
    	port(clk , rstN , push , pop : in std_logic ;
		    Data_In : in std_logic_vector(0 to 3);
		    full , empty : out std_logic;
		    Data_out : out std_logic_vector(0 to 3) );
    end stack;
    ------------------------------------------------------------------------
    architecture Behavioral of stack is

    type stack32bit is array(0 to 7) of std_logic_vector(0 to 3);
    signal sp : integer := 0;
    signal remove_data : std_logic_vector(0 to 3):= "0000" ;
    signal stack : stack32bit;

    begin
    	process(clk , rstN)
	
		--type stack32bit is array(0 to 7) of std_logic_vector(0 to 3);
		--variable sp : integer := 0;
		--variable remove_data : std_logic_vector(0 to 3):= "0000" ;
	begin
		
		--Data_out <= "0000";
		if (rstN = '1' ) then
			full <= '0' ;
			empty <= '0' ;
		elsif(rstN = '0') then
			if (rising_edge(clk)) then
				if(sp = 0) then          -- checking full & empty
					empty <= '1' ;
					full <= '0' ;
				elsif(sp = 8) then
					empty <= '0' ;
					full <= '1' ;
				end if;
		
				
				if(push = '1') then      -- pushing data
					if(sp = 8) then
						empty <= '0';
						full <= '1';
					else
						sp <= sp + 1;
						stack(sp) <= Data_In;
						empty <= '0';
						full <= '0';
					end if;
				end if;
				
				
				if(pop = '1') then    --poping data
					if(sp = 0) then
						empty <= '1';
						full <= '0';
					else
						--Data_out <= stack(sp);
						remove_data <= stack(sp);
						sp <= sp - 1;
						empty <= '0';
						full <= '0';
					end if;
				end if;
				
				Data_out <= remove_data ;
				
			end if;
			
		
		end if;
	
	  end process;	

    end Behavioral;


#TestBench

    LIBRARY ieee;
    USE ieee.std_logic_1164.ALL;
    --------------------------------------------------------- 
    ENTITY stackTest IS
    END stackTest;
    ---------------------------------------------------------
    ARCHITECTURE behavior OF stackTest IS 
 
    -- Component Declaration for the Unit Under Test (UUT)
 
    COMPONENT stack
    PORT(
         clk : IN  std_logic;
         rstN : IN  std_logic;
         push : IN  std_logic;
         pop : IN  std_logic;
         Data_In : IN  std_logic_vector(0 to 3);
         full : OUT  std_logic;
         empty : OUT  std_logic;
         Data_out : OUT  std_logic_vector(0 to 3)
        );
    END COMPONENT;
    

       --Inputs
       signal clk : std_logic := '0';
       signal rstN : std_logic := '0';
       signal push : std_logic := '0';
       signal pop : std_logic := '0';
      signal Data_In : std_logic_vector(0 to 3) := (others => '0');
    
  	--Outputs
      signal full : std_logic;
    signal empty : std_logic;
    signal Data_out : std_logic_vector(0 to 3);

     -- Clock period definitions
     constant clk_period : time := 10 ns;
 
    BEGIN
 
	-- Instantiate the Unit Under Test (UUT)
     uut: stack PORT MAP (
           clk => clk,
          rstN => rstN,
          push => push,
          pop => pop,
          Data_In => Data_In,
          full => full,
          empty => empty,
          Data_out => Data_out
        );

     -- Clock process definitions
     clk_process :process
    begin
		clk <= '0';
		wait for clk_period/2;
		clk <= '1';
		wait for clk_period/2;
    end process;
 

    -- Stimulus process
     stim_proc: process
     begin	

		rstN <= '1';
		wait for 10 ns;
      -- hold reset state for 100 ns.
		
		rstN <= '0';
		
		---- push -------
		
      wait for clk_period;     
		push <= '1' ;            -- sp = 1
		Data_In <= "0010";
		wait for 10 ns;
		push <= '0' ;
		
		wait for clk_period;
		push <= '1' ;            -- sp = 2
		Data_In <= "0001";
		wait for 10 ns;
		push <= '0' ;
		
		wait for clk_period;
		push <= '1' ;            -- sp = 3
		Data_In <= "0010";
		wait for 10 ns;
		push <= '0' ;
		
		
		wait for clk_period;
		push <= '1' ;             -- sp = 4
		Data_In <= "0011";
		wait for 10 ns;
		push <= '0' ;
		
		--wait for clk_period;
	--	push <= '1' ;             -- sp = 5
		--Data_In <= "0100";
		--wait for 10 ns;
		--push <= '0' ;
		
		
		----- pop -----
		wait for clk_period;
		pop <= '1' ;              -- sp = 4
		wait for 12 ns;
		pop <= '0' ;		
		wait for 10 ns;
		
		wait for clk_period;
		pop <= '1' ;
		wait for 10 ns;          -- sp = 3
		pop <= '0' ;
		wait for 10 ns;
		
		wait for clk_period;     
		pop <= '1' ;             -- sp = 2
		wait for 10 ns;
		pop <= '0' ;
		wait for 10 ns;
		
		wait for clk_period;     
		pop <= '1' ;             -- sp = 1
		wait for 10 ns;
		pop <= '0' ;
		wait for 10 ns;
		
		wait for clk_period;     
		pop <= '1' ;             -- sp = 0
		wait for 10 ns;
		pop <= '0' ;
		wait for 10 ns;
		
      wait for clk_period*10;

      -- insert stimulus here 

      wait;
     end process;

    END;
