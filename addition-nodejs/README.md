- serverless.yaml
    - # You can pin your service to only deploy with a specific Serverless version
        # Check out our docs for more details
        # frameworkVersion: "=X.X.X"
    - services
        service:
            name: myService
            awsKmsKeyArn: arn:aws:kms:us-east-1:XXXXXX:key/some-hash # Optional KMS key arn which will be used for encryption for all functions #TODO
    - Provider
        name: aws
        runtime: nodejs6.10
        stage: dev # Set the default stage used. Default is dev
        region: us-east-1 # Overwrite the default region used. Default is us-east-1
        profile: production # The default profile to use with this service
        memorySize: 512 # Overwrite the default memory size. Default is 1024
        timeout: 10 # The default is 6 seconds. Note: API Gateway current maximum is 30 seconds
    
    #  Stack specific

        # Environment specific
        environment: # Service wide environment variables
        serviceEnvVar: 123456789

        stackTags: # Optional CF stack tags
        key: value
        cfnRole: arn:aws:iam::XXXXXX:role/role # ARN of an IAM role for CloudFormation service. If              specified, CloudFormation uses the role's credentials
        stackPolicy: # Optional CF stack policy. The example below allows updates to all resources except deleting/replacing EC2 instances (use with caution!)
                - Effect: Allow
                Principal: "*"
                Action: "Update:*"
                Resource: "*"
                - Effect: Deny
                Principal: "*"
                Action:
                    - Update:Replace
                    - Update:Delete
                Condition:
                    StringEquals:
                    ResourceType:
                        - AWS::EC2::Instance
            vpc: # Optional VPC. But if you use VPC then both subproperties (securityGroupIds and subnetIds) are required
                securityGroupIds:
                - securityGroupId1
                - securityGroupId2
                subnetIds:
                - subnetId1
                - subnetId2
            notificationArns: # List of existing Amazon SNS topics in the same region where notifications about stack events are sent.
                - 'arn:aws:sns:us-east-1:XXXXXX:mytopic'

                iamManagedPolicies: # Optional IAM Managed Policies, which allows to include the policies into IAM Role
            - arn:aws:iam:*****:policy/some-managed-policy
        iamRoleStatements: # IAM role statements so that services can be accessed in the AWS account
            - Effect: 'Allow'
            Action:
                - 's3:ListBucket'
            Resource:
                Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                    - Ref: ServerlessDeploymentBucket
    #Rest Specific
        endpointType: regional # Optional endpoint configuration for API Gateway REST API. Default is Edge.
        apiKeys: # List of API keys to be used by your service API Gateway REST API
            - myFirstKey
            - ${opt:stage}-myFirstKey
            - ${env:MY_API_KEY} # you can hide it in a serverless variable
        apiGateway: # Optional API Gateway global config
            restApiId: xxxxxxxxxx # REST API resource ID. Default is generated by the framework
            restApiRootResourceId: xxxxxxxxxx # Root resource ID, represent as / path
            restApiResources: # List of existing resources that were created in the REST API. This is required or the stack will be conflicted
            '/users': xxxxxxxxxx
            '/users/create': xxxxxxxxxx

    #Packaging
        package: # Optional deployment packaging configuration
        include: # Specify the directories and files which should be included in the deployment ackage
            - src/**
            - handler.js
        exclude: # Specify the directories and files which should be excluded in the deployment ackage
            - .git/**
            - .travis.yml
        excludeDevDependencies: false # Config if Serverless should automatically exclude dev dependencies in the deployment package. Defaults to true
        artifact: path/to/my-artifact.zip # Own package that should be used. You must provide this              file.
        individually: true # Enables individual packaging for each function. If true you must provide               package for each function. Defaults to false

        deploymentBucket:
        name: com.serverless.${self:provider.region}.deploys # Deployment bucket name. Default is generated by the framework
        serverSideEncryption: AES256 # when using server-side encryption
