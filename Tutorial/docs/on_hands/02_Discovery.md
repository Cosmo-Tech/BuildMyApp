# Deploy a pre-packaged Solution

In this first part we will go through the steps to deploy a pre-packaged solution, using templated content and babylon.

## Set up the babylon configuration

Before being able to deploy our solution we will need to configure babylon by defining a state.

The babylon state will ensure that when we apply changes to our solution and workspace later we keep working on the same elements and do not duplicate everything.

So for now we will work on the `build-my-app-training-workspace` repository.

In that repository you have a file named `variables.yaml` it contains most of the element you will update during the hands on session.

You also have other files that we will come back to later.

Back to the `variables.yaml` file where you will need to apply your first modifications and change the values in the first two keys `state_id` and `context_id`

You can put any other name that you want on those, because the value in `remote` is set to `False` we won't be sending the data to be synchronized so the state will be local.

Once you have chosen values for those two you will be close to run your first babylon command.

!!! warning "Chekpoint"
    You will need to get the babylon environment variables to be able to run any command.

Now that you have your environment variables you can run the following commands

```bash title="Activate babylon namespace"
export BABYLON_ORG_NAME=<Replace this>
export BABYLON_TOKEN=<Replace this>
export BABYLON_SERVICE=<Replace this>

babylon namespace use -s <state_id> -c <context_id> -p <platform_id>
```

You will need to replace the values in the babylon command with the values that are set in your `variables.yaml` and the environment variable by those you will be given

And once that command runs without error your configuration for babylon will be done.

## Customize the Solution and Workspace template

With babylon configured we can now customize the template of the solution to get our own workspace and solution.

You will need to update the following values in `variables.yaml`:

- `solution_key`: A unique key for your solution
- `solution_name`: The name of your solution
- `solution_description`: A description for your solution
- `workspace_key`: A unique key for your workspace, it will be required for some resource creation
- `workspace_name`: The name of your workspace
- `workspace_description`: A decription of your workspace
- `powerbi_workspace_name`: A unique name for your powerbi workspace that will host your dashboards
- `security.accessControlList`: Add your mail addresses as `Admin` to ensure that you will have access to your solution
- `powerbi_permissions`: Add your mail addresses as `Admin` to ensure that you will be able to upload and configure your power bi dashboards
- `adx_permissions`: You will need to make use of the `gen_adx_permissions.sh` script to get your `principal_id` and then add yourself as `Admin` so that you can execute queries on ADX

Once all those changes are made you will be able to run your second babylon command

!!! warning "Checkpoint"
    Make sure you have your rights for Power BI ready with a Pro subscription and an Azure Entra identity so that you can get your `principal_id`

If all your permissions are ready you can run the following command

```bash title="My first babylon deployment"
babylon apply project --solution
babylon apply project --workspace
```

And once those commands are finished you can go to [the Build My App Web App](https://build-my-app.app.cosmotech.com) and see your workspace ready for you.

This now conclude our first subject of deploying a pre-package solution, you can play around with it if you want.

The next subject will be to go inside of the solution and start customizing it.