# Installation

```ad-summary
https://hub.docker.com/r/tryton/tryton

Alternatively, you can deploy and download with a compose file. This is what we should do incase we need to add more dependencies associated with tryton. 
```

An `.env` file must be created in the same directory as the `.yml` compose file to create the variables. 

> [!example] `DB_PASSWORD`, `PASSWORD`, `DB_NAME`

> [!danger] We need to ***ensure*** that the `.env` file has the proper permissions so passwords cannot be accessed in cleartext
> 

