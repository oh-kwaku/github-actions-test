# github-actions-test

You use "needs" to enable a to wait untle another job(s) completes.
- with parameter on an action enables us to supply values to the action.

 
> Encrypt /Decrypt File
  - To decrypt file 
    - use command: gpg --symmetric --cipher-algo AES256 filename
  - TO decrypt file 
    - use gpg --batch --quiet --yes --decrypt --passphrase="${{secrets.PASS_PHRASE}}" --output $HOME/secret.json secret.json.gpg
    
> Expressons & Contexts
 
> 18. Using Functions in Expressions 
  We can use expressions in ${{expression}}. eg ${{2+2}}, ${{toJson('string')}},${{endsWith('Hello','lo')}}

> 19. Job status functions
  This returns information about status of jobs
  We can use "if" property of job to define literal that when true, the job will run. NB: Github already treats value of "if" property as expression so there's no need to do ${{expression}}
  "if" can also be added to steps. NB: When a step fails, subsequent steps of that job will not run. However, we can use "if" in a step to let it run even if the previous steps failed by using 
  job status function called "failure". failure() returns true if the previous step failed. we can use "if: always()" to let a step to always run even if previous steps failed or not.

>20. Continue on Error & Timeout miniutes
  Add and set "continue-on-error" property  to "true" on a task to enable it continue even if there's an error
  Use "time-out" property to cancel a job or step after the specified value. get time-out: 360; 360 is default value

>21. Using the setup-node Action:
  > We can change the default version of node js for a step using the setup node actions
  
>22. Creating a Matrix for Running a Job with Different Environments
  > If we want to run a job different times on different versions of lets say node js ie. versions 6,8,10, instead of creating separate steps and repeating the same code, we can use matrix to do that. You add matrix to a job like
    >jobs:
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

  In the above code we specified variables for the matrix property to be used in jobs. ie. node_verion, os etc. Jobs will be run for each of the values of the variables of the matrix property. The example code above will run nine (9) times. ie. one for  macos-lates running node versions 6,8,10 then for windows running the specified versions of node js the same for unbuntu.