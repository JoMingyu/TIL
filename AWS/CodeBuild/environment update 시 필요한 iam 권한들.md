```
{
	"Statement": [
		{
			"Action": [
				"iam:CreatePolicy",
				"iam:CreatePolicyVersion",
				"iam:DeletePolicy",
				"iam:DeletePolicyVersion"
			],
			"Effect": "Allow",
			"Resource": [
				"arn:aws:iam::{accountId}:policy/service-role/CodeBuildBasePolicy-{projectName}",
				"arn:aws:iam::{accountId}:policy/service-role/CodeBuildVpcPolicy-{projectName}"
			]
		},
		{
			"Action": [
				"codebuild:UpdateProject"
			],
			"Effect": "Allow",
			"Resource": [
				"arn:aws:codebuild:{region}:{accountId}:project/{projectName}"
			]
		},
		{
			"Action": [
				"iam:PassRole"
			],
			"Effect": "Allow",
			"Resource": [
				"arn:aws:iam::{accountId}:role/{roleName}"
			]
		}
	],
	"Version": "2012-10-17"
}
```
