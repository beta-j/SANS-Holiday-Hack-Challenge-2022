# OBJECTIVE 5 - Recover the Cloud Ring #
## 5B - Trufflehog Search ##

### OBJECTIVE : ###
>Use Trufflehog to find secrets in a Git repo.  Work with Jill Underpole in the Cloud Ring for hints.  What’s the name of the file that has AWS credentials?
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 5B</summary>
  
>-	If you want to look at an older code commit with git, you can `git checkout CommitNumberHere`.
>-	You can search for secrets in a Git repo with `trufflehog git https://some.repo/here.git`.
</details>

#  

## PROCEDURE : ##
First, we use Trufflehog to look through the git repository:
```console
~$ trufflehog git https://haugfactory.com/asnowball/aws_scripts.git
```

The first result it gives us indicates that AWS credentials have been detected in a file called **`put_policy.py`** but it’s not in the latest commit.
```console
Found unverified result 🐷🔑❓
Detector Type: AWS
Decoder Type: PLAIN
Raw result: AKIAAIDAYRANYAHGQOHD
Line: 6
Commit: 106d33e1ffd53eea753c1365eafc6588398279b5
File: put_policy.py
Email: asnowball <alabaster@northpolechristmastown.local>
Repository: https://haugfactory.com/asnowball/aws_scripts.git
Timestamp: 2022-09-07 07:53:12 -0700 -0700
```

So we can clone the repository using `git clone https://haugfactory.com/asnowball/aws_scripts.git` and have a look at the contents of `put_policy.py` with the commit hash we got from Trufflehog by running `git show 106d33e1ffd53eea753c1365eafc6588398279b5`  inside the `aws_scripts` folder we just cloned.  There we can see the aws access id and key in plaintext.  
```diff --git a/put_policy.py b/put_policy.py
index d78760f..f7013a9 100644
--- a/put_policy.py
+++ b/put_policy.py
@@ -4,8 +4,8 @@ import json
 
 iam = boto3.client('iam',
     region_name='us-east-1',
-    aws_access_key_id=ACCESSKEYID,
-    aws_secret_access_key=SECRETACCESSKEY,
+    aws_access_key_id="AKIAAIDAYRANYAHGQOHD",
+    aws_secret_access_key="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",
```

All we need to do know is configure these credentials by running `aws configure` and running `aws sts get-caller-identity`.
 
From the output we can see that our username is `haug` and we can plug this in the next command to get the list of attached user policies:
```console
$ aws iam list-attached-user-policies --user-name haug
```
We now know that our policy’s arn is `arn:aws:iam::602123424321:policy/TIER1_READONLY_POLICY`.

We can use this to get the policy assigned to our user by running:
```console
$ aws iam get-policy --policy-arn arn:aws:iam::602123424321:policy/TIER1_READONLY_POLICY
```

Similarly to view the default version of this policy we can use:
```console
$ aws iam get-policy-version --policy-arn arn:aws:iam::602123424321:policy/TIER1_READONLY_POLICY --version-id v1
```

Now to list inline user policies associated with the username `haug` we use:
```console
$ aws iam list-user-policies –user-name haug
```

With this we see that the policy Name is `S3Perms` and we can use this to retrieve haug’s user policy:
```console
$ aws iam get-user-policy –user-name haug –policy-name S3Perms
```

This discloses the name of a S3 bucket called `smogmachines3`.  We can use this to list objects in this bucket by using:
```console
$ aws s3api list-objects –bucket smogmachines3
```

To list the lambda privileges that attached user policy is providing us with, we can use:
```console
$ aws lambda list-functions
```

This shows us a function called `smogmachine_lambda`.  Let’s see whether this is directly accessible through a public URL:
```console
$ aws lambda get-function-url-config --function-name smogmachine_lambda
```

And sure enough – the function is directly accessible from ``https://rxgnav37qmvqxtaksslw5vwwjm0suhwc.lambda-url.us-east-1.on.aws/ ``


 
