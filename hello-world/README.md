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
    - #TODO log group , check the URL https://serverless.com/framework/docs/providers/aws/guide/resources#aws-cloudformation-resource-reference

#Commit3 
    - Using Variables
      - using environment variables 
        - set environment variable in system
        - using in yaml
          ${env:FUNC_PREFIX}
        - using in handler.js
          process.env.NAME
      - Referencing CLI options
        - using in yaml
          ${opt:stage}
        - using inside handler.js, #TODO
      - Referencing variables in other files
         - custom: ${file(../myCustomFile.yml)} # You can reference the entire file
           - schedule: ${self:custom.globalSchedule} # This would also work in this case
         - schedule: ${file(../myCustomFile.yml):globalSchedule} # Or you can reference a specific property
      - Reference variables in Javascript Files
        - // scheduleConfig.js
            module.exports.rate = () => {
              // Code that generates dynamic data
              return 'rate (10 minutes)';
            }
        - // config.js
            module.exports = (serverless) => {
              serverless.cli.consoleLog('You can access Serverless config and methods as well!');

              return {
                property1: 'some value',
                property2: 'some other value'
              }
            }
        - You can also return an object and reference a specific property. Just make sure you are returning a valid object and referencing a valid property:
        - // myCustomFile.js
              module.exports.schedule = () => {
                // Code that generates dynamic data
                return {
                  ten: 'rate(10 minutes)',
                  twenty: 'rate(20 minutes)',
                  thirty: 'rate(30 minutes)'
                };
              }
          -If your use case requires handling dynamic/async data sources (ie. DynamoDB, API calls...etc), you can also return a Promise that would be resolved as the value of the variable:
    - resources:
        Resources: ${file(cloudformation-resources.json)}
        -the corresponding resources which are defined inside the cloudformation-resources.json file will be resolved and loaded into the Resources section.
    - Nesting Variable References
      - custom:
        myFlexibleArn: ${env:${opt:stage}_arn}
    - overwriting variables
       myStage: ${opt:stage, self:provider.stage} - What this says is to use the stage CLI option if it exists, if not, use the default stage (which lives in provider.stage). So during development you can safely deploy with serverless deploy
   - using Custom Variable Syntax -#TODO

   - referencing cloudFormation Otuputs
      - you can reference CloudFormation stack output values as the source of your variables to use in your service with the cf:stackName.outputKey syntax. 
      - ${cf:another-service-dev.functionPrefix}-hello
   - Referencing S3 Objects
      - You can reference S3 values as the source of your variables to use in your service with the s3:bucketName/key syntax. 
      -  name: ${s3:myBucket/myKey}-hello
   - Reference Variables using the SSM Parameter Store(Systems Manager)
      - ${ssm:/path/to/service/myParam}-hello -   the value for the SSM Parameters will be looked up and used to populate the variables.
      - You can also reference encrypted SSM Parameters, of type SecureString, using the extended syntax, ssm:/path/to/secureparam~true.
