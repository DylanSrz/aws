## Paso 3

```
PS C:\Users\Cohorte 5\Desktop\dylan dev\aws\iam> aws iam attach-user-policy --user-name dev-dylan --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
PS C:\Users\Cohorte 5\Desktop\dylan dev\aws\iam> aws iam attach-user-policy --user-name dev-dylan --policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
PS C:\Users\Cohorte 5\Desktop\dylan dev\aws\iam> aws iam list-attached-user-policies --user-name dev-dylan
{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonEC2ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess"
        },
        {
            "PolicyName": "ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/ReadOnlyAccess"
        }
    ]
}
```