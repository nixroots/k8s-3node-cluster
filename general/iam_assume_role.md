# assume-role
Returns a set of temporary security credentials that you can use to access Amazon Web Services resources that you might not normally have access to. These temporary credentials consist of an access key ID, a secret access key, and a security token. Typically, you use ```AssumeRole``` within your account or for cross-account access

When you create a role, you create two policies: A role trust policy that specifies who can assume the role and a permissions policy that specifies what can be done with the role. You specify the trusted principal who is allowed to assume the role in the role trust policy.

To assume a role from a different account, your Amazon Web Services account must be trusted by the role. The trust relationship is defined in the role's trust policy when the role is created. That trust policy states which accounts are allowed to delegate that access to users in the account.

A user who wants to access a role in a different account must also have permissions that are delegated from the user account administrator. The administrator must attach a policy that allows the user to call ```AssumeRole``` for the ARN of the role in the other account.

To allow a user to assume a role in the same account, you can do either of the following:

  - Attach a policy to the user that allows the user to call ```AssumeRole``` (as long as the role's trust policy trusts the account).
  - Add the user as a principal directly in the role's trust policy.

```
[root@ip-10-192-55-12 ~]# cat assume-role.sh
#!/bin/bash
ACCOUNT=$1  #account ID
export http_proxy=http://x.x.x.x:80   #export proxy if needed
CREDENTIALS=`aws sts assume-role --role-session-name "CrossAccountAccess" --role-arn arn:aws:iam::$ACCOUNT:role/assume-role`

export AWS_ACCESS_KEY_ID=`echo $CREDENTIALS | jq -r '.Credentials.AccessKeyId'`
export AWS_SECRET_ACCESS_KEY=`echo $CREDENTIALS | jq -r '.Credentials.SecretAccessKey'`
export AWS_SESSION_TOKEN=`echo $CREDENTIALS | jq -r '.Credentials.SessionToken'`
```

IAM Role Sample (in Target Account (B))
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::$ACCOUNT-ID-A:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {}
        }
    ]
}
```


## Creating or editing the policy
A policy that grants a user permission to assume a role must include a statement with the Allow effect on the following:

The ```sts:AssumeRole``` action

The Amazon Resource Name (ARN) of the role in a Resource element

This is as shown in the following example. Users that get the policy (either through group membership or directly attached) are allowed to switch to the specified role.

Note
If Resource is set to *, the user can assume any role in any account that trusts the user's account. (In other words, the role's trust policy specifies the user's account as Principal). As a best practice, we recommend that you follow the principle of least privilege and specify the complete ARN for only the roles that the user needs.

The following example shows a policy that lets the user assume roles in only one account. In addition, the policy uses a wildcard (*) to specify that the user can switch to a role only if the role name begins with the letters Test.
```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::account-id:role/Test*"
  }
}
```
