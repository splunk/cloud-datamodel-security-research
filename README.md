Cloud Infrastructure Data Model
==========

A Splunk data model for cloud infrastructure data (AWS / GCP / Azure)

*Join the conversation at* [![Slack Status](https://img.shields.io/badge/slack-@splunk/security-yellow.svg?logo=slack)](https://splunk-usergroups.slack.com/signup)

- Original Author: Rico Valdez
- Current maintainers:
- Sourcetype: aws:cloudtrail, aws:so mucloudwatchlogs:vpcflow, google:gcp:pubsub:message, mscs:azure:audit
- Has index-time ops: false



# Features
* Provides a data model suitable for normalizing some of the data coming from AWS, GCP, and Azure. 
	* Blocks for compute (VM Instances), storage, and network traffic
* Includes additional eventtypes, field aliases, tags, and calculations to help populate the model.
* Provides data-model mapping for:
	* Basic VM activity (start, stop, create, terminate)
	* Basic bucket/object activity for storage use cases
	* Network traffic, as provided by vpcflow logs, and gec_instance events for GCP


# Description

A Splunk data model is a type of knowledge object that applies an information structure to raw data at search time—regardless of the data's origin or format—and encodes the domain knowledge necessary to build a variety of specialized searches. Data models enhance the efficiency of your searches. You can increase their velocity with data-model acceleration, which creates summaries for the fields you report on, accelerating the dataset represented by those fields. 

The Cloud Infrastructure Data Model normalizes and combines your machine data from AWS, Azure, and/or GCP, so you can write analytics that work across all three.


# Prerequisites

You should have the appropriate add-on(s) for your cloud provider(s) to bring data into Splunk and perform the basic field extractions. These are the following:

* AWS: Splunk Add-on for Amazon Web Services - https://splunkbase.splunk.com/app/1876/
* GCP: Splunk Add-on for Google Cloud Platform - https://splunkbase.splunk.com/app/3088/
* Azure: Splunk Add-on for Microsoft Cloud Services - https://splunkbase.splunk.com/app/3110/


# Installing the Data Model 

Method 1:

1. Download the .spl package and install as a normal app.
2. It may be necessary to move props.conf, tags.conf, and eventtypes.conf from the app default directory to the local directory. (If it doesn't already exist, create one at the same level as the default directory.)


Method 2:

1. Clone or download the repo. 
2. Find the cloud_infrastructure.json file located in default/data/models.
3. Navigate to Settings->Datamodels in your Splunk instance.
4. Click the "Upload" button in the top right of the page and select the cloud_infrastrucutre.json file. You can install in whatever app context you like. 
5. Go back one level to the list of data models and click "Edit," then select "Edit Permissions."
6. Select "All Apps" and assign permissions, so that everyone can read and admins can write. Click "Save."
7. [optional] Click "Edit," then choose "Edit Data Model Acceleration," if you wish to accelerate the model. 
8. Drop the included props.conf, tags.conf, and eventtypes.conf in the <code>local</code> directory of the app context in which you installed or create/update the associated files in the local directory for the appropriate app. For example, create or modify <code>local/props.conf</code> in the app directory for the Amazon add-on, to include the information under the AWS stanzas in the included props.conf file.


# Testing the Data Model

The following search will show you how the data model is being populated with compute data:

<code>| datamodel Cloud_Infrastructure Compute search | table Compute*</code>

You can re-run the search, replacing both instances of "Compute" with "Storage" or "Traffic." You can also look at a specific provider by inserting a search, such as:

<code>| datamodel Cloud_Infrastructure Compute search | search sourcetype=aws:cloudtrail | table Compute*</code>


# Troubleshooting
If the data is not showing up, confirm that the provided .conf files are properly deployed. They may need to be moved into an app <code>local</code> directory to take precedence over existing directives.

Make sure the indexes containing your cloud_infrastructure data are searchable by default, or add the indexes to the definition for the eventtypes, as necessary.

If data is not properly mapped, consider making edits to the lines provided in props.conf.

# To Dos
* Further testing/refinement of extractions and mappings


# Provided DM fields


|                                                                                                  |                                                                  | 
|--------------------------------------------------------------------------------------------------|------------------------------------------------------------------| 
|                                                                                       |  **Field Name**                                                                 | 
| **Compute**                                                                                          |                                                                  | 
|                                                                                                  | action                                                           | 
|                                                                                                  | http_user_agent                                                  | 
|                                                                                                  | region                                                           | 
|                                                                                                  | resource_id                                                      | 
|                                                                                                  | resource_name                                                    | 
|                                                                                                  | src                                                              | 
|                                                                                                  | src_network                                                      | 
|                                                                                                  | user_type                                                        | 
|                                                                                                  | src_user                                                         | 
|                                                                                                  | dest                                                             | 
|                                                                                                  | account                                                          | 
|                                                                                                  | image_id                                                         | 
|                                                                                                  | status                                                           | 
|                                                                                                  | event_name                                                       | 
|                                                                                                  | msg                                                              | 
|                                                                                                  |                                                                  | 
| **Storage**                                                                                          |                                                                  | 
|                                                                                                  | action                                                           | 
|                                                                                                  | account                                                          | 
|                                                                                                  | bucket_name                                                      | 
|                                                                                                  | http_user_agent                                                  | 
|                                                                                                  | object_path                                                      | 
|                                                                                                  | resource_name                                                    | 
|                                                                                                  | src                                                              | 
|                                                                                                  | src_network                                                      | 
|                                                                                                  | user_type                                                        | 
|                                                                                                  | src_user                                                         | 
|                                                                                                  | event_name                                                       | 
|                                                                                                  | msg                                                              | 
|                                                                                                  |                                                                  | 
| **Checks**                                                                                           | action                                                           | 
|                                                                                                  | status                                                           | 
|                                                                                                  | state?                                                           | 
| **Interesting fields**                                                                               |                                                                  | 
| GCP                                                                                              | data.jsonPayload.ident                                           | 
| GCP                                                                                              | data.protoPayload.authorizationInfo{}.resourceAttributes.service | 
| GCP                                                                                              | data.resource.type                                               | 
| GCP                                                                                              | data.severity                                                    | 
| GCP                                                                                              | data.protoPayload.response.status                                | 
| GCP                                                                                              | "data.protoPayload.authorizationInfo{}.resource"                 | 
| Azure                                                                                            |                                                                  | 
| *** Need to handle multi-value fields for instance_id and possibly others                        |                                                                  | 
|                                                                                                  |                                                                  | 
| *Network*                                                                                          | index="gcp_data" "data.resource.type"=gce_network                | 
|                                                                                                  | Field Name                                                       | 
|                                                                                                  |                                                                  | 
|                                                                                                  | host                                                             | 
|                                                                                                  | dest_zone                                                        | 
|                                                                                                  | src_port                                                         | 
|                                                                                                  | src_ip                                                           | 
|                                                                                                  | dest_ip                                                          | 
|                                                                                                  | bytes_out                                                        | 
|                                                                                                  | packets_out                                                      | 
|                                                                                                  | protocol                                                         | 
|                                                                                                  | dest_port                                                        | 
|                                                                                                  | dest_instance                                                    | 
|                                                                                                  | src_instance                                                     | 
|                                                                                                  | user                                                             | 
|                                                                                                  | resource_name                                                    | 
|                                                                                                  | subnetwork_name                                                  | 
|                                                                                                  | network_id                                                       | 
|                                                                                                  | src_asn                                                          | 
|                                                                                                  | dst_asn                                                          | 
|                                                                                                  | dvc_mac                                                          | 
|                                                                                                  | rule                                                             | 
| EC2 Events handled - Stop, Start, Run, Terminate - look at bringing in createInstance and others |                                                                  | 
