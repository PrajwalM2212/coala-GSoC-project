# coala-GSoC-project

### Action System

coala can run in many modes - normal, non-interactive, json or format mode But all these modes call `run_coala` . Then it collects cofiguration using `gather_configuration`. Finally it runs `execute_section`.  In `execute_section`  `instantiate_processes` runs the bears on files. Then `process_queues` takes care of applying actions and printing. 

The project shall not focus on any of the regions of code handled by the above mentioned methods. 
Infact only the `print_result` method in `process_queues` takes care of the `actions` system in coala.  The `print_result` function takes care of auto applying actions based on the default actions specified in the config files using `autoapply_actions` function. This is where actions can be seen in action. But also if coala is run in interactive mode, then actions are used again in a lot of places in `ConsoleInteraction.py` file. 

In both of these places `action.is_applicable(result, file_dict, file_diff_dict)` is used to check whether a action is applicable for a given result. Also this `is_applicable` is called for every result. `Next Generation action` system is about solving this and providing the ability for bears to specify the list of actions they can perform through the results.


### Solution

1. Changing the `Result` and `HiddenResult` classes. Providing for a new argument called `actions` in the `__init__` methods of both `Result` and `HiddenResult` classes. `actions` argument shall hold the list of actions that a bear can apply. Also `from_values` method of `Result` class must be changed to accomodate for the new argument. This should make bears capable of emitting `Results` with list actions they can apply.

2. Within the `Result Action` class, the static method `is_applicable` can be removed. Also all the `is_applicable` defined in the currently available actions have to be removed. 

3.After modifying the result class to accommodate the actions argument.
  a. All bears can have IgnoreResultAction, OpenEditorAction and DoNothingAction<br>
  b. If bears generate a Diff , then they can also have ShowPatchAction, ShowAppliedPatchesAction, ApplyPatchAction<br>
  c. If external linters are used to create a bear , then depending on output format, the actions can be added by modifying        Linter.py file.  If output format is corrected or unified-diff then it can have the six actions else it will have the          normal 3 actions(IgnoreResultAction, OpenEditorAction and DoNothingAction)<br>
     This is an example of how the new result can be yielded
     

        yield Result(self,

             'The code does not comply to PEP8.',

             affected_code=(diff.range(filename),),

             diffs={filename: diff},

             actions = ['IgnoreResultAction', 'OpenEditorAction' , 'DoNothingAction', 'ApplyPatchAction', 'ShowPatchAction',  'ShowAppliedPatchesAction'])


4. In `autoapply_actions` function change the logic from 
  ```python
  applicable = action.is_applicable(result, file_dict, file_diff_dict)
  if applicable:
    action().apply_from_section(result,
                                file_dict,
                                file_diff_dict,
                                section)
  ```

to 
  ```python 
  if action in result.actions:
    action().apply_from_section(result,
                                file_dict,
                                file_diff_dict,
                                section)
  ```
Similar changes has to be done in `acquire_actions_and_apply`, `print_result` functions in `ConsoleInteraction.py` file. 

5. As part of all the steps add/modify tests and documentation. The project is test and documentation heavy.
