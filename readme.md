# github-actions-test

You use "needs" to enable a to wait untle another job(s) completes.

 
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

>21. Using the setup-node Action
  