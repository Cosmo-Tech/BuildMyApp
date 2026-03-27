# Deploy a Solution

During this Hands-On training, you will start from a minimal simulator and progressively transform it into a fully deployed Solution.  
You will learn to configure environments, work with CosmoTech run templates, build and publish Docker images, and connect everything to ADX and Power BI for visualization.

In this discovery part, we will go through the steps to deploy a pre-packaged Solution, using templated content and babylon.

---

## 1: Connect to the cluster

For this, we will need to access the kubernetes service stored on Azure.
Today we are going to use the following cluster: `aks-qa-campaign`

Once you're logged into the azure portal, go to kubernetes service. In here, select the cluster specified above.
On the new page, you will find a connect button at the top. Upon clicking it, a pop up will appear on the right side of the window. Select the "Azure-cli" tab and run the first three commands.

Congrats, you now have a connection to the kubernetes cluster.

---

## 2: Choosing a cluster connection

You can see all the connections on your computer by typing

```bash
kubectl config get-contexts
```

And you can select the one you want to use with

```bash
kubectl config use-context <CONTEXT-NAME>
```

---

## 3: Setting up the workfolder

Create a new folder where we'll setup our Babylon

```bash
mkdir bma_folder
cd bma_folder
```

In here we're going to initialize our babylon

```bash
babylon init
```

Here, you will find a file named variables.yaml that contains most of the elements you will update during the Hands-On training.

---

## 4: Setting up the organization and workspace

Open the files created by a babylon in the IDE of your choice and head to `variables.yaml`

Our first step is going to be setting up our organization, the only usable variable in here is the name.

Then we can customize our workspace name, key and description.

---

## 5: Setting up the Solution - Part 1

Setting up the Solution will require a bit more work now.

Just as for the Workspace, we can customize the name, key and description.

You will need to update the following values in `variables.yaml`:

- `solution_key`: A unique key for your Solution
- `solution_name`: The name of your Solution
- `solution_description`: A description for your Solution
- `workspace_key`: A unique key for your workspace, it will be required for some resource creation
- `workspace_name`: The name of your workspace
- `workspace_description`: A description of your workspace
- `security.accessControlList`: Add your mail addresses as `Admin` to ensure that you will have access to your Solution
- `cluster_name`: The name of the Kubernetes cloud cluster
- `cluster_domain`: The full domain name of the Kubernetes cloud cluster
- `tenant`: The id of the tenant used to host the platform
- `webapp_name`: A unique name for your Web App

<!-- But, we are also going to use a simulator image, this will take a few steps. -->

---

## 6: Obtaining a simulator

At this point, there are two possibilities:

<h4>1. Using a pre-packaged simulator</h4>

&emsp;Following the instructions of the person in charge of the Hands-On training, go directly to:

&emsp;[7: Setting up the Solution - Part 2](./02_Discovery.md/#7-setting-up-the-solution-part-2)

<h4>2. Building a simulator of your own</h4>

&emsp;Customize an existing Simulator based on the brewery sample Solution:
[<h4>&emsp;:octicons-arrow-right-24: Customize an existing Simulator</h4>](./Discovery/02_07.2_Simulator.md)

---

## 7: Setting up the Solution - Part 2

Now that we have a usable simulator image, we are able to finish preparing our Solution.

The simulator repository will be our image name and our simulator version will be the simulator version.

---

## 8: Setting up the WebApp

Setting up the WebApp will be easier than setting up the Solution

The variables we'll be touching are:
- The cluster name
- The cluster domain
- The tenant (with the `tenant-` truncated)
- The webapp name

We'll be using those variables:

```yaml
cluster_name: aks-qa-campaign
cluster_domain: aks-qa-campaign.azure.platform.cosmotech.com
tenant: e2e
```

You can use your favorite words for the webapp name

---

## 9: Setting up the security

For this we'll just need to input our email adress as Id and give ourself the admin role:

```yaml
security:
  default: none
  accessControlList:
    - id: <YOUR_MAIL>
      role: admin
```

At the time of writing this guide, the keycloak group system has been implemented in the Cosmotech API but is still undergoing compatibility work with the WebApp.

---

## 10: Running Babylon

Before giving Babylon all the informations we entered and putting it to work. We're going to need to tell it where it should work.

For this, run this command:

```bash
babylon namespace use -c <CONTEXT_NAME> -t <TENANT_NAME> -s <STATE_NAME>
```

And now that Babylon has everything it needs to now, let's run it.

```bash
babylon apply project/
```


---

## 11: Update info

Now that we have created our desired Cosmotech objects and deployed our personnal webapp, before looking at the detail, we can see how to easily modify the configuration of our objects.

If we modify any informations in our organization, workspace or Solution, and run again the same command:

```bash
babylon apply project/
```

Babylon will automatically compare our local configuration to the existing objects and make the necessary changes by itself.

---

## 12: Accessing the webapp

In the logs of our previous command, babylon provides us with the link to our deployed webapp.
It should look something like this:

```
https://aks-qa-campaign.azure.platform.cosmotech.com/tenant-e2e/webapp-<WEBAPP_NAME>
```

---

---

> **< < < < < < < < < < < < < < < < < < < < Work in Progress > > > > > > > > > > > > > > > > > > > >**
> 
> ## 13: Creating a dataset
> 
> ## 14: Creating a Scenario and running it
> 
> ## 15: A brief look at Superset
> <br />
> **< < < < < < < < < < < < < < < < < < < < Work in Progress > > > > > > > > > > > > > > > > > > > >**

---

---

## 16: Cleaning up after yourself

Once we are done with working on this workshop, we can clean up for the next person and those working on this tenant by running a simple command.

```bash
babylon destroy
```

This simple command will delete everything created by Babylon in our context.
And with that this session is wrapped up.

This concludes the first section on deploying a pre-packaged Solution. You can explore and experiment with it if you wish.

In the next part, we will go inside of the Solution and start customizing it.
