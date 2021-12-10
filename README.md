# TwinCAT meets Project Euler
Welcome at the open source repository TwinCAT meets project Euler!  
This repository contains a TwinCAT project which solves [Project Euler](https://projecteuler.net/) problems.  
I created this repository because I thought it would be fun to see how far _**we**_ can come with Structured Text.  
But mostly because I hope that we learn some cool ST tips and tricks from each other while solving the problems. We perhaps might need some additional math functions/libraries as well..

## How does it work
The project is a TwinCAT stand alone PLC project. The PLC project contains a base function block '[FbProblemSolverBase](ProjectEuler/POUs/Problems/Base/FbProblemSolverBase.TcPOU)' to solve the problems. The results are verified with [TCUnit](https://tcunit.org) in the main program 'PRG_TEST'.

#### FbProblemSolverBase
FbProblemSolverBase implements some logic to control states like 'Busy' and 'Done' and has a 'Execute' property to start the  problem solving process. The results are returned with a STRING property called: 'Result'.

#### Problem solver function blocks
All new problem solving function blocks should inherit from FbProblemSolverBase. In the function block base simply call the base class:
```
Super^();
```

The problem solving logic should be implemented in the overriden method: 'CyclicAction'. This action will be called cyclic until the output parameter: 'complete' is set the true.
```
METHOD PROTECTED CyclicAction
VAR_OUTPUT
    complete : BOOL;
    result : STRING;
END_VAR
```
```
// Logic
result := '123';
complete := TRUE;
```
In case a function block needs specific cleanup after the process has completed, override the method 'ResetAction' and implement any cleanup actions here. This may take more than one cycle. As with the 'CyclicAction' this is controlled with the output parameter 'Complete'.

#### Testing
This project uses TCUnit to verify the results. Because the structure of the problem solving function blocks is the same, the testing can be done with one function block: '[FbProblemTester](ProjectEuler/POUs/Tests/FbProblemTester.TcPOU)'. This function block uses FB_init to acquire the function block to test and the expected result as STRING.  
To add a new problem in the test sequence just add them in the variable list of 'PRG_TEST':
```
PROGRAM PRG_TEST
VAR
    _problem1 : Problem1;
    _problem1Test : FbProblemTester(_problem1, '233168');
    _problem2 : Problem2;
    _problem2Test : FbProblemTester(_problem2, '4613732');
END_VAR
```

#### Project structure
```
/- Project
/- - POUs
/- - - Problems
/- - - - Base
/- - - - - FbProblemSolverBase
/- - - - - IProblemSolver
/- - - - Problem1 (FB)
/- - - - Problem2 (FB)
/- - - - ........ (FB)
/- - - - Add your new problem here (FB)
/- - - Tests
/- - - - FbProblemTester (FB)
/- - - PRG_TEST (PRG)  
```
