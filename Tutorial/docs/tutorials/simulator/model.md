# Model

!!! info "Tutorial Duration: 15-20 minutes"

This tutorial guides you through the fundamentals of conceptual modeling with CoSML (Complex System Modeling Language). You'll learn to define entities, their relationships, and create hierarchical structures that form the foundation of your simulations.

## Learning Objectives

By the end of this tutorial, you will understand:

- How to define basic and compound entities
- Entity state attributes and data types
- Environment types and their usage
- Entity relationships and hierarchies
- The structure of a CoSML conceptual model

## Introduction to CoSML

CoSML (Complex System Modeling Language) is the core language for describing complex systems in CoSMo. It allows you to model:

- **Entities**: The building blocks of your system (basic or compound)
- **Environments**: The spaces where entities interact
- **Relationships**: How entities are connected and communicate
- **Hierarchies**: How entities contain other entities

Let's explore these concepts using a brewery simulation as our example.

## The Brewery Model Overview

Our brewery model simulates customer satisfaction in a bar environment:

- **Customers** interact with each other and influence satisfaction levels
- A **Bar** serves customers and manages stock
- Customers form a **social network** where satisfaction spreads
- The bar has **waiters** and **stock** that affect service quality

## Data Types

Every CoSML model starts with basic data type definitions:

```xml
<csm:DataType xsi:type="csm:BoolType" name="Boolean"/>
<csm:DataType xsi:type="csm:IntType" name="Integer" bits="32" signed="1"/>
<csm:DataType xsi:type="csm:FloatType" name="FloatingPoint" bits="32"/>
<csm:DataType xsi:type="csm:StringType" name="String"/>
```

These define the primitive types used throughout your model for entity attributes.

## Basic Entities

### Customer Entity

A basic entity represents individual objects in your system. Here's our Customer entity:

```xml
<csm:BasicEntity name="Customer">
  <csm:State xsi:type="csm:CompositeType" name="CustomerState">
    <csm:Container>
      <csm:Attribute name="Thirsty">
        <csm:DataTypeReference>Boolean</csm:DataTypeReference>
      </csm:Attribute>
      <csm:Attribute name="Satisfaction">
        <csm:DataTypeReference>Integer</csm:DataTypeReference>
      </csm:Attribute>
      <csm:Attribute name="SurroundingSatisfaction">
        <csm:DataTypeReference>Integer</csm:DataTypeReference>
      </csm:Attribute>
    </csm:Container>
  </csm:State>
  
  <!-- Rules and processes will be covered in the Rules tutorial -->
</csm:BasicEntity>
```

### Entity State

The **State** defines what information each entity instance holds:

- `Thirsty`: Whether the customer wants a drink
- `Satisfaction`: The customer's current satisfaction level  
- `SurroundingSatisfaction`: Average satisfaction of nearby customers

!!! tip "State Design"
    Design your entity state to include only the essential information needed for your simulation logic. Too many attributes can make models complex and slow.

## Compound Entities

Compound entities contain other entities and represent higher-level organizational structures:

```xml
<csm:CompoundEntity name="Bar">
  <csm:State xsi:type="csm:CompositeType" name="BarState">
    <csm:Container>
      <csm:Attribute name="Stock">
        <csm:DataTypeReference>Integer</csm:DataTypeReference>
        <csm:DefaultValue>10</csm:DefaultValue>
      </csm:Attribute>
      <csm:Attribute name="RestockQty">
        <csm:DataTypeReference>Integer</csm:DataTypeReference>
        <csm:DefaultValue>10</csm:DefaultValue>
      </csm:Attribute>
      <csm:Attribute name="NbWaiters">
        <csm:DataTypeReference>Integer</csm:DataTypeReference>
        <csm:DefaultValue>2</csm:DefaultValue>
      </csm:Attribute>
    </csm:Container>
  </csm:State>
  
  <csm:Compound>
    <csm:Environment>CustomerGraph</csm:Environment>
    <csm:SubEntities>
      <csm:CanBeWithin>Customer</csm:CanBeWithin>
    </csm:SubEntities>
  </csm:Compound>
</csm:CompoundEntity>
```

### Key Compound Entity Features

1. **Own State**: The bar has its own attributes (stock, waiters, etc.)
2. **Sub-entities**: Specifies which entity types can be contained (`Customer`)
3. **Environment**: Defines how sub-entities interact (`CustomerGraph`)
4. **Default Values**: Attributes can have initial values

## Meso-State and Meso-Functions

Compound entities can have **meso-state** - computed information about their sub-entities:

```xml
<csm:MesoState xsi:type="csm:CompositeType" name="BarMesoState">
  <csm:Container>
    <csm:Attribute name="AverageSatisfaction">
      <csm:DataTypeReference>Integer</csm:DataTypeReference>
    </csm:Attribute>
    <csm:Attribute name="NbThirstyCustomers">
      <csm:DataTypeReference>Integer</csm:DataTypeReference>
    </csm:Attribute>
  </csm:Container>
</csm:MesoState>

<csm:MesoFunction name="UpdateAverageSatisfaction">
  <csm:Operations>
    <csm:Operation xsi:type="csm:MesoAverage" 
                   target="AverageSatisfaction" 
                   subEntityAttribute="Satisfaction"/>
  </csm:Operations>
</csm:MesoFunction>

<csm:MesoFunction name="UpdateNbThirstyCustomers">
  <csm:Operations>
    <csm:Operation xsi:type="csm:MesoValueCount" 
                   target="NbThirstyCustomers" 
                   subEntityAttribute="Thirsty" 
                   attributeValue="true"/>
  </csm:Operations>
</csm:MesoFunction>
```

!!! note "Meso-Functions"
    Meso-functions automatically compute aggregate information about sub-entities. Common operations include:
    - `MesoAverage`: Average of an attribute
    - `MesoValueCount`: Count entities with specific attribute values
    - `MesoSum`: Sum of an attribute

## Environments

Environments define how entities interact spatially or logically. Our brewery uses a **Graph Environment**:

```xml
<csm:Environment xsi:type="csm:Graph" name="CustomerGraph">
  <csm:Arc name="Satisfaction">
    <csm:Source>Customer</csm:Source>
    <csm:Destination>Customer</csm:Destination>
  </csm:Arc>
</csm:Environment>
```

### Environment Types

CoSMo supports several environment types:

| Environment Type | Usage | Example |
|-----------------|-------|---------|
| **Graph** | Network relationships | Social networks, communication |
| **Regular Grid** | Spatial positioning | Geographic locations, cellular automata |
| **Room** | Global interaction | Shared spaces where all entities interact |
| **No Environment** | No spatial relationships | Independent entities |

## Communication

Entities communicate through **Communicators** - defined channels for information exchange:

```xml
<csm:Communicator name="CustomerToCustomer">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[return GetSatisfaction();]]></csm:Code>
  </csm:Implementation>
  <csm:CommunicatedReference>Integer</csm:CommunicatedReference>
  <csm:Destination>Customer</csm:Destination>
</csm:Communicator>
```

This communicator allows customers to share their satisfaction level with other customers in their network.

## Model Hierarchy Structure

```
Model (Root)
└── Bar (Compound Entity)
    ├── Environment: CustomerGraph
    ├── Sub-entities: Customer instances
    └── Meso-state: Aggregated customer data
```

## Complete Model Structure

Here's how all these pieces fit together in the complete model:

```xml
<csm:Model xmlns:csm="http://www.cosmotech.com/csm/11.3">
  <csm:ModelName>Brewery</csm:ModelName>
  
  <!-- Root compound with model environment -->
  <csm:Compound>
    <csm:Environment>ModelEnvironment</csm:Environment>
  </csm:Compound>
  
  <!-- Data types -->
  <csm:DataType xsi:type="csm:BoolType" name="Boolean"/>
  <!-- ... other data types ... -->
  
  <!-- Basic entities -->
  <csm:BasicEntity name="Customer">
    <!-- Customer definition -->
  </csm:BasicEntity>
  
  <!-- Compound entities -->
  <csm:CompoundEntity name="Bar">
    <!-- Bar definition -->
  </csm:CompoundEntity>
  
  <!-- Environments -->
  <csm:Environment xsi:type="csm:Graph" name="CustomerGraph">
    <!-- Graph definition -->
  </csm:Environment>
  
</csm:Model>
```

## Best Practices

1. **Start Simple**: Begin with basic entities and add complexity gradually
2. **Clear Naming**: Use descriptive names for entities, attributes, and environments
3. **Logical Hierarchy**: Organize entities in a way that reflects real-world relationships
4. **Minimal State**: Include only necessary attributes in entity state
5. **Document Purpose**: Use documentation elements to explain entity purposes

## Exercise: Extending the Model

Try adding a new entity type to the brewery model:

1. **Waiter Entity**: 
   - State: `Busy` (Boolean), `ServingCustomer` (Integer reference)
   - Should be a sub-entity of Bar
   - Add to the customer service process

2. **Menu Item Entity**:
   - State: `Name` (String), `Price` (Float), `InStock` (Boolean)
   - Create a compound entity for Menu that contains items

## Next Steps

Now that you understand the conceptual model structure, the next tutorial will cover:

- **Rules and Processes**: How entities behave and interact
- **Entity communication patterns**
- **Scheduling entity behaviors**

The conceptual model defines *what* your system contains - rules and processes define *how* it behaves!</new_str>
</str_replace_editor>

Now let me create the comprehensive rules.md tutorial:

<str_replace_editor>
<command>str_replace</command>
<path>Tutorial/docs/tutorials/simulator/rules.md</path>
<old_str># Rules</old_str>
<new_str># Rules and Processes

!!! info "Tutorial Duration: 20-25 minutes"

This tutorial covers entity behavior implementation through rules and processes. You'll learn how entities interact, communicate, and evolve over time using the Brewery model as a practical example.

## Learning Objectives

By the end of this tutorial, you will understand:

- The difference between rules and processes
- How to implement entity behavior in C++/Python
- Communication patterns between entities
- The Perceive/React design pattern
- Meso-functions for compound entity computations
- Scheduling and task execution

## Rules vs Processes

In CoSMo, entity behavior is implemented through two main mechanisms:

| Type | Purpose | Environment Required | Example Use |
|------|---------|---------------------|-------------|
| **Rules** | Environment-dependent behavior | Yes | Social interactions, spatial movement |
| **Processes** | Internal entity updates | No | State updates, internal calculations |

## Basic Processes

### Customer Update Process

Let's start with a simple process that updates customer thirst:

```xml
<csm:Process name="Update">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
int test = rand()%10 + GetSatisfaction();
if (!GetThirsty() && test > 5)
{
  SetThirsty(true);
}
    ]]></csm:Code>
  </csm:Implementation>
</csm:Process>
```

### Process Breakdown

- **No Environment**: Processes don't require environment context
- **State Access**: Use `Get[AttributeName]()` and `Set[AttributeName](value)`
- **Logic**: Customers become thirsty based on satisfaction and randomness
- **Probability**: Higher satisfaction = higher chance of becoming thirsty

!!! tip "State Access Methods"
    CoSMo automatically generates getter/setter methods for all entity attributes:
    - `GetSatisfaction()` → reads the Satisfaction attribute
    - `SetThirsty(true)` → sets the Thirsty attribute to true

## The Perceive/React Pattern

For environment-dependent behavior, CoSMo uses the **Perceive/React** pattern:

1. **Perceive**: Gather information from neighbors
2. **React**: Update state based on perceived information

### Customer Perceive Rule

```xml
<csm:Rule name="Perceive">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
int satisfaction = 0;
int count = 0;

// Iterate through all Customer neighbors in the graph
csmForEachTypedNeighbor(BE_Customer, neighbor)
{
  // Get satisfaction from each neighbor via communicator
  satisfaction += neighbor->GetCustomerToCustomer(this);
  count++;
}

// Calculate average satisfaction of neighbors
int avrg = int(float(satisfaction) / float(csm::Math::Max(count, 1)));
SetSurroundingSatisfaction(avrg);
    ]]></csm:Code>
  </csm:Implementation>
  <csm:EnvironmentReference>CustomerGraph</csm:EnvironmentReference>
</csm:Rule>
```

### Customer React Rule

```xml
<csm:Rule name="React">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
int satisfaction = GetSatisfaction();

// Adjust satisfaction based on neighbors
if (satisfaction > GetSurroundingSatisfaction())
{
  SetSatisfaction(satisfaction - 1);  // Decrease if above average
}
else if (satisfaction < GetSurroundingSatisfaction())
{
  SetSatisfaction(satisfaction + 1);  // Increase if below average
}
    ]]></csm:Code>
  </csm:Implementation>
  <csm:EnvironmentReference>CustomerGraph</csm:EnvironmentReference>
</csm:Rule>
```

### Key Perceive/React Concepts

- **Environment Reference**: Both rules specify which environment they operate in
- **Neighbor Iteration**: `csmForEachTypedNeighbor` loops through connected entities
- **Communication**: Entities request data from neighbors via communicators
- **Separation**: Perceive gathers data, React makes decisions

!!! note "Why Perceive/React?"
    This pattern ensures simultaneous updates. All entities perceive the current state, then all react based on that snapshot, preventing order-dependent results.

## Communicators

Communicators define how entities share information:

```xml
<csm:Communicator name="CustomerToCustomer">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
return GetSatisfaction();
    ]]></csm:Code>
  </csm:Implementation>
  <csm:CommunicatedReference>Integer</csm:CommunicatedReference>
  <csm:Destination>Customer</csm:Destination>
</csm:Communicator>
```

### Communicator Components

- **Implementation**: Code that computes the shared value
- **CommunicatedReference**: Data type being shared
- **Destination**: Which entity types can receive this data

### Using Communicators

In the Perceive rule, neighbors are accessed via the communicator:

```cpp
neighbor->GetCustomerToCustomer(this)
```

This calls the `CustomerToCustomer` communicator on the neighbor, passing `this` customer as the destination.

## Compound Entity Processes

Compound entities can have processes that operate on their sub-entities:

### Bar Serve Process

```xml
<csm:Process name="Serve">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
// Calculate service capacity based on waiters and demand
int thirsty = GetNbThirstyCustomers();
int waiters = GetNbWaiters();
int threshold = int(10.f * float(waiters) / float(csm::Math::Max(thirsty, 1)));

// Iterate through all customer sub-entities
csmForEachTypedSubEntity(BE_Customer, customer)
{
  if (customer->GetThirsty())
  {
    // Probability of service based on waiter availability
    int test = rand()%10 + 1;
    if (test <= threshold && GetStock() > 0)
    {
      // Successful service
      SetStock(GetStock() - 1);
      customer->SetThirsty(false);
      customer->SetSatisfaction(customer->GetSatisfaction() + 1);
    }
    else if (customer->GetSatisfaction() > 0)
    {
      // Poor service reduces satisfaction
      customer->SetSatisfaction(customer->GetSatisfaction() - 1);
    }
  }
}
    ]]></csm:Code>
  </csm:Implementation>
</csm:Process>
```

### Bar Restock Process

```xml
<csm:Process name="Restock">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
SetStock(GetStock() + GetRestockQty());
    ]]></csm:Code>
  </csm:Implementation>
</csm:Process>
```

### Compound Process Features

- **Sub-entity Access**: `csmForEachTypedSubEntity` iterates through contained entities
- **Direct Manipulation**: Compound entities can modify sub-entity state
- **Business Logic**: Complex algorithms involving multiple entities
- **Resource Management**: Stock tracking and consumption

## Meso-Functions

Meso-functions compute aggregate information about sub-entities:

### Automatic Meso-Functions

```xml
<csm:MesoFunction name="UpdateAverageSatisfaction">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[]]></csm:Code>
  </csm:Implementation>
  <csm:Operations>
    <csm:Operation xsi:type="csm:MesoAverage" 
                   target="AverageSatisfaction" 
                   subEntityAttribute="Satisfaction"/>
  </csm:Operations>
</csm:MesoFunction>

<csm:MesoFunction name="UpdateNbThirstyCustomers">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[]]></csm:Code>
  </csm:Implementation>
  <csm:Operations>
    <csm:Operation xsi:type="csm:MesoValueCount" 
                   target="NbThirstyCustomers" 
                   subEntityAttribute="Thirsty" 
                   attributeValue="true"/>
  </csm:Operations>
</csm:MesoFunction>
```

### Built-in Meso-Operations

| Operation | Purpose | Example |
|-----------|---------|---------|
| `MesoAverage` | Calculate mean value | Average customer satisfaction |
| `MesoValueCount` | Count specific values | Number of thirsty customers |
| `MesoSum` | Sum all values | Total satisfaction points |
| `MesoMin/MesoMax` | Find extremes | Most/least satisfied customer |

### Custom Meso-Functions

You can also implement custom logic:

```xml
<csm:MesoFunction name="CalculateServiceQuality">
  <csm:Implementation language="cpp">
    <csm:Code><![CDATA[
float totalSatisfaction = 0;
int customerCount = 0;

csmForEachTypedSubEntity(BE_Customer, customer)
{
  totalSatisfaction += customer->GetSatisfaction();
  customerCount++;
}

float avgSatisfaction = totalSatisfaction / csm::Math::Max(customerCount, 1);
float serviceQuality = avgSatisfaction * GetStock() / 10.0f;

SetServiceQuality(serviceQuality);
    ]]></csm:Code>
  </csm:Implementation>
</csm:MesoFunction>
```

## Advanced Behavior Patterns

### Conditional Logic

```cpp
// State-dependent behavior
if (GetStock() < 5)
{
  // Emergency restocking
  SetRestockQty(20);
}
else if (GetStock() > 50)
{
  // Reduce waste
  SetRestockQty(5);
}
```

### Probabilistic Behavior

```cpp
// Random events with weighted probability
int satisfaction = GetSatisfaction();
int randomValue = rand() % 100;
int threshold = 20 + satisfaction * 5;  // Higher satisfaction = higher chance

if (randomValue < threshold)
{
  // Positive event occurs
  PerformAction();
}
```

### Neighbor-Dependent Actions

```cpp
// Actions based on neighbor count and state
int happyNeighbors = 0;
int totalNeighbors = 0;

csmForEachTypedNeighbor(BE_Customer, neighbor)
{
  totalNeighbors++;
  if (neighbor->GetSatisfaction() > 7)
  {
    happyNeighbors++;
  }
}

float happyRatio = float(happyNeighbors) / float(csm::Math::Max(totalNeighbors, 1));
if (happyRatio > 0.7f)
{
  // Surrounded by happy customers
  SetSatisfaction(GetSatisfaction() + 2);
}
```

## Code Organization Best Practices

### 1. Clear Variable Names

```cpp
// Good
int currentSatisfaction = GetSatisfaction();
int neighborSatisfactionSum = 0;
int neighborCount = 0;

// Avoid
int s = GetSatisfaction();
int sum = 0;
int c = 0;
```

### 2. Separate Concerns

```cpp
// Calculate neighbor influence
int neighborInfluence = CalculateNeighborInfluence();

// Apply influence to self
ApplyInfluence(neighborInfluence);

// Update internal state
UpdateInternalState();
```

### 3. Use Helper Functions

```cpp
// In a meso-function or complex process
bool IsBarBusy()
{
  return GetNbThirstyCustomers() > GetNbWaiters() * 3;
}

float CalculateServiceEfficiency()
{
  return float(GetNbWaiters()) / float(csm::Math::Max(GetNbThirstyCustomers(), 1));
}
```

## Common Implementation Patterns

### 1. Threshold-Based Behavior

```cpp
const int SATISFACTION_THRESHOLD = 5;
const int STOCK_LOW_THRESHOLD = 3;

if (GetSatisfaction() < SATISFACTION_THRESHOLD)
{
  // Low satisfaction behavior
}

if (GetStock() < STOCK_LOW_THRESHOLD)
{
  // Emergency restocking
}
```

### 2. State Machine Pattern

```cpp
enum CustomerState { CONTENT, THIRSTY, DRINKING, LEAVING };

switch (GetCurrentState())
{
  case CONTENT:
    if (ShouldBecomeThirsty()) TransitionTo(THIRSTY);
    break;
  case THIRSTY:
    if (IsServed()) TransitionTo(DRINKING);
    break;
  // ... other states
}
```

### 3. Weighted Decision Making

```cpp
struct Decision {
  std::string action;
  float weight;
};

std::vector<Decision> decisions = {
  {"stay", GetSatisfaction() * 0.1f},
  {"leave", (10 - GetSatisfaction()) * 0.2f},
  {"order", GetThirsty() ? 0.8f : 0.1f}
};

// Select action based on weights
std::string chosenAction = SelectWeightedRandom(decisions);
```

## Debugging and Testing

### Adding Debug Output

```cpp
// Use CoSMo logging
csm::Log().Info("Customer {} satisfaction: {}", GetId(), GetSatisfaction());
csm::Log().Debug("Neighbor count: {}", neighborCount);
```

### Validation Checks

```cpp
// Ensure valid ranges
if (GetSatisfaction() < 0) SetSatisfaction(0);
if (GetSatisfaction() > 10) SetSatisfaction(10);

// Check preconditions
assert(GetStock() >= 0);
assert(GetNbWaiters() > 0);
```

## Exercise: Implementing New Behaviors

Try implementing these additional behaviors:

### 1. Customer Loyalty System

Add a `Loyalty` attribute and implement:
- Loyalty increases with good service
- Loyal customers are more forgiving of poor service
- Loyalty affects tip amounts

### 2. Peak Hours Simulation

Implement time-based behavior:
- More customers become thirsty during peak hours
- Bar adjusts waiter count based on time
- Price changes based on demand

### 3. Social Influence Propagation

Enhance the social network:
- Extremely satisfied customers create "viral" satisfaction
- Dissatisfied customers have stronger negative influence
- Implement influence decay over network distance

## Next Steps

In the next tutorial, you'll learn about:

- **Model Instantiation**: Creating entity instances
- **Environment Setup**: Defining spatial relationships
- **Scheduler Configuration**: Controlling when behaviors execute
- **Initialization**: Setting initial conditions

The rules and processes you've learned here define *how* entities behave - the next tutorial covers *when* and *where* they behave!