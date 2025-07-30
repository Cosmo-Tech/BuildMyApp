# Input Data and Model Instantiation

!!! info "Tutorial Duration: 15-20 minutes"

This tutorial covers how to create instances of your conceptual model and configure them for simulation. You'll learn to set up entity instances, environments, schedulers, and initial conditions using the Brewery example.

## Learning Objectives

By the end of this tutorial, you will understand:

- The difference between conceptual models and model instances
- How to create entity instances and set their initial states
- Environment instantiation and relationship setup
- Scheduler configuration for controlling simulation flow
- Initialization and modification files for parameter setting

## Conceptual Model vs Model Instance

| Aspect | Conceptual Model | Model Instance |
|--------|------------------|----------------|
| **Purpose** | Defines entity *types* and their behaviors | Creates actual *instances* of entities |
| **Level** | Abstract/Template | Concrete/Specific |
| **Example** | "Customer entity with satisfaction attribute" | "Customer1 with satisfaction=5, Customer2 with satisfaction=3" |
| **File** | `.csm.xml` | `.ist.xml` |

Think of it like classes vs objects in programming - the conceptual model is the class definition, the instance is the actual object.

## Basic Entity Instantiation

### Creating the Bar Instance

Our brewery simulation starts with a Bar compound entity containing customers:

```xml
<csm:Bar id="1" name="MyBar">
  <csm:Compound>
    <csm:SubEntities>
      <csm:Customer id="2" name="Customer1"/>
      <csm:Customer id="3" name="Customer2"/>
      <csm:Customer id="4" name="Customer3"/>
      <csm:Customer id="5" name="Customer4"/>
    </csm:SubEntities>
    <!-- Environment and scheduler definitions follow -->
  </csm:Compound>
</csm:Bar>
```

### Entity Instance Components

- **id**: Unique numeric identifier for referencing the instance
- **name**: Human-readable name for debugging and display
- **Type**: Defined by the XML element name (`csm:Customer`, `csm:Bar`)

!!! tip "ID Management"
    Use systematic ID numbering:
    - Main entities: 1, 10, 100 (easy to remember)
    - Sub-entities: 2-9, 11-19, 101-109 (grouped by parent)
    - This makes debugging and referencing much easier

## Environment Instantiation

### Graph Environment Setup

The CustomerGraph environment defines how customers interact:

```xml
<csm:Environment>
  <!-- Define vertices (customers in the graph) -->
  <csm:Vertex>2</csm:Vertex>
  <csm:Vertex>3</csm:Vertex>
  <csm:Vertex>4</csm:Vertex>
  <csm:Vertex>5</csm:Vertex>
  
  <!-- Define arcs (directed relationships) -->
  <csm:Arc>
    <csm:Source>2</csm:Source>
    <csm:Destination>4</csm:Destination>
  </csm:Arc>
  <csm:Arc>
    <csm:Source>4</csm:Source>
    <csm:Destination>2</csm:Destination>
  </csm:Arc>
  <csm:Arc>
    <csm:Source>2</csm:Source>
    <csm:Destination>3</csm:Destination>
  </csm:Arc>
  <csm:Arc>
    <csm:Source>3</csm:Source>
    <csm:Destination>2</csm:Destination>
  </csm:Arc>
  <!-- More arcs... -->
</csm:Environment>
```

### Graph Structure Analysis

This creates a social network where:
- **Customer2** is highly connected (central hub)
- **Customer1, 3, 4** form a connected cluster
- Information flows bidirectionally between connected customers

### Other Environment Types

#### Regular Grid Environment

For spatial positioning (2D grid example):

```xml
<csm:Environment sizeX="5" sizeY="5">
  <csm:Entity id="2" placeX="1" placeY="1"/>
  <csm:Entity id="3" placeX="2" placeY="2"/>
  <csm:Entity id="4" placeX="3" placeY="3"/>
</csm:Environment>
```

#### Room Environment

For global interaction (all entities can interact):

```xml
<csm:Environment>
  <csm:EntityRef id="2"/>
  <csm:EntityRef id="3"/>
  <csm:EntityRef id="4"/>
  <csm:EntityRef id="5"/>
</csm:Environment>
```

## Scheduler Configuration

### Basic Scheduler Structure

The scheduler controls when and how entity behaviors execute:

```xml
<csm:Scheduler>
  <!-- Parallel execution of customer Perceive/React -->
  <csm:ParallelTaskBlock concurrencyLevel="1">
    <csm:ParallelTask>
      <csm:Entity>2</csm:Entity>
    </csm:ParallelTask>
    <csm:ParallelTask>
      <csm:Entity>3</csm:Entity>
    </csm:ParallelTask>
    <csm:ParallelTask>
      <csm:Entity>4</csm:Entity>
    </csm:ParallelTask>
    <csm:ParallelTask>
      <csm:Entity>5</csm:Entity>
    </csm:ParallelTask>
  </csm:ParallelTaskBlock>
  
  <!-- Sequential execution of customer updates -->
  <csm:SimpleTask>
    <csm:Entity>2</csm:Entity>
    <csm:Process>Update</csm:Process>
  </csm:SimpleTask>
  <csm:SimpleTask>
    <csm:Entity>3</csm:Entity>
    <csm:Process>Update</csm:Process>
  </csm:SimpleTask>
  <!-- More update tasks... -->
</csm:Scheduler>
```

### Task Types

| Task Type | Purpose | Example |
|-----------|---------|---------|
| **SimpleTask** | Execute a specific rule/process | Customer satisfaction update |
| **ParallelTaskBlock** | Execute multiple tasks simultaneously | All customers perceive together |
| **SequentialTaskBlock** | Execute tasks in order | Step-by-step process |
| **RepeatTaskBlock** | Repeat tasks multiple times | Simulation loops |

### Parallel vs Sequential Execution

```xml
<!-- Parallel: All customers perceive simultaneously -->
<csm:ParallelTaskBlock>
  <csm:ParallelTask><csm:Entity>2</csm:Entity></csm:ParallelTask>
  <csm:ParallelTask><csm:Entity>3</csm:Entity></csm:ParallelTask>
</csm:ParallelTaskBlock>

<!-- Sequential: Customers update one by one -->
<csm:SequentialTaskBlock>
  <csm:SimpleTask>
    <csm:Entity>2</csm:Entity>
    <csm:Process>Update</csm:Process>
  </csm:SimpleTask>
  <csm:SimpleTask>
    <csm:Entity>3</csm:Entity>
    <csm:Process>Update</csm:Process>
  </csm:SimpleTask>
</csm:SequentialTaskBlock>
```

!!! note "Perceive/React Pattern"
    Always use parallel tasks for Perceive and React rules to ensure all entities see the same state before updating. This prevents order-dependent simulation results.

## Model Root Structure

### Complete Instance File Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<csm:Model xmlns:csm="http://www.cosmotech.com/csm/11.3">
  
  <!-- Root scheduler that starts the simulation -->
  <csm:Scheduler>
    <csm:RunTask>
      <csm:Entity>1</csm:Entity>  <!-- Start with Bar entity -->
    </csm:RunTask>
  </csm:Scheduler>
  
  <!-- Main compound entity instance -->
  <csm:Bar id="1" name="MyBar">
    <!-- Bar configuration here -->
  </csm:Bar>
  
</csm:Model>
```

### Root Scheduler

The root scheduler is essential - it tells the simulation which entity to start with:

- **RunTask**: Executes the scheduler of a compound entity
- **Entity reference**: Points to the main compound entity (Bar in our case)

## Initialization Files

### Setting Initial Attribute Values

Create an initialization file (`.ini.xml`) to set specific starting conditions:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<csmIni:Model xmlns:csmIni="http://www.cosmotech.com/csm_initialization/4.0.1">
  
  <csmIni:Entity name="MyBar">
    <!-- Set bar attributes -->
    <csmIni:State>
      <csmIni:Attribute name="Stock">
        <csmIni:Value>15</csmIni:Value>
      </csmIni:Attribute>
      <csmIni:Attribute name="NbWaiters">
        <csmIni:Value>3</csmIni:Value>
      </csmIni:Attribute>
    </csmIni:State>
    
    <!-- Set customer attributes -->
    <csmIni:SubEntities>
      <csmIni:Entity name="Customer1">
        <csmIni:State>
          <csmIni:Attribute name="Satisfaction">
            <csmIni:Value>7</csmIni:Value>
          </csmIni:Attribute>
          <csmIni:Attribute name="Thirsty">
            <csmIni:Value>false</csmIni:Value>
          </csmIni:Attribute>
        </csmIni:State>
      </csmIni:Entity>
      
      <csmIni:Entity name="Customer2">
        <csmIni:State>
          <csmIni:Attribute name="Satisfaction">
            <csmIni:Value>3</csmIni:Value>
          </csmIni:Attribute>
          <csmIni:Attribute name="Thirsty">
            <csmIni:Value>true</csmIni:Value>
          </csmIni:Attribute>
        </csmIni:State>
      </csmIni:Entity>
      <!-- More customers... -->
    </csmIni:SubEntities>
  </csmIni:Entity>
  
</csmIni:Model>
```

### Initialization Best Practices

1. **Realistic Values**: Use domain-appropriate initial values
2. **Varied States**: Create interesting starting conditions
3. **Boundary Testing**: Include edge cases (empty stock, maximum satisfaction)

## Modification Files

### Runtime Model Changes

Modification files (`.mod.xml`) allow changes during simulation:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<csmMod:Modifications xmlns:csmMod="http://www.cosmotech.com/csm_modifications/4.0.1">
  
  <!-- Increase stock dramatically -->
  <csmMod:DOMAttrModified target="[Id=1]" 
                          scope="State" 
                          attributeName="Stock" 
                          operator="+" 
                          newValue="20"/>
  
  <!-- Add a new customer -->
  <csmMod:DOMNodeInserted target="MyBar">
    <csmMod:Customer id="6" name="Customer5">
      <csmMod:State>
        <csmMod:Satisfaction>5</csmMod:Satisfaction>
        <csmMod:Thirsty>true</csmMod:Thirsty>
      </csmMod:State>
    </csmMod:Customer>
  </csmMod:DOMNodeInserted>
  
  <!-- Remove a customer -->
  <csmMod:DOMNodeRemoved target="[Id=4]"/>
  
</csmMod:Modifications>
```

### Modification Operations

| Operation | Purpose | Example |
|-----------|---------|---------|
| **DOMAttrModified** | Change attribute values | Increase stock, modify satisfaction |
| **DOMNodeInserted** | Add new entities | New customers arrive |
| **DOMNodeRemoved** | Remove entities | Customers leave |

## Advanced Configuration Patterns

### Conditional Entity Creation

```xml
<!-- Create premium customers with higher initial satisfaction -->
<csm:Customer id="10" name="PremiumCustomer1">
  <!-- Will be initialized differently -->
</csm:Customer>
```

### Dynamic Graph Structures

```xml
<!-- Hub-and-spoke network -->
<csm:Arc><csm:Source>1</csm:Source><csm:Destination>2</csm:Destination></csm:Arc>
<csm:Arc><csm:Source>1</csm:Source><csm:Destination>3</csm:Destination></csm:Arc>
<csm:Arc><csm:Source>1</csm:Source><csm:Destination>4</csm:Destination></csm:Arc>

<!-- Ring network -->
<csm:Arc><csm:Source>2</csm:Source><csm:Destination>3</csm:Destination></csm:Arc>
<csm:Arc><csm:Source>3</csm:Source><csm:Destination>4</csm:Destination></csm:Arc>
<csm:Arc><csm:Source>4</csm:Source><csm:Destination>2</csm:Destination></csm:Arc>
```

### Complex Scheduler Patterns

```xml
<csm:Scheduler>
  <!-- Initialization phase -->
  <csm:SequentialTaskBlock>
    <csm:SimpleTask>
      <csm:Entity>1</csm:Entity>
      <csm:Process>Initialize</csm:Process>
    </csm:SimpleTask>
  </csm:SequentialTaskBlock>
  
  <!-- Main simulation loop -->
  <csm:RepeatTaskBlock repetitions="100">
    <csm:SequentialTaskBlock>
      <!-- Customer interactions -->
      <csm:ParallelTaskBlock>
        <!-- All customers perceive -->
      </csm:ParallelTaskBlock>
      <csm:ParallelTaskBlock>
        <!-- All customers react -->
      </csm:ParallelTaskBlock>
      
      <!-- Bar operations -->
      <csm:SimpleTask>
        <csm:Entity>1</csm:Entity>
        <csm:Process>Serve</csm:Process>
      </csm:SimpleTask>
    </csm:SequentialTaskBlock>
  </csm:RepeatTaskBlock>
  
  <!-- Cleanup phase -->
  <csm:SimpleTask>
    <csm:Entity>1</csm:Entity>
    <csm:Process>Cleanup</csm:Process>
  </csm:SimpleTask>
</csm:Scheduler>
```

## Data Types and Collections

### Working with Complex Attributes

```xml
<!-- List of integers -->
<csmIni:Attribute name="CustomerRatings">
  <csmIni:Value>[8, 6, 9, 7, 5]</csmIni:Value>
</csmIni:Attribute>

<!-- Map of string to integer -->
<csmIni:Attribute name="MenuPrices">
  <csmIni:Value>{"beer": 5, "wine": 8, "cocktail": 12}</csmIni:Value>
</csmIni:Attribute>

<!-- Composite type -->
<csmIni:Attribute name="BarInfo">
  <csmIni:Value>{"name": "CoSMo Brewery", "capacity": 50, "isOpen": true}</csmIni:Value>
</csmIni:Attribute>
```

### Partial Initialization

```xml
<!-- Initialize specific list elements -->
<csmIni:Attribute name="CustomerRatings::0">
  <csmIni:Value>10</csmIni:Value>
</csmIni:Attribute>

<!-- Initialize specific map values -->
<csmIni:Attribute name="MenuPrices::beer">
  <csmIni:Value>6</csmIni:Value>
</csmIni:Attribute>

<!-- Initialize composite fields -->
<csmIni:Attribute name="BarInfo::capacity">
  <csmIni:Value>75</csmIni:Value>
</csmIni:Attribute>
```

## File Organization Best Practices

### Recommended File Structure

```
Simulation/
├── MyBrewery.ist.xml              # Main instance file
├── BreweryInitialization.ini.xml  # Initial values
├── Scenarios/
│   ├── RushHour.mod.xml          # Peak time scenario
│   ├── StockOutage.mod.xml       # Emergency scenario
│   └── NewCustomers.mod.xml      # Growth scenario
└── Variations/
    ├── SmallBar.ist.xml          # 2 customers, 1 waiter
    ├── LargeBar.ist.xml          # 20 customers, 5 waiters
    └── SocialHubBar.ist.xml      # Dense network
```

### Naming Conventions

- **Descriptive Names**: `CustomerSatisfiedHigh` vs `Customer1`
- **Systematic IDs**: Main entities (1, 10, 100), sub-entities (2-9, 11-19)
- **File Prefixes**: `Basic_`, `Test_`, `Scenario_` for different purposes

## Testing and Validation

### Create Test Scenarios

```xml
<!-- Minimal test case -->
<csm:Bar id="1" name="TestBar">
  <csm:Compound>
    <csm:SubEntities>
      <csm:Customer id="2" name="TestCustomer"/>
    </csm:SubEntities>
    <csm:Environment>
      <csm:Vertex>2</csm:Vertex>
    </csm:Environment>
  </csm:Compound>
  <csm:Scheduler>
    <csm:SimpleTask>
      <csm:Entity>2</csm:Entity>
      <csm:Process>Update</csm:Process>
    </csm:SimpleTask>
  </csm:Scheduler>
</csm:Bar>
```

### Validation Patterns

```xml
<!-- Boundary value testing -->
<csmIni:Attribute name="Satisfaction">
  <csmIni:Value>0</csmIni:Value>      <!-- Minimum -->
</csmIni:Attribute>
<csmIni:Attribute name="Satisfaction">
  <csmIni:Value>10</csmIni:Value>     <!-- Maximum -->
</csmIni:Attribute>

<!-- Edge cases -->
<csmIni:Attribute name="Stock">
  <csmIni:Value>0</csmIni:Value>      <!-- Empty stock -->
</csmIni:Attribute>
<csmIni:Attribute name="NbWaiters">
  <csmIni:Value>1</csmIni:Value>      <!-- Minimal staff -->
</csmIni:Attribute>
```

## Exercise: Creating Your Own Instance

Try creating a new bar instance with these specifications:

1. **Sports Bar**:
   - 8 customers in two groups (sports fans vs casual)
   - Higher stock (25 units)
   - 4 waiters
   - Two separate customer clusters in the graph

2. **Initialization**:
   - Sports fans start with high satisfaction (8-9)
   - Casual customers start with medium satisfaction (4-6)
   - Half the customers start thirsty

3. **Scheduler**:
   - Add a periodic restock task every 5 simulation steps
   - Include meso-function updates for tracking

## Next Steps

In the next tutorial, you'll learn about:

- **Probing and Monitoring**: Collecting data during simulation
- **Consumer Configuration**: Processing and visualizing results
- **Real-time Analysis**: Understanding simulation behavior
- **Output Formats**: CSV, charts, and custom displays

Your model instances define the *initial conditions* - the next tutorial shows you how to *observe* what happens during simulation!