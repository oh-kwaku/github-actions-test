# github-actions-test

You use "needs" to enable a to wait untle another job(s) completes.
- with parameter on an action enables us to supply values to the action.

 
# Encrypt /Decrypt File
  - To decrypt file 
    - use command: gpg --symmetric --cipher-algo AES256 filename
  - TO decrypt file 
    - use gpg --batch --quiet --yes --decrypt --passphrase="${{secrets.PASS_PHRASE}}" --output $HOME/secret.json secret.json.gpg
    
# Expressons & Contexts
 
# 18. Using Functions in Expressions 
  We can use expressions in ${{expression}}. eg ${{2+2}}, ${{toJson('string')}},${{endsWith('Hello','lo')}}

# 19. Job status functions
  This returns information about status of jobs
  We can use "if" property of job to define literal that when true, the job will run. NB: Github already treats value of "if" property as expression so there's no need to do ${{expression}}
  "if" can also be added to steps. NB: When a step fails, subsequent steps of that job will not run. However, we can use "if" in a step to let it run even if the previous steps failed by using 
  job status function called "failure". failure() returns true if the previous step failed. we can use "if: always()" to let a step to always run even if previous steps failed or not.

# 20. Continue on Error & Timeout miniutes
  Add and set "continue-on-error" property  to "true" on a task to enable it continue even if there's an error
  Use "time-out" property to cancel a job or step after the specified value. get time-out: 360; 360 is default value

# 21. Using the setup-node Action:
  > We can change the default version of node js for a step using the setup node actions
  
# 22. Creating a Matrix for Running a Job with Different Environments
  If we want to run a job different times on different versions of lets say node js ie. versions 6,8,10, instead of creating separate steps and repeating the same code, we can use matrix to do that. You add matrix to a job like
 ```   
    jobs:
      node-version:
        strategy:
          matrix:
            os: [macos-latest, windows-latest, ubuntu-latest]
            node_version: [6,8,10]
          fail-fast: true # set fail-fast to true to immediately exit the job if execution fails for the current matrix iteration;
        max-parallel: 0  # use max-parallel to limit the number of jobs that are running in the current matrix
         runs-on: ${{matrix.os}}
      steps:
      # first we want to look at the installed node js on the vm
        - name: log node node-version
        run: node -v

        # we are setting up node version 6 for this step
        - uses: actions/setup-node@v1
        with:
          node-version: ${{matrix.node_version}}
          # see version after setup
        - name: log node version again
        run: node -v
```
In the above code we specified variables for the matrix property to be used in jobs. ie. node_verion, os etc. Jobs will be run for each of the values of the variables of the matrix property. The example code above will run nine (9) times. ie. one for  macos-lates running node versions 6,8,10 then for windows running the specified versions of node js the same for unbuntu.

# 23. Including and excluding Matrix configurations
We can skip running  jobs for some specified values of the variables of a matrix using the "exclude" property of a matrix like so
```
      exclude:
          - os: ubuntu-latest
            node_version: 6
          - os: macos-latest
            node_version:8
```
Here when the previous job in (22) will skip when 
- 1. os is ubuntu-latest and node_version is 6
- 2. When os is macos-latest and node_version is 8

We can use "include" property to define variables that are only available for some specified values of the matrix's variables. 

NB: Include is not use to add additional values for a matrix variable value. ie. "include" property it used to add configurations for a certain matrix
``` 
include: 
  - os: ubuntu-latest
    node_version:8
    is_ubuntu_8: "true"
```
the above code declares variable is_ubuntu_8 which is only available as environment variable with the matrix iterations has unbuntu-lates for os  and node_version is 8. The "is_ubuntu_8" value is true when the specified condition is met otherwise it will be null or empty string
        
# 24. Using docker containers in jobs
container property of job is used to setup and run container for a job in the format
```
container:[docker hub username]:image
```
so if we want to run node on alpine we will do
```
conainter:node:13.5.0-alpine3.10
```
we can also pass container property as object in the format
```
conatiner:
  image: 13.5.0-alpine3.10
  env: some environment variables
  ports: array of ports we want to expose in the container
  volumes: volumes we want to map in our  virtual machine
  options: options we want to use in docker create
```
** NB**: when we use containers, the job steps are run inside of the container and not in the job runner's virtual machine

We can  also run containers for each step in our job. Containers can also be run as services
## Running containers as services
If we want to run containers as services we can use the services property of jobs. in this case we will not use the container property. You specify that in the format:
```
services: 
  app:
  build: [use image that is published on docker hub here]
  ports:
    - 3001:3000
  mongo:
    image: mongo
    ports:
      - "27001:27001"
 ```
## Running docker containers in Individual steps
We can also run docker container for each job step using the "uses" property of the step. eg.
```
steps:
  - name: use docker in the step
    uses: docker://node:12.14.1-alpine3.10
```
we values to the docker parameters 
```
...
uses: docker://node:12.14.1-alpine3.10
with: 
  entrypoint: `/bin/echo'
  args: ''Hello world'

        
        