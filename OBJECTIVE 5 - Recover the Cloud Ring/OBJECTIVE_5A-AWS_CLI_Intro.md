# OBJECTIVE 5 - Recover the Cloud Ring #
## 5A - AWS CLI Intro ##

### OBJECTIVE : ###
>Try out some basic AWS command line skills in this terminal.  Talk to Jill Underpole in the Cloud Ring for hints.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 5A</summary>
  
>-	In the AWS command line (CLI), the Secure Token Service or [STS](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sts/get-caller-identity.html) has one very useful function.
</details>

#  

## PROCEDURE : ##
Just follow the on-screen instructions and read the help files:

```console
elf@3634594c4105:~$ aws help
elf@3634594c4105:~$ 
elf@3634594c4105:~$ aws configure
AWS Access Key ID [None]: AKQAAYRKO7A5Q5XUY2IY 
AWS Secret Access Key [None]: qzTscgNdcdwIo/soPKPoJn9sBrl5eMQQL19iO5uf
Default region name [None]: us-east-1
Default output format [None]: 
elf@3634594c4105:~$ aws sts get-caller-identity
{
    "UserId": "AKQAAYRKO7A5Q5XUY2IY",
    "Account": "602143214321",
    "Arn": "arn:aws:iam::602143214321:user/elf_helpdesk"
}
elf@3634594c4105:~$
```

