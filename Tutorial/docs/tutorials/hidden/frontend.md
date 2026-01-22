# Frontend

Learn about the CosmoTech web application interface and how to create datasets and scenarios to run simulations.

## Overview

The CosmoTech web application provides an intuitive interface for:
- Creating and managing datasets
- Configuring and running scenarios
- Monitoring simulation execution
- Visualizing results through Power BI dashboards

This guide walks you through the essential steps to get started with your first simulation.

## Creating a Dataset

Datasets contain the input data that your scenarios will use for simulations. Before running any scenario, you need to create at least one dataset.

### Step 1: Navigate to the Dataset View

When you first access a workspace with no datasets, you'll see an empty dataset view.

![No Dataset](../../assets/webapp/empty_dataset.png)

### Step 2: Enter Dataset Information

Click the **Create Dataset** button and provide the basic information:

- **Name** - A descriptive name for your dataset
- **Description** - Optional details about the dataset contents
- **Tags** - Optional labels for organization

![Creating Dataset - Basic Information](../../assets/webapp/create_dataset.png)

### Step 3: Configure Data Source

Select your data source type and configure the connection parameters:

- **Source Type** - Choose from available connectors (ADT, Azure Storage, Local File, etc.)
- **Connection Parameters** - Provide authentication and location details
- **Data Mapping** - Configure how data should be imported

![Creating Dataset - Source Configuration](../../assets/webapp/create_dataset_2.png)

### Step 4: Dataset Import

The web application will begin importing your data. Depending on the data size, this may take a few moments.

![Dataset Created Successfully](../../assets/webapp/created_dataset.png)

Once the import is complete, your dataset will be ready to use in scenarios.

## Creating a Scenario

Scenarios define the simulation parameters and execution settings. Each scenario is linked to a dataset and a run template.

### Step 1: Navigate to the Scenario View

When you first access a workspace with no scenarios, you'll see an empty scenario view.

![No Scenario](../../assets/webapp/empty_scenario.png)

### Step 2: Configure Scenario Details

Click the **Create Scenario** button and provide the configuration:

- **Name** - A descriptive name for your scenario
- **Description** - Optional details about what this scenario simulates
- **Run Template** - Select the type of simulation to execute
- **Dataset** - Choose the dataset created in the previous steps
- **Parameters** - Configure scenario-specific parameters based on the selected run template

![Create Scenario](../../assets/webapp/create_scenario.png)

### Step 3: Scenario Created

Once created, your scenario is ready to run. You can modify parameters or execute the simulation.

![Created Scenario](../../assets/webapp/created_scenario.png)

## Running a Scenario

### Execute the Simulation

Click the **Run** button to start the simulation. The scenario will be submitted to the execution queue.

### Monitor Progress

The web application displays the execution status in real-time:
- **Pending** - Waiting for resources
- **Running** - Simulation in progress
- **Successful** - Completed successfully
- **Failed** - Execution encountered an error

![Running Scenario](../../assets/webapp/running_scenario.png)

### View Results

Once the scenario has finished running successfully, you can:
- View results in the **Dashboards** tab (Power BI reports)
- Analyze data directly in **Azure Data Explorer**
- Compare results with other scenarios
- Export data for further analysis

The simulation output data is automatically sent to Event Hub, ingested into ADX, and made available for visualization in Power BI dashboards.

## Next Steps

After running your first scenario:

1. **Explore Results** - Navigate to the Dashboards tab to view Power BI reports
2. **Create Variations** - Clone your scenario and adjust parameters to explore different outcomes
3. **Compare Scenarios** - Use comparison features to analyze differences
4. **Query Raw Data** - Access ADX directly for custom data analysis
5. **Share Insights** - Export reports and share findings with your team

## Tips and Best Practices

- **Naming Conventions** - Use clear, descriptive names for datasets and scenarios
- **Dataset Reuse** - Multiple scenarios can share the same dataset
- **Parameter Documentation** - Add descriptions to help others understand your scenario configuration
- **Version Control** - Create scenario clones before making significant changes
- **Monitor Resources** - Check execution status and logs if a scenario fails
- **Validate Data** - Ensure datasets are correctly imported before running scenarios

## Troubleshooting

### Dataset Import Issues
- Verify connection parameters and authentication
- Check data source accessibility
- Review error messages in the dataset details

### Scenario Execution Failures
- Confirm the dataset is valid and complete
- Check parameter values are within acceptable ranges
- Review execution logs for error details
- Verify the Docker image is available in the registry

### Dashboard Not Loading
- Ensure the scenario has completed successfully
- Verify Power BI workspace permissions
- Check that ADX contains the expected data
- Refresh the dashboard view