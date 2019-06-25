# Notificação de Pipeline na AWS

```yaml
  MySNSTopic:
    Type: AWS::SNS::Topic
    Properties:
      Subscription:
      - Endpoint:
          Ref: EmailAddress
        Protocol: email
```


CloudWatch Event Rule

The CloudWatch Event Rule defines the conditions under which a CloudWatch Event is triggered. In the snippet below, it triggers the SNS Topic as a target when the CodePipeline source is in a FAILED state. In addition, it provides the text that’s displayed in the SNS Topic so that the user knows which action to take.

EventRule:
    Type: "AWS::Events::Rule"
    Properties:
      Description: "EventRule"
      EventPattern:
        source:
        - aws.codepipeline
        detail-type:
        - CodePipeline Pipeline Execution State Change
        detail:
          state:
          - FAILED
      State: "ENABLED"
      Targets:
        -
          Arn:
            Ref: "MySNSTopic"
          Id: "OpsTopic"
          InputTransformer:
            InputTemplate: '"The Pipeline  has failed. Go to https://console.aws.amazon.com/codepipeline/home?region=us-east-1#/view/" '
            InputPathsMap:
              pipeline: "$.detail.pipeline"


---
Description: CodePipeline notifications.
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
  EmailAddress:
    Description: Lista de distribuição para notificação da Pipeline
    Type: String
Resources:
  EventRule: 
    Type: "AWS::Events::Rule"
    Properties: 
      Description: "EventRule"
      EventPattern: 
        source:
        - aws.codepipeline
        detail-type:
        - CodePipeline Pipeline Execution State Change
        detail:
          state:
          - FAILED
      State: "ENABLED"
      Targets: 
        - 
          Arn: 
            Ref: "MySNSTopic"
          Id: "PipelineNotificationTopic"
          InputTransformer:
            InputTemplate: '"The Pipeline <pipeline> has failed. Go to https://console.aws.amazon.com/codepipeline/home?region=us-east-1#/view/<pipeline>" '
            InputPathsMap:
              pipeline: "$.detail.pipeline" 
  MySNSTopic:
    Type: AWS::SNS::Topic
    Properties:
      Subscription:
      - Endpoint:
          Ref: EmailAddress
Protocol: email