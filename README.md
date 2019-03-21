# coala-GSoC-project

### Action System

coala can run in many modes - normal, non-interactive, json or format mode But all these modes call `run_coala` . Then it collects cofiguration using `gather_configuration`. Finally it runs `execute_section`.  In `execute_section`  `instantiate_processes` runs the bears on files. Then `process_queues` takes care of applying actions and printing. 

The project shall not focus on any of the regions of code handled by the above mentioned methods. 
Infact only the `print_result` method in `process_queues` takes care of the `actions` system in coala.  The `print_result` function takes care of auto applying actions based on the default actions specified in the config files using `autoapply_actions` function. This is where actions can be seen in action. But also if coala is run in interactive mode, then actions are used again. 

In both of these places `action.is_applicable(result, file_dict, file_diff_dict)` is used to check whether a action is applicable for a given result. Also this `is_applicable` is called for every result. Next Generation action system is about solving this and providing the ability for bears to specify the list of actions they can perform through the results.


