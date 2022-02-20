Debugging infeasibilities in MESSAGEix
======================================
In case the ixmp solve command used to call GAMS or GAMS invoked directly via the command line or its 
IDE does not exit with status 0, there is a high likelihood that the reason is an infeasibility in the 
MESSAGEix model. To assess the reason for the non-zero exit status and eventually to debug a model 
infeasibility, it is necessary to investigate the GAMS in more detail. The GAMS .log and .lst files 
provide information about the reason for the non-zero exit code. These files (MESSAGE_run.log/lst) 
can be found in the directory where the message_ix repository is installed (see [MESSAGEix tutorial, slide 22/23](../presentations/MESSAGEix_tutorial_2019-10-21.pdf).
Compilation errors can be found in the .lst file by searching for entries beginning with “****” while 
infeasibilities can be found by searching for the keyword "INFES".

There are different command-line options that allow switching on various debugging options in MESSAGEix 
can be found in the model_setup.gms file in the MESSAGE subfolder. The options can either be activated 
by running the model via the MESSAGE_master.gms file or using the command line option via MESSAGE_run.gms
(see {MESSAGEix documentation](https://message.iiasa.ac.at/en/stable/debugging.html#identification-of-infeasibilities)).
The following settings exist: 
- calibration "*": activates that the solution is printed to the .lst file and that up to 1e6 columns 
(varibales) and rows (equations) of the problem are being written to the .lst file (a warning: for larger 
models the process of writing this diagnostic output to disk may take a long time and occupy tens of GB 
of disk space).
In addition, there are a number of relaxations for specific constraint blocks to identify infeasibilities 
during model development/calibration which are named SLACK*. These 'slack' variables get added to constraints 
to relax them and add associated penalty costs to the objective function.
- commodity balance-related infeasibilities
  - SLACK_COMMODITY_EQUIVALENCE "*": relax the commodity balance equation
- capacity-related infeasibilities
  - SLACK_CAP_NEW_BOUND_UP "*": relax upper bounds on new build capacity 
  - SLACK_CAP_NEW_BOUND_LO "*": relax lower bounds on new build capacity 
  - SLACK_CAP_TOTAL_BOUND_UP "*": relax upper bounds on total capacity 
  - SLACK_CAP_TOTAL_BOUND_LO "*": relax lower bounds on total capacity 
  - SLACK_CAP_NEW_DYNAMIC_UP "*": relax upper bounds on dynamic upscaling constraint on capacity 
  - SLACK_CAP_NEW_DYNAMIC_LO "*": relax lower bounds on dynamic upscaling constraint on capacity 
- activity-related infeasibilities
  - SLACK_ACT_BOUND_UP "*": relax upper bounds on activity 
  - SLACK_ACT_BOUND_LO "*": relax lower bounds on activity
  - SLACK_ACT_DYNAMIC_UP "*": relax upper bounds on dynamic upscaling constraints on activity
  - SLACK_ACT_DYNAMIC_LO "*": relax lower bounds on dynamic upscaling constraints on activity
- user-defined relations-related infeasibilities
  - SLACK_RELATION_BOUND_UP "*": relax upper bounds on user-defined relations 
  - SLACK_RELATION_BOUND_LO "*": relax lower bounds on user-defined relations
- land scenario-related infeasibilities (only for models with land-use emulator)
  - SLACK_LAND_SCEN_UP "*": relax upper bounds on land use scenario
  - SLACK_LAND_SCEN_LO "*": relax lower bounds on land use scenario
  - SLACK_LAND_TYPE_UP "*": relax upper bounds on land type
  - SLACK_LAND_TYPE_LO "*": relax lower bounds on land type

When solving the model again with these slack variables activated the model should become feasible. Once
that is achieved, the levels of these slack variables can be inspected in the .lst or .gdx solution files.
The levels give an indication for how much the respective constraints need to be relaxed to get a feasible
solution.

