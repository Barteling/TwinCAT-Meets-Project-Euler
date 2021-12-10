# TwinCAT meets Project Euler
Welcome at the open source repository TwinCAT meets project Euler!  
This repository contains a TwinCAT project written in Structured Text which solves [Project Euler](https://projecteuler.net/) problems.  
I created this repository because I thought it would be fun to see how far _**we**_ can come with Structured Text.  
But mostly because I hope that we learn some cool ST tips and tricks from each other while solving the problems. We perhaps might need some additional math functions/libraries as well..

## How does it work
The project is a TwinCAT stand alone PLC project. The PLC project contains a base function block named '[FbProblemSolverBase](ProjectEuler/POUs/Problems/Base/FbProblemSolverBase.TcPOU)' which contains the base logic. The results are verified with [TCUnit](https://tcunit.org) in the main program 'PRG_TEST'.

#### FbProblemSolverBase
FbProblemSolverBase is loosely base on PLCOpens ETRIG pattern and implements some logic to control states like 'Busy' and 'Done' and has a 'Execute' property to start the problem solving process. The results are returned with a STRING property called: 'Result'.

#### Problem solver function blocks
All new problem solving function blocks should inherit from FbProblemSolverBase. In the new problem solving function block base simply call the base function block:
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
## How can I run it
This project is a stand alone project. To run it download the project and either in de same solution or in a different solution create a TwinCAT XAE project matching your hardware. Import the stand alone project as described [here](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/4278074763.html&id=) and you should be ready to run the PLC project!

## Can I contribute
Yes! The purpose of this project is to have some fun with Structured Text and learn from each other. Everyone is welcome to solve a problem.
To solve a Project Euler problem follow these steps:
 1. Clone this repository
 2. Add a new function block which inherits from FbProblemSolverBase.
 3. Pick your problom from [Project Euler](https://projecteuler.net/) and implement your solution in the method Cyclic action.
 4. Add your function block to the test sequence in PRG_TEST
 5. Verify that all tests pass.
 6. Open a pull request with your additions.
