# Description 
Look, Mum, no opcodes!

# Solution
this is a VM reverse challenge that consists of only 3 instructions.

this is not the intended way to solve it but reversing it manually would take forever sooo thats when angr comes in handy

this script took about a minute thanks to claripy

```py
import angr
import claripy
#disable "auto_load_libs" to speed up the execution and set the image base to 0 to use offsets instead of addresses in the script
proj = angr.Project("r1sc",load_options={"auto_load_libs": False},main_opts={"base_addr": 0})
input_length=48 

#create 48 8-bit symbolic vectors 
flag = [claripy.BVS("x{}".format(i), 8) for i in range(input_length)] 
#concatenate them and form one long bit vector 
flagg = claripy.Concat(*flag)
#pass the bit vector to the binary and create a new state at the default entry point of the program
state = proj.factory.entry_state(stdin=flagg)

#add extra contsrtaints to make sure the flag is printable
for charcater in flag:
    state.solver.add(character >= 0x20)
    state.solver.add(character < 0xff)

#start the simulation
simgr = proj.factory.simulation_manager(state)
print("Started exploring")

#find the state that prints "Access authorized" to stdout
simgr.explore(find=lambda s: b"Access authorized" in s.posix.dumps(1))
 
if len(simgr.found) > 0: # the constraints added are satisfiable 
    found = simgr.found[0] # create a new state 
    valid_flag = found.solver.eval(flagg, cast_to=bytes) #evalute the value of the symbolic bit vector 'flagg'
    print(valid_flag)
else: 
    print("Unsatisfiable")
```

# Flag
``` flag{actually_3_instructions:_subleq,_ret,_int3} ```
