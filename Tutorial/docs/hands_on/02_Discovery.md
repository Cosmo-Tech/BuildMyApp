# Deploy a pre-packaged Solution

In this first part we will go through the steps to deploy a pre-packaged solution, using templated content and babylon.

## Set up the babylon configuration

Before being able to deploy our solution we will need to configure babylon by defining a state.

The babylon state ensures that when you apply changes to your solution and workspace later, you continue working on the same elements without creating duplicates.

So for now we will work on the `build-my-app-training-workspace` repository.

In that repository, you have a file named `variables.yaml` that contains most of the elements you will update during the hands-on session.

You also have other files that we will come back to later.

Return to the `variables.yaml` file where you need to make your first modifications by changing the values in the first two keys: `state_id` and `context_id`.

You can use any names you want for these values. Since the `remote` value is set to `False`, the data won't be synchronized remotely, keeping the state local.

Once you have chosen values for these two settings, you will be ready to run your first babylon command.

!!! warning "Checkpoint"
    You will need to get the babylon environment variables to be able to run any command.

Now that you have your environment variables you can run the following commands

```bash title="Activate babylon namespace"
export BABYLON_ORG_NAME=<Replace this>
export BABYLON_TOKEN=<Replace this>
export BABYLON_SERVICE=<Replace this>

babylon namespace use -s <state_id> -c <context_id> -p <platform_id>
```

You need to replace the values in the babylon command with the values from your `variables.yaml` file and set the environment variables with the values you will be provided.

Once this command runs successfully without errors, your babylon configuration will be complete.

## Customize the Solution and Workspace template

With babylon now configured, you can customize the solution template to create your own workspace and solution.

You will need to update the following values in `variables.yaml`:

- `solution_key`: A unique key for your solution
- `solution_name`: The name of your solution
- `solution_description`: A description for your solution
- `workspace_key`: A unique key for your workspace, it will be required for some resource creation
- `workspace_name`: The name of your workspace
- `workspace_description`: A description of your workspace
- `powerbi_workspace_name`: A unique name for your powerbi workspace that will host your dashboards
- `security.accessControlList`: Add your mail addresses as `Admin` to ensure that you will have access to your solution
- `powerbi_permissions`: Add your mail addresses as `Admin` to ensure that you will be able to upload and configure your power bi dashboards
- `adx_permissions`: You need to use the `gen_adx_permissions.sh` script to obtain your `principal_id`, then add yourself as an `Admin` to execute queries on ADX.

Once all those changes are made you will be able to run your second babylon command

!!! warning "Checkpoint"
    Make sure you have your rights for Power BI ready with a Pro subscription and an Azure Entra identity so that you can get your `principal_id`

If all your permissions are ready you can run the following command

```bash title="My first babylon deployment"
babylon apply project --solution
babylon apply project --workspace
```

And once those commands are finished you can go to [the Build My App Web App](https://build-my-app.app.cosmotech.com) and see your workspace ready for you.

This concludes the first section on deploying a pre-packaged solution. You can explore and experiment with it if you wish.

The next subject will be to go inside of the solution and start customizing it.
