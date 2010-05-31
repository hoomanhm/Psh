Psh
===

Psh is a Java implementation of the Push programming language and of PushGP. Push is a stack-based language designed for evolutionary computation, specifically genetic programming. PushGP is a genetic programming system that evolves programs in Push. More information about Push and PushGP can be found [here](http://hampshire.edu/lspector/push.html).

Psh is licensed under the GNU General Public License. This is v0.3 of Psh.

Getting Started with Git
========================

To Get Psh
----------
    $ git clone git://github.com/jonklein/Psh.git
    $ cd Psh

To Update Psh
-------------
    $ cd Psh
    $ git pull

Git References
--------------
- [Pro Git](http://progit.org/book/) - A wonderful source for those new to git.
- [GitHub Help](http://help.github.com/) - GitHub help pages.

Getting Started with Psh
========================

Building Psh
------------
After getting Psh with get, build the package:

    $ make

Using PshGP
----------
To run PshGP on a sample problem:

    $ java PshGP gpsamples/intreg1.pushgp

This problem uses integer symbolic regression to solve the equation y = 12x^2 + 5. Other sample problems are available, with descriptions, in 'gpsamples/'. For example, 'intreg.2.pushgp' uses integer symbolic regression to solve the factorial function, and 'regression1.pushgp' uses float symbolic regression to solve y = 12x^2 + 5.

Using PshInspector
------------------
PshInspector allows you to examine every step of a Psh program as it executes. To run PshInspector on a sample psh program:

    $ java PshInspector pushsamples/exampleProgram1.push

This push file runs the psh program '(2994 5 integer.+)' for 100 steps after pushing the inputs '44, 22, true, 17.76'. Other sample psh programs are available in 'pushsamples/'.

Psh In More Detail
==================

Configuration Files
-------------------
PshGP runs are setup using configuration files which have the extension '.pushgp'. These files contain a list of parameters in the form of 

    param-name = value

The following parameters must be defined in the configuration file, given with example values:

    interpreter-class = org.spiderland.Psh.Interpreter
    individual-class = org.spiderland.Psh.PushGPIndividual
    inputpusher-class = org.spiderland.Psh.InputPusher
    problem-class = org.spiderland.Psh.IntSymbolicRegression
    
    max-generations = 200
    population-size = 1000
    execution-limit = 150
    max-points-in-program = 100
    
    tournament-size = 7
    trivial-geography-radius = 10
    
    mutation-percent = 30
    crossover-percent = 55
    simplification-percent = 5
    simplify-flatten-percent= 20
    
    reproduction-simplifications = 25
    report-simplifications = 100
    final-simplifications = 1000
    
    fair-mutation-range = .3
    max-random-code-size = 40
    
    test-cases = ((1 1) (2 2) (3 6) (4 24) (5 120) (6 720))
    instruction-set = (registered.exec registered.boolean integer.% integer.* integer.+ integer.- integer./ integer.< integer.= integer.> integer.dup integer.flush integer.pop integer.rot integer.stackdepth integer.swap)

The following parameters are optional:

    output-file = out.txt
    mutation-mode = fair
    push-frame-mode = pushstacks

PshInspector Files
------------------
In order to inspect the execution of a program, PshInspector takes a push program file with the extension '.push'. After every step of the program, the stacks of the interpreter are displayed. The input file contains the following, separated by new lines:

- Program: The Psh program to run
- ExecutionLimit: Maximum execution steps
- Input(optional): Any inputs to be pushed before execution, separated by spaces. The inputs are pushed in the order in which they are given. Note: Only int, float, and boolean inputs are accepted.

Problem Classes
---------------
PshGP uses problem classes, implemented as Java classes, to determine certain aspects of the run, such as how to compute fitness values. The choice of problem class determines how test case data is interpreted, and which stacks are used for test case input and output. In addition, certain inherited methods in both GA.java and PushGP.java may be overwritten for further customization.

Psh comes with a few standard problem classes. The following problem classes are currently implemented:

- FloatSymbolicRegression.java: Maps an input floating point value to an output floating point value. Error value is computed as the difference between the desired output value and the top value on the float stack.
- IntSymbolicRegression.java: Maps an input integer value to an output integer value. Error value is computed as the difference between the desired output value and the top value on the integer stack.
- CartCentering.java: Maps two input floats (position and velocity) to a boolean value that represents a forward or backward force applied to a cart. The error is the amount of time required to stop the cart at the origin. For more information, see the problem class file.

In order to perform runs for other types of problems, you can implement your own custom problem classes. Please note the following:

- You will likely want to implement the InitFromParamenters method, which can be used to set up test cases. If so, make sure to also call its parent method.
- In PshGP, the term fitness actually refers to error values, which means that lower values are considered more fit and that 0.0 represents no error. The EvaluateTestCase method must be implemented by any problem class, and should compute an individual's fitness, with lower values being better.
- The InitInterpreter method must be implemented by all problem classes though many times this method is simply left empty.
- There are other optional methods that can be overwritten or extended in the GA.java and PushGP.java classes. For example, the CartCentering.java problem class implements the Success method in order to override the conditions that PushGP uses to identify a successful run.

Changelog
=========

Major Changes since v0.3:
-------------------------
- Added new integer and float instructions: abs, neg, sin, cos, tan, max, min.
- Added new boolean instructions: and, or, xor, not.
- Added problem class for the cart centering problem (CartCentering.java), an optimal control problem.

Major Changes since v0.2:
-------------------------
- All instructions have been converted into lower case to match Schush and other implementations.
- An input stack was added, which holds all inputs. It has the following instructions:
    1. input.index - Pops n off of the integer stack and pushes input[n] onto corresponding stack. If integer stack is empty, acts as a no-op.
    2. input.makeinputsN - Creates N instructions called 'input.in0', 'input.in1', ..., 'input.in(N-1)'
    3. input.inall - For all n in 0 to input.size, push input[n] onto the corresponding stack.
    4. input.inallrev - For all n in input.size to 9, push input[n] onto the corresponding stack.
    5. input.stackdepth - Puts size of stack on integer stack.
- In config files, you can now include all instructions for a certain type using 'registered.type' (e.g. 'registered.integer' or 'registered.stack').
- Implemented auto-simplification, which is used during generation and final reports. Auto-simplification may also be used as a genetic operator along with mutation and crossover.

Major Changes since v0.1:
-------------------------
- Added problem classes for integer symbolic regression (IntSymbolicRegression.java) and integer symbolic regression without an input instruction (IntSymbolicRegressionNoInput.java).
- Fixed 'code' and 'exec' stack iteration functions, which were not executing correctly according to Push 3.0 standards.
- PshGP now displays the error values for the best program during the generation report.
- PshInspector was created to inspect interpreter stacks of push programs as they execute. This can be used to catch errors and trace executions. To run, see Using PshInspector section above.
