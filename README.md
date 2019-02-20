# Example Pipelines

This repo contains some example pipelines to show off Spinnaker. Feel free to add your examples with a screenshot and description.

<!-- Install MarkdownTOC for sublime https://github.com/naokazuterada/MarkdownTOC -->
<!-- MarkdownTOC -->

- [IntegrationTest - Deploy Encrypted Image](#integrationtest---deploy-encrypted-image)
- [IntegrationTest - Suite Runner](#integrationtest---suite-runner)
- [Release Armory Spinnaker](#release-armory-spinnaker)

<!-- /MarkdownTOC -->



# IntegrationTest - Deploy Encrypted Image
At Armory, we use this pipeline to verify that Spinnaker can deploy an encrypted image baked outside of Spinnaker. We generate this image by using a script to copy a public ami into our AWS account, encrypt the EBS volume, then make an AMI from that.

This image is cached, but we have automated cleanup processes that will remove stale AMIs. Sometime in the future, the AMI will be deleted and the script will have to rerun.
![](https://cl.ly/4ff131782557/Screen%20Shot%202019-02-05%20at%2021.03.11.png)

<details>
<summary><b>Expand this to see the pipeline JSON</b></summary>

```json
{
  "keepWaitingPipelines": false,
  "lastModifiedBy": "link108",
  "limitConcurrent": true,
  "stages": [
    {
      "cloudProvider": "aws",
      "cloudProviderType": "aws",
      "comments": "Generated by using this https://gist.github.com/kevinawoo/607a48730f57012493ed238927a57cb2",
      "completeOtherBranchesThenFail": false,
      "continuePipeline": true,
      "failPipeline": false,
      "name": "Find Encrypted Image",
      "packageName": "woo-ubuntu Encrypted",
      "refId": "1",
      "regions": [
        "us-west-2"
      ],
      "requisiteStageRefIds": [],
      "tags": {},
      "type": "findImageFromTags"
    },
    {
      "clusters": [
        {
          "account": "aws-staging",
          "application": "armoryhellodeploy",
          "availabilityZones": {
            "us-west-2": [
              "us-west-2a",
              "us-west-2b",
              "us-west-2c"
            ]
          },
          "capacity": {
            "desired": 1,
            "max": 1,
            "min": 1
          },
          "cloudProvider": "aws",
          "cooldown": 10,
          "copySourceCustomBlockDeviceMappings": false,
          "ebsOptimized": false,
          "enabledMetrics": [],
          "freeFormDetails": "encrypted",
          "healthCheckGracePeriod": 600,
          "healthCheckType": "EC2",
          "iamRole": "SpinnakerInstanceProfile",
          "instanceMonitoring": false,
          "instanceType": "t2.nano",
          "keyPair": "armory-spinnaker-keypair",
          "loadBalancers": [],
          "moniker": {
            "app": "armoryhellodeploy",
            "detail": "encrypted",
            "stack": "encrypted"
          },
          "provider": "aws",
          "securityGroups": [],
          "spelLoadBalancers": [],
          "spelTargetGroups": [],
          "spotPrice": "",
          "stack": "encrypted",
          "strategy": "",
          "subnetType": "external (spinnaker)",
          "suspendedProcesses": [],
          "tags": {},
          "targetGroups": [],
          "targetHealthyDeployPercentage": 100,
          "terminationPolicies": [
            "Default"
          ],
          "useAmiBlockDeviceMappings": true
        }
      ],
      "completeOtherBranchesThenFail": false,
      "continuePipeline": false,
      "failPipeline": true,
      "name": "Deploy Encrypted Image",
      "overrideTimeout": true,
      "refId": "2",
      "requisiteStageRefIds": [
        "1",
        "9"
      ],
      "stageTimeoutMs": 600000,
      "type": "deploy"
    },
    {
      "cloudProvider": "aws",
      "cloudProviderType": "aws",
      "name": "Find Unencrypted Image",
      "packageName": "ami-59396769",
      "refId": "4",
      "regions": [
        "us-west-2"
      ],
      "requisiteStageRefIds": [],
      "tags": {},
      "type": "findImageFromTags"
    },
    {
      "clusters": [
        {
          "account": "aws-staging",
          "application": "armoryhellodeploy",
          "availabilityZones": {
            "us-west-2": [
              "us-west-2a",
              "us-west-2b",
              "us-west-2c"
            ]
          },
          "capacity": {
            "desired": 1,
            "max": 1,
            "min": 1
          },
          "cloudProvider": "aws",
          "cooldown": 10,
          "copySourceCustomBlockDeviceMappings": false,
          "ebsOptimized": false,
          "enabledMetrics": [],
          "freeFormDetails": "unencrypted",
          "healthCheckGracePeriod": 600,
          "healthCheckType": "EC2",
          "iamRole": "SpinnakerInstanceProfile",
          "instanceMonitoring": false,
          "instanceType": "t2.nano",
          "keyPair": "armory-spinnaker-keypair",
          "loadBalancers": [],
          "moniker": {
            "app": "armoryhellodeploy",
            "detail": "unencrypted",
            "stack": "encrypted"
          },
          "provider": "aws",
          "securityGroups": [],
          "spelLoadBalancers": [],
          "spelTargetGroups": [],
          "spotPrice": "",
          "stack": "encrypted",
          "strategy": "",
          "subnetType": "external (spinnaker)",
          "suspendedProcesses": [],
          "tags": {},
          "targetGroups": [],
          "targetHealthyDeployPercentage": 100,
          "terminationPolicies": [
            "Default"
          ],
          "useAmiBlockDeviceMappings": true
        }
      ],
      "completeOtherBranchesThenFail": false,
      "continuePipeline": false,
      "failPipeline": true,
      "name": "Deploy Unencrypted Image",
      "overrideTimeout": true,
      "refId": "5",
      "requisiteStageRefIds": [
        "4"
      ],
      "stageTimeoutMs": 600000,
      "type": "deploy"
    },
    {
      "allowDeleteActive": true,
      "cloudProvider": "aws",
      "cloudProviderType": "aws",
      "cluster": "armoryhellodeploy-encrypted-encrypted",
      "credentials": "aws-staging",
      "moniker": {
        "app": "armoryhellodeploy",
        "cluster": "armoryhellodeploy-encrypted-encrypted",
        "detail": "encrypted",
        "stack": "encrypted"
      },
      "name": "Cleanup: encrypted",
      "refId": "6",
      "regions": [
        "us-west-2"
      ],
      "requisiteStageRefIds": [
        "2"
      ],
      "retainLargerOverNewer": "false",
      "shrinkToSize": 0,
      "type": "shrinkCluster"
    },
    {
      "allowDeleteActive": true,
      "cloudProvider": "aws",
      "cloudProviderType": "aws",
      "cluster": "armoryhellodeploy-encrypted-unencrypted",
      "credentials": "aws-staging",
      "moniker": {
        "app": "armoryhellodeploy",
        "cluster": "armoryhellodeploy-encrypted-unencrypted",
        "detail": "unencrypted",
        "stack": "encrypted"
      },
      "name": "Cleanup: unencrypted",
      "refId": "7",
      "regions": [
        "us-west-2"
      ],
      "requisiteStageRefIds": [
        "5"
      ],
      "retainLargerOverNewer": "false",
      "shrinkToSize": 0,
      "type": "shrinkCluster"
    },
    {
      "cloudProvider": "aws",
      "cloudProviderType": "aws",
      "comments": "",
      "name": "Find Created Image",
      "packageName": "woo-ubuntu Encrypted",
      "refId": "9",
      "regions": [
        "us-west-2"
      ],
      "requisiteStageRefIds": [
        "10"
      ],
      "stageEnabled": {
        "expression": "${#stage('Create Encrypted Image if Not Found')['status'].toString() == 'SUCCEEDED'}",
        "type": "expression"
      },
      "tags": {},
      "type": "findImageFromTags"
    },
    {
      "command": "create_encrypted_image.sh",
      "comments": "Generated by using this https://gist.github.com/kevinawoo/607a48730f57012493ed238927a57cb2",
      "failPipeline": true,
      "name": "Create Encrypted Image if Not Found",
      "refId": "10",
      "repoBranch": "master",
      "repoUrl": "https://github.com/armory-io/integration-test-create-encrypted-image",
      "requisiteStageRefIds": [
        "1"
      ],
      "stageEnabled": {
        "expression": "${#stage('Find Encrypted Image')['status'].toString() == 'FAILED_CONTINUE'}",
        "type": "expression"
      },
      "type": "script",
      "user": "link108",
      "waitForCompletion": true
    }
  ],
  "triggers": [],
  "updateTs": "1542762319000"
}
```
</details>



# IntegrationTest - Suite Runner
At Armory, this is one of our integration test suite runners. It's a pipeline that will trigger other pipelines and track their status. We run this suite each time there's a change from Armory or OSS (Open Source Software (Spinnaker)) project.
![](https://cl.ly/b21a6f5203f8/Screen%20Shot%202019-02-05%20at%2020.47.01.png)

<details>
<summary><b>Expand this to see the pipeline JSON</b></summary>

```json
{
  "keepWaitingPipelines": false,
  "lastModifiedBy": "link108",
  "limitConcurrent": true,
  "notifications": [
    {
      "address": "#engineering-alerts",
      "level": "pipeline",
      "message": {
        "pipeline.failed": {
          "text": ""
        }
      },
      "type": "slack",
      "when": [
        "pipeline.failed"
      ]
    }
  ],
  "stages": [
    {
      "application": "armoryhellodeploy",
      "comments": "",
      "failPipeline": true,
      "name": "AWS Deploy Only",
      "pipeline": "e8d1b962-cee8-4fa6-8c88-a22ed8fe739f",
      "refId": "2",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "comments": "disabled because we're not adding new features or support to it anymore.",
      "failPipeline": true,
      "name": "Barometer Datadog Canary (Fail)",
      "pipeline": "3c95de65-2b8e-4c15-b1a3-2b4c9c7dedd8",
      "refId": "3",
      "requisiteStageRefIds": [],
      "stageEnabled": {
        "expression": "true == false",
        "type": "expression"
      },
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Kubernetes Deploy Only",
      "pipeline": "1c48548f-2212-4091-a675-712df9515093",
      "refId": "5",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "loadBalancers": [],
      "name": "Create/Modify ELB",
      "pipeline": "c819dff2-3b03-4909-96a8-13ba356c73fc",
      "refId": "8",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Webhook",
      "pipeline": "4a5c3e93-6a39-4280-9a5e-0217759e4344",
      "refId": "9",
      "requisiteStageRefIds": [],
      "statusUrlResolution": "getMethod",
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Jenkins Job",
      "pipeline": "e40f8e8b-d99a-40ac-99a5-1f0693470356",
      "refId": "10",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "comments": "This test seems flaky, sometimes it works, sometimes it doesn't...",
      "completeOtherBranchesThenFail": false,
      "continuePipeline": false,
      "failPipeline": true,
      "name": "Multi Region Bake",
      "pipeline": "db2b042e-0c9c-432b-b0d5-e620e787e0a8",
      "refId": "12",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Run Jenkins Job and Verify",
      "pipeline": "c93d5629-22f3-447d-9c2a-2e2f100cba9b",
      "refId": "13",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "comments": "This test SHOULD actually be valid, but there's a bug that's causing it to fail. See the pipeline for notes.\n\nQuick link to the pipeline:\nhttps://preprod.spinnaker.armory.io/#/applications/armoryhellodeploy/executions/configure/9a6a4362-2974-4389-850e-2af68ac08901",
      "completeOtherBranchesThenFail": false,
      "continuePipeline": false,
      "failPipeline": true,
      "name": "Kub Deploy, Resize and Shrink Test",
      "pipeline": "9a6a4362-2974-4389-850e-2af68ac08901",
      "refId": "14",
      "requisiteStageRefIds": [],
      "stageEnabled": {
        "expression": "false",
        "type": "expression"
      },
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Clone Server Group",
      "pipeline": "02bc08dd-76b9-4f0b-818c-bc5d9a13e353",
      "refId": "15",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "comments": "This test SHOULD actually be valid, but there's a bug that's causing it to fail. See the pipeline for notes.\n\nQuick link to the pipeline:\nhttps://preprod.spinnaker.armory.io/#/applications/armoryhellodeploy/executions/configure/9a6a4362-2974-4389-850e-2af68ac08901",
      "failPipeline": true,
      "name": "AWS Shrink Cluster Test",
      "pipeline": "f699ee1e-09b8-454a-a556-4d90b3a0d434",
      "refId": "16",
      "requisiteStageRefIds": [],
      "stageEnabled": {
        "expression": "false",
        "type": "expression"
      },
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Kube Rollback",
      "pipeline": "e5dc7587-2aa7-43e2-b57d-67cbb6a5a0a9",
      "refId": "17",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Platform - Test Running",
      "pipeline": "df67c25e-c587-440d-a6b8-ae22f2b137be",
      "refId": "18",
      "requisiteStageRefIds": [],
      "stageEnabled": {
        "expression": "false",
        "type": "expression"
      },
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Pipeline",
      "pipeline": "7bda7e87-c300-4475-bca8-90c566417efe",
      "refId": "19",
      "requisiteStageRefIds": [],
      "stageEnabled": {
        "expression": "false",
        "type": "expression"
      },
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "K8s v2 Artifact Deploy Delete Test",
      "pipeline": "654cbdb1-a8cc-4e86-9a5a-15d7010f6702",
      "refId": "20",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "K8s v2 Find Test",
      "pipeline": "e484e6b6-c53f-49a8-be0a-bdfd15fce2cf",
      "refId": "21",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "K8s v2 Scale Test",
      "pipeline": "210cce53-70bb-4b28-85df-04750a3327e9",
      "refId": "22",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "K8s v2 Patch Test",
      "pipeline": "721a66fc-aad1-4c09-8455-d4d64284c59b",
      "refId": "23",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "comments": "This only works on the edge build. Once it works in stable, make it fail the build if the stage fails. - August 13, 2018 13:19 PDT",
      "completeOtherBranchesThenFail": false,
      "continuePipeline": true,
      "failPipeline": false,
      "name": "K8s v2 Undo Rollout Test",
      "pipeline": "93593858-4bdf-48fd-b0b5-9921f21a141e",
      "refId": "24",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "K8s v2 Bake Manifest Test",
      "pipeline": "d624ed10-580b-4af1-8fa4-fe9a9430f0d0",
      "refId": "25",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    },
    {
      "application": "armoryhellodeploy",
      "failPipeline": true,
      "name": "Deploy Encrypted Image",
      "pipeline": "7bda7e87-c300-4475-bca8-90c566417efe",
      "refId": "26",
      "requisiteStageRefIds": [],
      "type": "pipeline",
      "waitForCompletion": true
    }
  ],
  "triggers": [],
  "updateTs": "1543368551000"
}
```
</details>




# Release Armory Spinnaker
At Armory, we use this pipeline to release a release candidate (RC) as a public version. This pipeline will run a series of Jenkins Jobs that will email our different types of customers.
![](https://cl.ly/dd54a34a22cf/Screen%20Shot%202019-02-05%20at%2020.46.02.png)


<details>
<summary><b>Expand this to see the pipeline JSON</b></summary>

```json
{
  "appConfig": {},
  "description": "Automates our public releases. \nMake sure to get a build that has successfully completed in `publish global amis`.\n\nNote: do not enable the jenkins trigger.",
  "disabled": false,
  "keepWaitingPipelines": false,
  "lastModifiedBy": "kevinawoo",
  "limitConcurrent": true,
  "parameterConfig": [
    {
      "default": "",
      "description": "Did you need to update <a href=\"https://github.com/armory-io/armoryspinnaker/edit/master/version\">Armory Spinnaker's major.minor</a> versions?",
      "hasOptions": true,
      "label": "Are you sure Armory Spinnaker major.minor version is ok?",
      "name": "UPDATED_MAJOR_MINOR_VERSION",
      "options": [
        {
          "value": "Yep! I updated to a new major.minor version."
        },
        {
          "value": "Yep! I don't need to update it. I'm releasing a new patch version with the same major.minor version."
        }
      ],
      "required": true
    },
    {
      "default": "Yes",
      "description": "Make sure to get a build that has successfully completed in `publish global amis`.",
      "label": "Did you pick a published AMI?",
      "name": "PICKED_A_PUSHED_AMI",
      "required": true
    },
    {
      "description": "Please include the OSS version this version is based on (e.g. \"OSS Release 1.8.6\")",
      "hasOptions": false,
      "label": "Description of the Release",
      "name": "RELEASE_DESCRIPTION",
      "options": [
        {
          "value": ""
        }
      ],
      "required": true
    }
  ],
  "stages": [
    {
      "comments": "Found version v${ trigger['properties']['ARMORYSPINNAKER_VERSION'] }",
      "continuePipeline": false,
      "failPipeline": true,
      "job": "armoryspinnaker-prep-release",
      "master": "Armory Jenkins",
      "name": "Prep Release",
      "parameters": {
        "release_build_number": "${ trigger.buildNumber }"
      },
      "refId": "1",
      "requisiteStageRefIds": [],
      "type": "jenkins"
    },
    {
      "failPipeline": true,
      "instructions": "Curate the <a href=\"http://jenkins.armory.io/job/armoryspinnaker-prep-release/${#stage('Prep Release')['outputs']['buildInfo']['number']}/artifact/draft-release-notes.txt/*view*/\">release notes</a> and add them to our public documentation",
      "judgmentInputs": [],
      "name": "Curate Release Notes",
      "notifications": [],
      "refId": "2",
      "requisiteStageRefIds": [
        "1"
      ],
      "type": "manualJudgment"
    },
    {
      "continuePipeline": false,
      "failPipeline": true,
      "job": "armoryspinnaker-automated-release-email",
      "master": "Armory Jenkins",
      "name": "Send email to #engineering",
      "parameters": {
        "mailing_list": "#engineering",
        "release_version": "v${ trigger['properties']['ARMORYSPINNAKER_VERSION'] }"
      },
      "refId": "3",
      "requisiteStageRefIds": [
        "4"
      ],
      "sendNotifications": true,
      "type": "jenkins"
    },
    {
      "comments": "Cutting version: v${ trigger['properties']['ARMORYSPINNAKER_VERSION'] }",
      "continuePipeline": false,
      "failPipeline": true,
      "job": "armoryspinnaker-cut-release",
      "master": "Armory Jenkins",
      "name": "Cut Release",
      "parameters": {
        "release_build_number": "${ trigger.buildNumber }",
        "release_description": "${parameters[\"RELEASE_DESCRIPTION\"]}"
      },
      "refId": "4",
      "requisiteStageRefIds": [
        "2",
        "6"
      ],
      "type": "jenkins"
    },
    {
      "completeOtherBranchesThenFail": false,
      "continuePipeline": true,
      "customHeaders": {
        "Authorization": "Basic ${ #toBase64('spinnaker-bot@armory.io:mySuperSecretPassword') }"
      },
      "failPipeline": false,
      "method": "GET",
      "name": "Find open issues for next release",
      "refId": "5",
      "requisiteStageRefIds": [],
      "statusJsonPath": "",
      "statusUrlResolution": "getMethod",
      "successStatuses": "",
      "type": "webhook",
      "url": "https://armory.atlassian.net/rest/api/2/search?jql=filter=10807",
      "waitForCompletion": false
    },
    {
      "failPipeline": true,
      "instructions": "There's open issues with tag <a href=\"https://armory.atlassian.net/issues/?filter=10807\">next-armory-spinnaker-release-todo</a>. Please address the issues by:\n- updating release notes to indicate any breaking changes\n- indicating on the ticket on how to move forward (prob the release notes too)\n- comment on the issue saying doesn't apply to this version, but it affects the next one\n- close the issues and remove the `next-armory-spinnaker-release-todo` tag\n\nBy clicking continue, I, ${execution['authentication']['user']} have done all steps above.",
      "judgmentInputs": [],
      "name": "Handle open issues",
      "notifications": [],
      "propagateAuthenticationContext": true,
      "refId": "6",
      "requisiteStageRefIds": [
        "5"
      ],
      "stageEnabled": {
        "expression": "#stage('Find open issues for next release')['context']['webhook']['body']['total'] != 0",
        "type": "expression"
      },
      "type": "manualJudgment"
    },
    {
      "failPipeline": true,
      "instructions": "Tech partners mailing list has emails in all customer email list.",
      "judgmentInputs": [
        {
          "value": "Yes"
        },
        {
          "value": "No"
        }
      ],
      "name": "Should we email tech-partners?",
      "notifications": [],
      "refId": "7",
      "requisiteStageRefIds": [
        "4"
      ],
      "type": "manualJudgment"
    },
    {
      "failPipeline": true,
      "judgmentInputs": [
        {
          "value": "Yes"
        },
        {
          "value": "No"
        }
      ],
      "name": "Should we email all customers?",
      "notifications": [
        {
          "address": "#engineering",
          "level": "stage",
          "type": "slack",
          "when": [
            "manualJudgment"
          ]
        }
      ],
      "propagateAuthenticationContext": false,
      "refId": "8",
      "requisiteStageRefIds": [
        "12"
      ],
      "sendNotifications": true,
      "type": "manualJudgment"
    },
    {
      "continuePipeline": false,
      "failPipeline": true,
      "job": "armoryspinnaker-automated-release-email",
      "master": "Armory Jenkins",
      "name": "Send E-mail to tech-partners",
      "parameters": {
        "mailing_list": "tech-partners",
        "release_version": "v${ trigger['properties']['ARMORYSPINNAKER_VERSION'] }"
      },
      "refId": "9",
      "requisiteStageRefIds": [
        "7"
      ],
      "stageEnabled": {
        "expression": "${#judgement('Should we email tech-partners?') ==  \"Yes\"}",
        "type": "expression"
      },
      "type": "jenkins"
    },
    {
      "continuePipeline": false,
      "failPipeline": true,
      "job": "armoryspinnaker-automated-release-email",
      "master": "Armory Jenkins",
      "name": "Send E-mail to all customers",
      "parameters": {
        "mailing_list": "all customers",
        "release_version": "v${ trigger['properties']['ARMORYSPINNAKER_VERSION'] }"
      },
      "refId": "11",
      "requisiteStageRefIds": [
        "8"
      ],
      "stageEnabled": {
        "expression": "${ #judgement('Should we email all customers?') ==  \"Yes\" && #stage('Should we email all customers?')[\"judgmentInput\"] }",
        "type": "expression"
      },
      "type": "jenkins"
    },
    {
      "comments": "empty stage to make pipeline look purrdy",
      "name": "🚀🌖",
      "preconditions": [],
      "refId": "10",
      "requisiteStageRefIds": [
        "8"
      ],
      "type": "checkPreconditions"
    },
    {
      "comments": "Wait at least a day.",
      "name": "Wait until tech-partners have tested it",
      "refId": "12",
      "requisiteStageRefIds": [
        "7"
      ],
      "type": "wait",
      "waitTime": "${ 5 * (24 * 60 * 60) }"
    }
  ],
  "triggers": [
    {
      "enabled": false,
      "job": "armory/job/armoryspinnaker/job/master",
      "master": "Armory Jenkins",
      "propertyFile": "build.properties",
      "type": "jenkins"
    }
  ],
  "updateTs": "1547864792000"
}
```
</details>
