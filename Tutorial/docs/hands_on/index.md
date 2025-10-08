# Hands On Build My App - Preparation session

During this Hands On session you will be working with a minimal Simulator Cosmo Tech Project and will get it to be a fully deployed Solution.

## Resources

You will be given the following elements by the person in charge of the session :

- A Simulator repository hosted on [GitHub](https://github.com/Cosmo-Tech/onboarding-brewery-solution) (on the branch `BuildMyApp/Simulator`)
    - This repository contains a minimal project that is "Deployment Ready" but has no real configuration
    - You have the full Conceptual Model of the "Brewery" which is the example project used for Modeling tutorials
    - It contains a working Simulator that will simulate the Conceptual Model
    - There is 2 minimal run templates :
        - Example : a simple run that will copy a fixed dataset and simulate it
        - ETL : an empty run template that just shows logs some environment variables
    - You have a single Simulation set-up : `BusinessApp_Simulation`
        - It takes a list of CSVs as inputs as found in `Simulation/Resource/default_dataset` and simulate it
        - With no specific configuration of the simulation it will output results in `Simulation/Output`
- A project deployment repository hosted on [Github](https://github.com/Cosmo-Tech/build-my-app-training-workspaces)
    - This repository contains an example deployable project compatible with the example Simulator you were provided
    - It contains the following elements :
        - a `variables.yaml` file that will contain most of the data you will need to update before doing your deployments
        - a `project/` folder containing the configuration for your project deployment
            - `solution.yaml` a consolidated solution file compatible with the `babylon` cli to deploy resources
            - `workspace.yaml` a consolidated workspace file also compatible with `babylon`
            - `adx/scripts/00-Initialization.kql` a kusto script that will initialize the ADX database tied to the project
            - `powerbi/Example.pbix` a simple PowerBi dashboard used for example data display configurated for using connection parameters
- A set of environment variables allowing you to connect to the resources :
    - `BABYLON_ORG_NAME` / `BABYLON_TOKEN` / `BABYLON_SERVICE` the variables used to connect to the Babylon source of truth
    - `AZURE_TENANT_ID` / `AZURE_CLIENT_ID` / `AZURE_CLIENT_SECRET` a triplet of Azure environment variables used to ensure connection to the Cosmo Tech API
    - `CSM_API_URL` / `CSM_API_SCOPE` a set of environment variables allowing for connection to the Cosmo Tech API
- A link to a pre-configurated web-app where your resources will show up once deployed
- A link to an `ADX` cluster interactive database query service where you will be able to look at your data
- A client ID for `Swagger` allowing you to use the Swagger app for the API to try your hand at the API calls without `babylon`

## Permissions

The person in charge of your session should make sure you have the following accesses ready :

- A Power BI App Licence
- A User right to the API you will be using during the session
- An azure entra identity on the tenant used for the training

## Software requirements

You will need most of the requirements listed on [this address](https://cosmo-tech.github.io/BuildMyApp/0.0.1/tutorials/pre-requisites/)  
Make sure all are ready to use before the start of the work session

Once all those are ready you will be able to move on to the full Build My App Hands On