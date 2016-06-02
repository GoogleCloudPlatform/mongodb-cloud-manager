# MongoDB via Cloud Manager

Bootstrap multiple Google Compute Engine instances with the MongoDB Cloud Manager agent.

## Prerequisites

### Google Cloud Platform
- Create a [Google Cloud Platform](https://cloud.google.com) account (and enable billing)
- Install and setup the [Google Cloud SDK](https://cloud.google.com/sdk/)
- [Enable](https://console.cloud.google.com/flows/enableapi?apiid=deploymentmanager,compute_component) the Deployment Manager and Compute Engine APIs
- Set your project: `gcloud config set project PROJECT-ID`
- Set the preferred zone: `gcloud config set compute/zone ZONE`

### MongoDB Cloud Manager
- Create a [MongoDB Cloud Manager](https://www.mongodb.com/cloud) account
- Navigate to `Cloud Manager > Settings > Group Settings` and copy the `Group ID` and `Agent API Key` at the top of the page, you will need these values below.

## Deploying Configuration

### Properties

The Deployment Manager template uses a [schema](https://cloud.google.com/deployment-manager/configuration/using-schemas) that defines and describes the properties required for the deployment:

`machineType`

- Default value is `n1-highmem-2`
- Can be any of the available [predefined machine types](https://cloud.google.com/compute/docs/machine-types#predefined_machine_types)
- For [custom machine types](https://cloud.google.com/compute/docs/machine-types#custom_machine_types) refer to the [machineType URL spec](https://cloud.google.com/compute/docs/reference/latest/instances#resource-representations)

`zone`

- Default value is `us-central1-f`
- Can be any of the [available regions & zones](https://cloud.google.com/compute/docs/regions-zones/regions-zones#available)

`mmsGroupId`

- No default value set
- Use the value from `Group Id` above when deploying (see example below)

`mmsApiKey`

- No default value set
- Use the value from `Agent API Key` above when deploying (see example below)

### Creating Deployment

Create the deployment using the Deployment Manager configuration template:

    $ gcloud deployment-manager deployments create mongodb-cloud-manager \
      --config mongodb-cloud-manager.jinja \
      --properties mmsGroupId=MMSGROUPID,mmsApiKey=MMSAPIKEY

Optionally, to override the default values for `machineType` or `zone` use the following:

    $ gcloud deployment-manager deployments create mongodb-cloud-manager \
      --config mongodb-cloud-manager.jinja \
      --properties machineType=n1-highmem-8,zone=us-central1-d,mmsGroupId=MMSGROUPID,mmsApiKey=MMSAPIKEY

The Deployment Manager configuration creates three 500GB Persistent SSDs and attaches them to three Compute Engine instances. Each instance then runs a startup script that configures and attaches storage, and installs the MongoDB Cloud Manager automation agent.

## Deploy MongoDB using Cloud Manager

Once the instances have completed the setup process, they should be visible via `Cloud Manager > Deployment > Servers` in a few minutes.

## Clean Up

First, unmanage the deployment via Cloud Manager. Refer to the documentation on [Removing a Process from Management](https://docs.cloud.mongodb.com/tutorial/unmanage-deployment/) for more information.

Next, delete the deployment from Google Cloud Platform. 

> **Note** that all configuration resources (instances, disks) will be deleted.
    
    $ gcloud deployment-manager deployments delete mongodb-cloud-manager

## Notes
- Instances are created within the `default` network however no MongoDB-specific firewall rules are added. To add rules that allow incoming traffic to your MongoDB deployment, refer to the [Firewalls](https://cloud.google.com/compute/docs/networking#firewalls) documentation.
- The [Google Cloud Platform Pricing Calculator](https://cloud.google.com/products/calculator) estimate of the monthly costs to run this deplyoment configuration can be found [here](https://cloud.google.com/products/calculator/#id=5561bb46-da61-4c64-aaa1-2f69d6d7b310)


