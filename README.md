# MongoDB via Cloud Manager

Bootstrap multiple Google Compute Engine instances with the MongoDB Cloud Manager agent.

## Prerequisites

### Google Cloud Platform
- [Google Cloud Platform](https://cloud.google.com) account (and enable billing)
- Install and setup the [Google Cloud SDK](https://cloud.google.com/sdk/)
- [Enable](https://console.cloud.google.com/flows/enableapi?apiid=deploymentmanager,compute_component) the Deployment Manager and Compute Engine APIs
- Set your project: `gcloud config set project PROJECT-ID`
- Set the preferred zone: `gcloud config set compute/zone ZONE`

### MongoDB Cloud Manager
- [MongoDB Cloud Manager](https://www.mongodb.com/cloud) account
- Navigate to `Cloud Manager > Deployment > Agents > Downloads and Settings`
- Click on one of the Automation Agent links and capture the [mmsGroupId](https://docs.cloud.mongodb.com/reference/automation-agent/#asetting.mmsGroupId) and [mmsApiKey](https://docs.cloud.mongodb.com/reference/automation-agent/#asetting.mmsApiKey)

## Deploy Instances

First, update the following default property values in the `mongodb-instance-template.jinja.schema` file:

```yaml
properties:
  machine-type:
    type: string
    default: n1-standard-4

  zone:
    type: string
    default: us-central1-f

  mmsGroupId:
    type: string
    default: MMSGROUPID

  mmsApiKey:
    type: string
    default: MMSAPIKEY
```

Next, create the deployment using the Deployment Manager configuration:

    $ gcloud deployment-manager deployments create mongodb-cloud-manager --config mongodb-cloud-manager.yaml

The Deployment Manager template will create 3 500GB Persistent SSDs and attach them to 3 Compute Engine instances. Each instance will run a startup script that configures storage and installs the MongoDB Cloud Manager automation agent.

## Deploy MongoDB using Cloud Manager

Once the instances have completed the setup process, they should be visible in Cloud Manager interface via `Deployment > Servers`.

## Clean Up

First, delete the deployment from Google Cloud Platform. Please note that all created resources (instances, disks) will be deleted.
    
    $ gcloud deployment-manager deployments delete mongodb-cloud-manager

Next, unmanage the deployment via Cloud Manager. Refer to the documentation on [Removing a Process from Management](https://docs.cloud.mongodb.com/tutorial/unmanage-deployment/) for more information.

## Notes
- Instances are created within the `default` network however no MongoDB-specific firewall rules are added. To add rules that allow incoming traffic to your MongoDB deployment, refer to the [Firewalls](https://cloud.google.com/compute/docs/networking#firewalls) documentation.


