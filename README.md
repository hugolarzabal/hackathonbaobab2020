# hackathonbaobab2020

The problem consists of scheduling all jobs by deciding when and in which mode the job is executed.
There are two types of resources: (R) renewables have an availability that is recovered each period of time, (N) non-renewables have an availability for the whole planning horizon.
The objective is to reduce the finishing time (start time + duration) of the last job.

The instances for the problem are found here:

http://www.om-db.wi.tum.de/psplib/getdata_mm.html

On the `data` directory of this repository we have copied the smallest instances.
For the format of the solution, since there is no example that we know of, we'll be using the one in `data/solutions/c15mm/c1564_9.output.json`

Below are the instructions to use the helper functions and checker (they are optional).
To understand the format of the input data file, you can check how we parse it in python in the function `Instance.from_mm(path)` in the file`core/instance.py`

## Installation

python>=3.5 is needed. I'm assuming a windows installation.

```
cd hackathonbaobab2020
python -m venv venv
venv/Scripts/activate
pip install -r requirements.txt
```

## How to add a new solver

These are the steps to add a solver and make it compatible with the command line and the python functions:

1. Add a file inside the `solvers` directory with a subclass of `core.experiment.Experiment` that implements, at least, the `solve()` method *with the same argument names*.
1. Your `solve` method needs to return an integer with the status of the solving process. Current options are `{4: "Optimal", 2: "Feasible", 3: "Infeasible", 0: "Unknown"}`.
1. Edit the `solvers/__init__.py` to import your solver and edit the `solvers` dictionary by giving your solver a name.

**Note**: Everything that your solver needs should be inside the `solvers` directory (you can put more than one file). Do not edit the files outside the `solvers` directories with code from your solver!

## Command line

The command line app has three main ways to use it.

### To execute instances

To get all possible commands just run::

    python main.py solve-scenarios --help

This assumes you have downloaded the zip `j30.mm.zip` of input instances and you have stored it in the `data` directory. It solves instance `j301_1.mm`.
    
    python main.py solve-scenarios --directory=data --scenario=j30.mm.zip --solver=default --instance=j301_1.mm --no-test

You can also solve multiple scenarios or multiple instances by passing the `--instances` and `--scenarios` options. Just be careful with the string format::

    python main.py solve-scenarios --directory=data --scenarios='["c15.mm.zip", "c21.mm.zip", "j10.mm.zip", "j30.mm.zip", "m1.mm.zip", "m5.mm.zip", "n0.mm.zip", "n1.mm.zip", "n3.mm.zip", "r1.mm.zip", "r4.mm.zip", "r5.mm.zip"]' --solver=default

Finally, if you pass the `zip` option you create a nice little zip at the end.

The output format is always the same::

    solver_name/scenario_name/instance_name/(input, output, options)

### To get statistics from a solution

You first need to have a zip with the results you want to get statistics from. For this, the easiest is to pass the `zip` option to the `solve-scenarios` function above.

Then you do something like::

    python main.py export-table --path=data/default.zip --path_out=data_default.csv

And this generates a table in a csv with several columns: scenario,name,objective,solver,time,errors

### To convert from one type of instance to another

This can be particularly useful for the AIMMS people. And is not yet available.

## Code

We use the following helper objects:

1. `Instance` to represent input data.
2. `Solution` to represent a solution.
3. `Experiment` to represent input data+solution.
4. `Algorithm(Experiment)` to represent a resolution approach.

The last one (4) includes an example of a solution approach. It schedules one job at a time while respecting the sequence. Apparently, it passes the tests.

There are helper functions to read and write an instance and a solution to/from a file.

An small example of how to use the existing code is available in `example/test_script.py`.

