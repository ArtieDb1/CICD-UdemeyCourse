# Pipelines

## What are pipelines
**Analogy**
Imagine we have a car factory and we have a car at the assembly line.
In order to build a car from scratch, you need the chasis to be there before the engine and the wheels are added. 
After the car is built, then you have to test the code

In Gitlab CI we will build and test our car


# tutorial

1. Creating the git project
2. To build a pipeline we need a file to define the pipeline
3. Write a script

## Step 2 - New file

name a new file file ".gitlab-ci.yml"

yml is a file that defines a pipeline. It is a "data serialization language used by gitlabCI.
Serialization is a way of breaking a structure down into components that can be brought together and used later.
We are not breaking down any code here but we are just making the config files human readable, which is where the serializable term comes from.

The YML file will be stored at the root of the project.

## Step 3 -Build the car Writing the script exactly as shown

Press new file and paste the follwing script in there
After that you must commit the changes to gitlab
note that "build the car" is a job declaration!

**NOTE: Indentation is achieved through 2 spaces not tabs like in python**
```
build the car:
  script:
    - mkdir build
    - cd build
    - touch car.txt
    - echo "chassis" > car.txt
    - echo "engine" > car.txt
    - echo "wheels" > car.txt

```
After saving and committing the file to gitlab, Gitlab will automatically execute the first Job
The test -f command is a shell test operator used to check whether a specified file exists and is a regular file
Finally if not specified a job will automatically be assigned to a test stage

![Alt text](<Images/1RunningFirstPipeline.png>)

GitLab will execute the script using gitLab Runner 
![Alt text](<Images/2RunningScript.png>)

## Testing the car with the script
Reopen the file
Append the following to the end of the script:
```
test the car:
  script:
    - test -f build/car.txt
    - cd build
    - grep "chassis" car.txt
    - grep "engine" car.txt
    - grep "ewheels" car.txt
```


Gitlab will try and execute both jbs at the same time which is what we dont want.
We need tin define what are called stages which will make gitlab start doing jobs chronologically.
At the top of the file youll have to define these stages like so:
```
stages:
  - build
  - test
```

Each job should also look like so (after defining the stages):
```
stages:
  - build
  - test


build the car:
  stage: build
  script:
    - mkdir build
    - cd build
    - touch car.txt
    - echo "chassis" > car.txt
    - echo "engine" > car.txt
    - echo "wheels" > car.txt

test the car:
  stage: test
  script:
    - test -f build/car.txt
    - cd build
    - grep "chassis" car.txt
    - grep "engine" car.txt
    - grep "wheels" car.txt

```

But Wait Look:
![Alt text](<Images/3Final Pipeline.png>)

### Why it failed
The 2 jobs are independent from eachother
After job one creaed and saved data to a txt file, the file was removed because it wasnt told to stay by the pipeline
So after Job 1 was done, the environement wasnt done
Then Job 2 had no car.txt file.
So we need to take that file from job 1 after it has been executed

Now you must give it an artifact. This will be what survives after the job is executed and destroyed. Enter the follwing

```
  artifacts:
    paths:
      - build/
```
So that your script looks like this:
```
stages:
  - build
  - test


build the car:
  stage: build
  script:
    - mkdir build
    - cd build
    - touch car.txt
    - echo "chassis" > car.txt
    - echo "engine" > car.txt
    - echo "wheels" > car.txt
  artifacts:
    paths:
      - build/

test the car:
  stage: test
  script:
    - test -f build/car.txt
    - cd build
    - grep "chassis" car.txt
    - grep "engine" car.txt
    - grep "wheels" car.txt

```

Here we have just added the whole folder as an artifact
Everything in the build stage should be kept somewhere.
Commit the changes and see what happens!
Failed again!

This is because the echo statements have one arrow which means replace the all lines withing the file with the following with the following:
**Example:**
```
echo "chassis" > car.txt
```
This will replace the file with the chassis command rather than appending it!
To fix it, you just add another Greater-Than sign to the statement 

```
echo "chassis" > car.txt
```
your final script will now look like this:

```
  artifacts:
    paths:
      - build/
```
So that your script looks like this:
```
stages:
  - build
  - test


build the car:
  stage: build
  script:
    - mkdir build
    - cd build
    - touch car.txt
    - echo "chassis" >> car.txt
    - echo "engine" >> car.txt
    - echo "wheels" >> car.txt
  artifacts:
    paths:
      - build/

test the car:
  stage: test
  script:
    - test -f build/car.txt
    - cd build
    - grep "chassis" car.txt
    - grep "engine" car.txt
    - grep "wheels" car.txt

```

## Ending
You can now download the artifacts but they will not be a part of the project. They also will not be commited as changes.
From today, you know how to have 2 jobs within a pipeline 


## Blockers:

You may encounter a problem where your GitLab CI pipeline is not running.

Make sure that:

- You have named your file .gitlab-ci.yml (and not gitlab-ci.yml or .gitlab-ci.yaml)

- the pipeline file is in the root of the project and not inside a directory

- Auto DevOps is NOT enabled
- 