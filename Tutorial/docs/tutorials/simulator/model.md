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

The conceptual model defines *what* your system contains - rules and processes define *how* it behaves!