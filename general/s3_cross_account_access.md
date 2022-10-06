https://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/


1.    Create an ```S3 bucket``` in ```Account A```.

2.    Create an ```IAM role``` or user in ```Account B```.

3.    Give the ```IAM role in Account B``` permission to download (GET Object) and upload (PUT Object) objects to and from a specific bucket. Use the following IAM policy to also grant the IAM role in Account B permissions to call PutObjectAcl, granting object permissions to the bucket owner:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": "arn:aws:s3:::AccountABucketName/*"

        }
    ]
}
```



4.    Configure the ```bucket policy for Account A``` to grant permissions to the IAM role or user that you created in ```Account B```. Use this bucket policy to grant a user the permissions to GetObject and PutObject for objects in a bucket owned by Account A:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::AccountB:user/AccountBUserName"
            },
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::AccountABucketName/*"
            ]
        }
    ]
}
```
