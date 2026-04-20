# Deploy a Solution

During this Hands-On training, you will start from a minimal simulator and progressively transform it into a fully deployed Solution.  
You will learn to configure environments, work with CosmoTech run templates, build and publish Docker images, and connect everything to ADX and Power BI for visualization.

In this discovery part, we will go through the steps to deploy a pre-packaged Solution, using templated content and babylon.

---

## 1: Connect to the cluster

For this, we will need to access the kubernetes service stored on Azure.
Today we are going to use the following cluster: `aks-dev-pmu.azure`

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

## 5: Setting up the Solution - First Part

Setting up the Solution will require a bit more work now.

Just as for the Workspace, we can customize the name, key and description.

Update the following values in `variables.yaml`:

- `solution_name`: The name of your Solution
- `solution_description`: A description for your Solution
- `workspace_name`: The name of your workspace
- `workspace_description`: A description of your workspace

Example:
```yaml
solution_name: Babylon v5 Solution
solution_description: Brewery Testing babylon v5 Solution PLT
workspace_name: Babylon v5 workspace
workspace_description: Testing workspace for the brewery web application
```

<!-- But, we are also going to use a simulator image, this will take a few steps. -->

---

## 6: Obtaining a simulator

At this point, there are two possibilities:

<h4>a. Using a pre-packaged simulator</h4>

&emsp;Following the instructions of the person in charge of the Hands-On training, go directly to:

&emsp;[7: Setting up the Solution - Second Part](./02_Discovery.md/#7-setting-up-the-solution-second-part)

<h4>b. Building a simulator of your own</h4>

&emsp;Customize an existing Simulator based on the brewery sample Solution:
[<h4>&emsp;:octicons-arrow-right-24: Customize an existing Simulator</h4>](./Discovery/02_07.b_Simulator.md)

---

## 7: Setting up the Solution - Second Part

Now that we have a usable simulator image, we are able to finish preparing our Solution.

Update the following values in `variables.yaml`:

- `simulator_repository`: The path to the simulator image repository
- `simulator_version`: The tag of the simulator image

Example:
```yaml
simulator_repository: tenant-sphinx/brewerysamplesolution_simulator
simulator_version: 3.0.0
```

---

## 8: Setting up the WebApp

Setting up the WebApp will be easier than setting up the Solution

Update the following values in `variables.yaml`:

- `cluster_name`: The name of the Kubernetes cloud cluster
- `domain_zone`: The domain of the Kubernetes cloud cluster
- `tenant`: The id of the tenant used to host the platform (without the `tenant-` prefix)
- `webapp_name`: A unique name of your choosing for your own Web App

Example:
```yaml
cluster_name: aks-dev-luxor
domain_zone: azure.platform.cosmotech.com
tenant: sphinx
webapp_name: business
```

---

## 9: Setting up the security

For this we'll just need to input our email adress as Id and give ourself the admin role:

- `id`: The mail address of the user
- `role`: A role among (admin, editor, viewer)

Example:
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

For this, run the command:
```bash
babylon namespace use -c <CONTEXT_NAME> -t <TENANT_NAME> -s <STATE_NAME>
```
where you can chose freely the name you want to give to your local context and state.

Example:
```bash
babylon namespace use -c bmactx -t tenant-sphinx -s bmast
```

And now that Babylon has everything it needs to now, let's run it:
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
https://aks-dev-pmu.azure.azure.platform.cosmotech.com/tenant-e2e/webapp-<WEBAPP_NAME>
```

---

## 13: Creating a dataset

&emsp;Create a dataset on the Web App:
[<h4>&emsp;:octicons-arrow-right-24: Creating a dataset</h4>](./Discovery/02_13_FE_Dataset.md)

---

## 14: Creating a Scenario and running it

&emsp;Create a scenario and run it on the Web App:
[<h4>&emsp;:octicons-arrow-right-24: Creating and running a scenario</h4>](./Discovery/02_14_FE_Scenario.md)

---

## 15: Visualizing results

&emsp;Visualize results on the Web App:
[<h4>&emsp;:octicons-arrow-right-24: A brief look at Superset</h4>](./Discovery/02_15_FE_Superset.md)

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
