# PlantUmlテスト

```uml
@startuml
'Copyright 2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
'SPDX-License-Identifier: MIT (For details, see https://github.com/awslabs/aws-icons-for-plantuml/blob/master/LICENSE)

!define AWSPuml https://raw.githubusercontent.com/awslabs/aws-icons-for-plantuml/v14.0/dist
!include AWSPuml/AWSCommon.puml
!include AWSPuml/Groups/all.puml
!include AWSPuml/Compute/LambdaLambdaFunction.puml
!include AWSPuml/General/Documents.puml
!include AWSPuml/General/Multimedia.puml
!include AWSPuml/General/Tapestorage.puml
!include AWSPuml/General/User.puml
!include AWSPuml/MediaServices/ElementalMediaConvert.puml
!include AWSPuml/MachineLearning/Transcribe.puml
!include AWSPuml/Storage/SimpleStorageService.puml
skinparam shadowing false
' define custom group for Amazon S3 bucket
AWSGroupColoring(S3BucketGroup, #FFFFFF, AWS_COLOR_GREEN, plain)
!define S3BucketGroup(g_alias, g_label="Amazon S3 bucket") AWSGroupEntity(g_alias, g_label, AWS_COLOR_GREEN, SimpleStorageService, S3BucketGroup)

!procedure $stepnum($number) 
<back:black><color:white><b>$number</b></color></back>
!endprocedure

' Groups are rectangles with a custom style using stereotype - need to hide
hide stereotype
skinparam linetype ortho
skinparam rectangle {
    BackgroundColor AWS_BG_COLOR
    BorderColor transparent
}

rectangle "$UserIMG()\nUser" as user
AWSCloudGroup(cloud){
  RegionGroup(region) {
    S3BucketGroup(s3) {
      rectangle "$MultimediaIMG()\n\tvideo\t" as video
      rectangle "$TapestorageIMG()\n\taudio\t" as audio
      rectangle "$DocumentsIMG()\n\ttranscript\t" as transcript

      user -r-> video: $stepnum("1")\lupload
      video -r-> audio
      audio -r-> transcript
    }

    rectangle "$LambdaLambdaFunctionIMG()\nObjectCreated\nevent handler" as e1
    rectangle "$ElementalMediaConvertIMG()\nAWS Elemental\nMediaConvert" as mediaconvert
    rectangle "$TranscribeIMG()\nAmazon Transcribe\n" as transcribe
    
    video -d-> e1: $stepnum("2")
    e1 -[hidden]r-> mediaconvert
    mediaconvert -[hidden]r-> transcribe
    mediaconvert -u-> audio: $stepnum("3")
    transcribe -u-> transcript: $stepnum("4") 
    
    StepFunctionsWorkflowGroup(sfw) {
      rectangle "$LambdaLambdaFunctionIMG()\nextract audio" as sfw1
      rectangle "$LambdaLambdaFunctionIMG()\ntranscribe audio" as sfw2

      e1 -r-> sfw1: Start\nExecution
      sfw1 -r-> sfw2
      sfw1 -u-> mediaconvert
      sfw2 -u-> transcribe
    }
  }
}

@enduml
```

```uml
@startuml Sequence Diagram - Images
' Uncomment the line below for "dark mode" styling
'!$AWS_DARK = true

!define AWSPuml https://raw.githubusercontent.com/awslabs/aws-icons-for-plantuml/v16.0/dist
!include AWSPuml/AWSCommon.puml
!include AWSPuml/AWSExperimental.puml
!include AWSPuml/Compute/Lambda.puml
!include AWSPuml/ApplicationIntegration/APIGateway.puml
!include AWSPuml/General/Internetalt1.puml
!include AWSPuml/General/User.puml
!include AWSPuml/Database/DynamoDB.puml

'Hide the bottom boxes / Use filled triangle arrowheads
hide footbox
skinparam style strictuml

skinparam MaxMessageSize 200

participant "$UserIMG()\nUser" as user
box AWS Cloud
'Instead of using ...Participant(), native creole img tags can be used
participant "$APIGatewayIMG()\nCredit Card System\nAll methods are POST" as api << REST API >>
participant "$LambdaIMG()\nAuthorizeCard\nReturns status" as lambda << python3.9 >>
participant "PaymentTransactions\n$DynamoDBIMG()\nsortkey=transaction_id+token" as db << on-demand >>
endbox
participant "Authorizer\nReturns status and token\n$Internetalt1IMG()" as processor

'Use shortcut syntax for activation with colored lifelines and return keyword
user -> api++ $AWSColor(ApplicationIntegration): <$Callout_1> Process transaction\l<$Callout_SP> ""POST /prod/process""
api -> lambda++ $AWSColor(Compute): <$Callout_2> Invokes lambda with\l<$Callout_SP> cardholder details
lambda -> processor++ $AWS_COLOR_SQUID: <$Callout_3> Submit via API token\l<$Callout_SP> card number, expiry, CID
processor -> processor: Validate and\lcreate token
return status code, token
lambda ->> db: PUT transaction id, token
return status code,\rtransaction id
return status code
@enduml
```