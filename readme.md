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
```
# 28. Creating our own executable file and running it in our steps
we can pass a path to entrypoint to run our custom executable file. Here we are passing path to our script "script.sh". since we are accessing file we need to clone our repo using
```
uses: actions/checkout@v1
```
NB: In order to make our script file executable we need to run the command "chomd +x [path to our script file] before committing the file
```
chomd +x script.sh
```
NB: failure to execute the above command before commit will cause the following error in the container
```
docker: Error response from daemon: failed to create shim: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "./script.sh": permission denied: unknown
```
the linux "chmod" command enables permissions to the script file

# 29. Sending a slack message using a docker container
we will be using the docker container "https://hub.docker.com/r/technosophos/slack-notify" to send slack messages.
the following environment variables are required
```
SLACK_WEBHOOK:
SLACK_MESSAGE: 
```
to create slack webhook go to "https://api.slack.com/apps" then click on "create an App"

Click on incoming webhooks and turn it on

Click on add new webhook to workspace

Select the channel to post messages to then click allow
        
NB: Slack will reject sending the message if you assign the webhook link directly to "SLACK_WEBHOOKls
". we will have to put it in secret

# 30. Creating a ReactJS Boilerplate Application
When running npm test command set "CI=true" on terminal to avoid interactive terminal in our workflow.
We used "surge" to deploy static website.
NB: Installing surge requires permissions. use sudo 
```
sudo install -global surge   
```

# 31 Using prettier to Check for Code formmating Rules
- Go to "https://prettier.io/playground/" then make formatting configurations you want. 
- click on copy configuration. 
- create and paste the configuration a file in the project that you want the formatting rules to be applied to with name ".prettierrc"
- Also create ".prettierignore" and add files or folders that need to be ignored
Execute 
```
npx prettirer --check "**/*.js"
``` 
to check for files that do not conform to the formatting configurations

execute
```
npx prettirer --write "**/*.js"
```
to allow prettier format files. NB "**/*.js" is pattern
- scripts section of package.json add
```
 "format":"prettier --check \"**/*.js\""
 "format":"prettier --check \"**/*.js\""
 ```
 # 34 Let's discuss our workflow plan
   - Feature branch
     - Install dependencies
     - check code formatting
     - Run automated tests
     - Update code coverage report as an artifact
     - Cache dependencies: this reduces time to install dependencies next time the workflow is run
   - Develop branch
     - Install dependencies
     - Check code formatting
     - Run automated tests
     - Upload code coverage as artifact
     - Build project
     - Upload build as an artifact
     - Deploy to staging server
     - cache depencies
   -  Master branch
     - Check code formatting
     -  Run automated tests
     - Updload code covarage as an artifact
     - Build project
     - Upload build as an artifact
     - Create a release
     - Deploy to production server
     - upload coverage to codecov
     - cache dependencies

  ## Automations
      - Job Failure -> Create Issue
      - Issue created -> Send a slack message
      - Release created -> Send a slack Message
# 35 Setting up our repository
You can "CODEOWNERS" file in ".github" folder to specify which files or extensions are owned by which user.
Code owners are required to review files

# 36 Setting the develop  pull request workflow

# 37 Creating the develop merge pull requests workflow

# 38 Caching NPM Dependencies
  we can use the actions/cache@v1 action to cache dependencies. Caches are saved under keys. Use the key param to specify a key for your cache. The key will also be used to retrieve that cache. 

  ```
    uses: actions/cache@v1
    with: 
      path: ~/.npm
      key: myCacheKey
  ```

  the cache key can be static (as in the above code) or dynamic (using an expression as below)

   ```
    uses: actions/cache@v1
    with: 
      path: ~/.npm
      key: ${{runner.os}}node-${{hashFiles('**/package-lock.json')}}
  ```

  in the above code, were generating a dynamic cache key when   package.lock.json file changes by hashing that file and using the hash value as part of the key and also we are including the operating system that the runner is being run on. 
    We can also use restore-key param to let github know which keys to restore packages at if  not caches are found with the main key (the value of the key param).
  ```
   with: 
     key:  ${{runner.os}}node-${{hashFiles('**/package-lock.json')}}
     restore-key: |
      ${{runner.os}}-node-
  ```

# Uploading Artifacts in our workflows
 Artifacts are files that are generated by  a workflow and that we need to make available for download.
 use the actions "actions/upload-artifact@v1" to upload artifacts. 
 ```
  - name: updload artifacts
    uses: actions/upload-artifact@v1
      with:
        name: any name you want
        path: the path of the artifacts to upload 
 ```
there's also action in the actions repository to download artifacts in your workspace ie. 
```
    actions/download-artifact
```

# 42 Semantic versioning & Conventional commits
 repo: https://github.com/semantic-release/semantic-release/blob/master/docs/usage/plugins.md
  When we create a new release of our project how are going to know which part of semantic versioning that we need to update? 
    ans: We would know from our commit messages. We use a convention called conventional commits. Conventional commits helps us know if a bug is fixed or added a new feature or had a breaking change and by following the convention in our commit messages, we are able to know how to update our semantic versioning.
Format of conventional commit
  <type>[optional scope]: <desscription>
    type: can be [fix, feature, docs, etc]
    scope: it is optional and describes the scope that a change affected. eg. Cart, employee etc
    description: any message you like
    body: it is optional
    footage: it is optional
eg
```
fix(cart): changed our endpoint
BREAKING CHANGE: changed cart endpoint
closes issue #2
```
  type: fix
  scope: cart
  body: "BREAKING CHANGES" signals semantic version to change the major part of the current version eg. 2.1.2 will be 3.0.0

another example
```
feat(auth): added facebook authentication
```

  type: feat, meaning feature
  scope: auth
in this example the type is feature, meaning a feature is added and there's no breaking change so only the minor part of the semantic version will be changed eg. 2.1.1 will be 2.2.0

other examples
```
docs(auth): facebook authentication added
```

```
ci: added a new workflow
```
we can also specify type of "ci" to show that we committed something that affected ci
## Installing Semantic version ()
 There's a package called semantic release which provides cli  that we can run CI on our workflow. It can take care of analyzing workflow and generating a new release with a version based on messages in our commit messages
 To install
 ```
npm install --save-dev semantic-release
 ```
After installing create a new file called 'release.config.js'
  in the file export an object that will contain the configuration  

## Running semantic-release in our Workflows.
  once we have our semantic release configured, we need to run it on our CI.
```
 - name: create a release
        run: npm semantic-release
        env: 
          GITHUB_TOKEN:${{secrets.GITHUB_TOKEN}}
        
```

# 45. Deploy to production when pushing to Master
Add step to deploy when pushed to master

# 46. Uploading Code Coverage Reports to Codecov 
  We need to create an account on www.codecov.io  then connect our github account in order to be able to choose the repo that we want code coverage on.
  A token will be generated by codecov after choosing a repo. 
  Create a new github repo secret in the chosen repo with name "CODECOV_TOKEN" then paste the generated token in it.

# 47. Quick Note:
 In the next lecture we are going to install a package called husky. When you install it, please use husky@4 instead of husky. This is because there's currently a new versing (v5) which has a different configuration setup and it's also only available to use for sponsors: https://github.com/typicode/husky

# 48 Validating our commit messages with commitlint & Commitizen.
Because codecov takes clues from our commit messages in order to determine if a commit is a bug fix, new feature etc, we ought to validate our commit messages to make sure that they contain clues that codecov understands otherwise any commit message can be used which might make codecov fail.
  In order to check if our commit messages follow conventional commit messages we need to intall a package called `commitlint`. repo: 
  github.com/conventional-changelog/commitlint. This package uses `husky`. 
    husky 
      Husky is a package that uses git hooks to allow us to run something before we commit or push or before we do anything that is related to git
To install 
```
npm install --save-dev @commitlint/config-conventional @commitlint/cli  husky
```
  After installing open package.json and add
```
 "husky":{
    "hooks":{
      "commit-msg":"commitlint -E HUSKY_GIT_PARAMS"
    }
  }
```

Now, we need to create a commit configuration file.
  run
```
echo "module.exports={extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```
  on the terminal. A file named `commitlint-config.js` will be created. 
  Test by executing git commit with invalid commit message. An error should be thrown.

## 48.2 Commitizen
  Commitizen helps us in creating commit messages by providing interactivity when we type `git commit`. Repo: `github.com/commitizen/cz-cli` .
  Cli utility: `http://commitizen.github.io/cz-cli`

  To install
```
npm install commitizen -g
```

Next, initialize your project to use the cz-conventional-changelog adapter by typing:

```
commitizen init cz-conventional-changelog --save-dev --save-exact
```

# 49. Sending a slack message when a new release is published
Create a github action job to send messages,  
  go to api.slack.com/apps then create a new app. Select the channel that you want messages to be sent to. Copy the curl url and paste it in the run commands of the job step. The webhook url copied should be a secret so create a repo secret and paste it in there then replace the url in the action step with the secret created. like so
  ```
  steps
    - name: send slack message
      run: |
         curl -X POST -H 'Content-type: application/json' --data '{"text":"This is a new release ${{github.event.release.tag_name}} is out. <${{github.event.release.html_url}}|check it out>"}' 
           ${{secretes.SLACK_WEBHOOK}}

  ```

# 50. Opening an Automated Issue when the Workflow Fails
We will need to create a step with an if condition 

```
- name: Issue on failed pull request
  if: failure() && github.event.name=='pull_request'
  run: |
  
```

# 51. Adding a status Badge in Readme.md
We will be add svg image as badge
The path of the badge image will be your github repos like so
```
![](https://github.com/kwakuemma37/empty_react/workflows/CI/badge.svg?branch=develop&event=push)
```
  