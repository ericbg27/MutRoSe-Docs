# MutRoSe-Docs
This repository contains all of the files needed in order to get instructions for modelling with the MutRoSe framework as well as examples of mission models.

## Repository Structure
This repository contains two higher level folders:

 - Docs: This is the folder that contains documents with instructions on the mission specification part of the framework
 - Examples: This folder contains several examples of missions models
 
## Instruction Video
You can find an example-based explanation video [here](https://youtu.be/rXl_m7FJgOo).

## Goals
After finishing the reading of the documents, you will:

 - Learn how to model the Goal Model and HDDL Domain specification for the MutRoSe framework
 - Learn how to run the decomposition binary and understand its output

## Overview

### Goal Model
The Goal Model in the MutRoSe framework is based on the Contextual Runtime Goal Model (CRGM) proposed in [1]. Its modelling can be performed in the [piStar-GODA](https://pistar-goda.herokuapp.com/) framework.

### HDDL
The HDDL Domain specification uses a subset of the one proposed in [2] with the addition of some structures. Its modelling can be performed using any text editor of your preference.

## 1. Goal Model for MRS Missions

### Goal Properties

#### Context Conditions

Context conditions are represented by the *CreationCondition* property and has two possible types:

 - Condition type context: Represents a condition to achieve some goal. The format is: 

> assertion condition "[Condition]"

where [Condition] represents the condition to be achieved. The currently accepted format for this condition is *[var].[attr]* where [var] is a variable name and [attr] is some boolean attribute

 - Trigger type context: Represents a list of events that trigger some goal. The format is:

> assertion trigger "[Events_List]"

where [Events_List] is a comma-separated list of event names

#### Monitors and Controls Syntax

Monitors and Controls syntax is a syntax inherited from [3]. These properties are declared with these names in the Goal Model and are defined as an OCL [4] list of variable declarations. A variable declaration in OCL is as follows:

> v1 : T1

where v1 is the variable name and T1 is the variable type. In the Monitors property types are optional since the variable must have been previously defined in some goal Controls property

#### Group and Divisible Attributes

The Group and Divisible properties are related to execution constraints. These properties have these same names when declared in the Goal Model. The Group property is a concept taken from [5], where:

 - True states that the goal's children can be executed by multiple robots
 - False states that the goal's children must be executed by a single robot

The Divisible property is only considered when Group is set to true. It has two possibilities:

 - True states that the goal's children can be executed by different teams of robots
 - False states that the goal's children must be executed by the same team of robots

It is important to note the order of priority of execution constraints:

 - Group False  has the higher priority
 - Group False and Divisible True has the lowest priority
 - Group True and Divisible True indicates no constraint

An example of how these priorities of execution constraints affects the decomposition process is shown in the Instructions for the MutRoSe framework PDF document

#### Goal Types

Goals can have three types, which are:

 - Query: These are goals that instantiate variables. It makes use of an OCL select statement in the *QueriedProperty* property, which syntax is shown below:

	> c->select(x:xt | $\phi$)

	where c is a collection variable called *Queried Variable*, x is non-collection variable called *Query Variable*, xt is the *Query Variable Type* and $\phi$ is a condition which is simply called *Condition*. There is a special value for c which represents the higher level of world knowledge that is called *world_db*.

 - Achieve: These are goals that state a condition to be achieved after the end of the children's execution. It has a special condition called *AchieveCondition* which can be a simple condition over some variable attribute or a forall OCL statement, which syntax is shown below.

	> c-forAll(x:xt | $\phi$)

	where c is a collection variable called *Iterated Variable*, x is a non-collection variable called *Iteration Variable*, xt is an optional type called *Iteration Variable Type* and $\phi$ is the condition to be achieved for each variable in c which is simply called *Condition*.

 - Perform: This is the default type, which has no special property attached to it.

### Task Attributes
Tasks have three possible attributes to be declared, which are shown below.

#### Location
The Location property is declared with the same name in the Goal Model. Its value must be a single variable which:

 - Can be of a collection type (Sequence) or non-collection type
 - Must be of a location type declared in the configuration file

#### Params
The Params property is declared with the same name in the Goal Model. Its value is a list of variables, which are mapped to HDDL variables in the configuration file.

#### RobotNumber
The RobotNumber property is declared with the same name in the Goal Model. Its value can be:

 - A single number (example: 4)
 - A range indicating the minimum and maximum number of robots to execute the task (example: [2,6])

## 2. HDDL Domain Definition
The HDDL Domain definition in the MutRoSe framework uses a subset of the HDDL language defined in [2]. New additions to the language were:

 - The capabilities domain attribute, declared with the :capabilities keyword
 - The :required-capabilities keyword for actions, which defines the list of capabilities needed to perform an action
 - The robot and robotteam native types. The robot type defines a single robot and the robotteam type defines a variable number of robots

These new additions are exemplified in the Figures below:

![HDDL1](https://user-images.githubusercontent.com/28356832/127876056-6b7330d0-9e97-4220-93bf-265dc3893070.png)

![HDDL2](https://user-images.githubusercontent.com/28356832/127876048-45320390-2e0e-4c6b-9954-070ce9d69c40.png)

There was also the addition of function declarations using the :functions keyword as in PDDL, from which HDDL is derived. Further information on the HDDL Domain specification can be found in the Instructions for the MutRoSe framework PDF document

## 3. Configuration File
The configuration file can be in XML or JSON format. In this file we define:

 - Information about the world knowledge file
 - Information about the output file
 - The high-level location types
 - The type mappings, which map types from the Goal Model to type in HDDL
 - The variable mappings, which map variables in task definitions (Location and Parameters properties) to variables in the task HDDL definition
 - The semantic mappings, which map world knowledge record attributes to predicates defined in the HDDL Domain definition

## 4. World Knowledge
The only accepted type for the world knowledge is an XML file. In this file we have our records of pre-defined types and their attributes. This knowledge is used to initialize the world state, which is used throughout the decomposition process

## How To Execute

### Linux
Using the linux-based binary MRSDecomposer file, one must simply run the following command in a Linux terminal in order to generate the decomposition of the mission:

> ./MRSDecomposer [PATH_TO_HDDL_FILE] [PATH_TO_GOAL_MODEL_FILE] [PATH_TO_CONFIGURATION_FILE]

### Additional options
In both versions there are two optional command-line options that can be used:

 - -v: This will generate a verbose output which shows intermeadiate results. This output is most suitable for debugging when adding a new feature
 - -p: This will generate a pretty-printed output of the valid mission decompositions. This output is most suitable for checking the obtained results of the decomposition when simply using the binary to decompose some mission
 - -h: This will generate iHTNs [6] for the valid mission decompositions. Note that this is experimental work and may not work under certain examples!

## Mantainers
Eric Gil - github.com/ericbg27
 

## References
[1] Mendonça, Danilo Filgueira, et al. "GODA: A goal-oriented requirements engineering framework for runtime dependability analysis." _Information and Software Technology_ 80 (2016): 245-264.

[2] Höller, Daniel, et al. "HDDL: An extension to PDDL for expressing hierarchical planning problems." _Proceedings of the AAAI Conference on Artificial Intelligence_. Vol. 34. No. 06. 2020.

[3] Van Lamsweerde, Axel. "From system goals to software architecture." _International School on Formal Methods for the Design of Computer, Communication and Software Systems_. Springer, Berlin, Heidelberg, 2003.

[4] OCL, OMG. Object Constraint Language (OCL), Version 2.4. 2014.

[5] Torreño, Alejandro, et al. "Cooperative multi-agent planning: A survey." _ACM Computing Surveys (CSUR)_ 50.6 (2017): 1-32.

[6] Lesire, Charles, et al. "A distributed architecture for supervision of autonomous multi-robot missions." Autonomous Robots 40.7 (2016): 1343-1362.
