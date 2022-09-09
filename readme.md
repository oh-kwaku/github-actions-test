# github-actions-test


 
> Encrypt /Decrypt File
  - To decrypt file 
    - use command: gpg --symmetric --cipher-algo AES256 filename
  - TO decrypt file 
    - use gpg --batch --quiet --yes --decrypt --passphrase="${{secrets.PASS_PHRASE}}" --output $HOME/secret.json secret.json.gpg
    
> Expressons & Contexts
 
> 18. Using Functions in Expressions 
  We can use expressions in ${{expression}}. eg ${{2+2}}, ${{toJson('string')}},${{endsWith('Hello','lo')}}