# Customize an existing Solution

Now that we saw how to deploy a Solution it is time to make our own version.

We will now move from the `build-my-app-training-workspace` repository to the `onboarding-brewery-solution` repository.

## First look at the Simulator

Let's begin our look at the simulator by going through the main files that are used to define a Simulation.

In the project 4 files are of interest :

- `ConceptualModel/MyBrewery.csm.xml`: This contains the actual model of the simulation, with all the model and logic of the Simulation
- `Simulator/Simulator.sor.xml`: This defines the Simulator itself which is a combination of a Conceptual Model and probes that will be able to export data during a simulation
- `Simulation/BusinessApp_Simulation.sml.xml`: This is the description of a simulation, that will instantiate entities and then define how the simulation runs, accompanied by the definition of Consumers that will consume the data from the probes and export them in different formats
- `Simulation/Resource/CSV_Brewery.ist.xml`: This is an instance of a simulation that will make use of CSV files to generate entities and add them to the simulation

