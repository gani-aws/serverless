#Commit1
 - simple hello world app created.
    - by default 
        - It Creates one s3 bucket to upload Lambda bucket
        - It Creates one Log group to provide logs
        - One IAM role with assumeRole for Lambda execution with inclusive of policies for logs:CreateLogStream,logs:PutLogEvents on Loggroup resource above mentioned
        - Creates Lambda Function and its Version resource
#commit2
  - Override default values
    - default upload Lambda bucket
    - IAM role with existing for lambda and for cfn service role.
    - #TODO log group 