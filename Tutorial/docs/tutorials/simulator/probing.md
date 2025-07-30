# Probing and Data Collection

!!! info "Tutorial Duration: 15-20 minutes"

This tutorial covers how to collect, monitor, and analyze data from your simulations. You'll learn to configure probes for data collection and consumers for processing results using the Brewery simulation example.

## Learning Objectives

By the end of this tutorial, you will understand:

- The difference between probes and consumers
- How to configure probes to collect simulation data
- Setting up consumers for data processing and visualization
- Real-time monitoring and analysis techniques
- Export formats and post-simulation analysis

## Probes vs Consumers

The CoSMo data collection system separates *what* to observe from *how* to process it:

| Component | Purpose | Example |
|-----------|---------|---------|
| **Probe** | Defines *what* data to collect | Monitor customer satisfaction |
| **Consumer** | Defines *how* to process data | Export to CSV, display charts |

This separation allows you to:
- Use one probe with multiple consumers (CSV + real-time chart)
- Reuse consumers with different probes
- Easily switch between analysis methods

## Basic Probe Configuration

### Stock Monitoring Probe

Let's start with monitoring the bar's stock level:

```xml
<csmSon:ProbeInstance name="StockProbe" 
                      type="GenericAttributesSender" 
                      location="Simulation" 
                      loader="GenericAttributes" 
                      tag="1::1::1::0.5" 
                      active="1">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar</csmSon:Name>
    <csmSon:AttributeName>Stock</csmSon:AttributeName>
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

### Probe Configuration Elements

- **name**: Unique identifier for the probe
- **type**: `GenericAttributesSender` for basic attribute monitoring
- **location**: `Simulation` means it's scheduled in the simulation scheduler
- **tag**: When in the simulation schedule to execute (more on this below)
- **active**: `1` (enabled) or `0` (disabled)

### Entity and Attribute Selection

```xml
<csmSon:Entity>
  <csmSon:Name>Model::MyBar</csmSon:Name>
  <csmSon:AttributeName>Stock</csmSon:AttributeName>
</csmSon:Entity>
```

- **Name**: Path to the entity (`Model::EntityName`)
- **AttributeName**: Which attribute to monitor

## Advanced Probe Configuration

### Multiple Attributes from One Entity

```xml
<csmSon:ProbeInstance name="BarProbe" 
                      type="GenericAttributesSender" 
                      location="Simulation" 
                      loader="GenericAttributes" 
                      tag="1::1::1::0.7">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar</csmSon:Name>
    <csmSon:AttributeName>Stock</csmSon:AttributeName>
    <csmSon:AttributeName>NbWaiters</csmSon:AttributeName>
    <csmSon:AttributeName>RestockQty</csmSon:AttributeName>
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

### Sub-Entity Monitoring

Monitor attributes from all customers in the bar:

```xml
<csmSon:ProbeInstance name="SatisfactionProbe" 
                      type="GenericAttributesSender" 
                      location="Simulation" 
                      loader="GenericAttributes" 
                      tag="1::1::1::0.6">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar</csmSon:Name>
    <csmSon:SubEntityTypeName>Customer</csmSon:SubEntityTypeName>
    <csmSon:AttributeName>Satisfaction</csmSon:AttributeName>
    <csmSon:AttributeName>Thirsty</csmSon:AttributeName>
    <csmSon:AttributeName>SurroundingSatisfaction</csmSon:AttributeName>
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

### Key Sub-Entity Features

- **SubEntityTypeName**: Specifies which type of sub-entities to monitor
- **Multiple Attributes**: Can monitor several attributes per sub-entity
- **Automatic Collection**: Gathers data from all instances of that type

## Scheduling Probes

### Understanding Tags

Tags control when probes execute in the simulation schedule:

```
tag="1::1::1::0.5"
     │  │  │   └── Position within the task block (0.5 = between 0 and 1)
     │  │  └────── Task block level
     │  └───────── Repeat block iteration
     └──────────── Top level position
```


### Coordinating with Simulation Schedule

Match probe timing to your simulation structure:

```xml
<!-- Simulation scheduler structure -->
<csmSon:RepeatTaskBlock repetitions="10">           <!-- tag: 1 -->
  <csmSon:WhileTaskBlock>                           <!-- tag: 1::1 -->
    <csmSon:SequentialTaskBlock>                    <!-- tag: 1::1::1 -->
      <csmSon:ModelTask>Model</csmSon:ModelTask>    <!-- tag: 1::1::1::1 -->
      <!-- Probe here: tag="1::1::1::1.5" -->
      <csmSon:SimpleTask>...</csmSon:SimpleTask>    <!-- tag: 1::1::1::2 -->
    </csmSon:SequentialTaskBlock>
  </csmSon:WhileTaskBlock>
  <csmSon:SimpleTask>Restock</csmSon:SimpleTask>    <!-- tag: 1::2 -->
</csmSon:RepeatTaskBlock>
```

## Consumer Configuration

### CSV File Consumer

Export data to CSV files for analysis:

```xml
<csmSon:ConsumerInstance name="StockProbeCSV" type="CSVFileGenericConsumer">
  <csmSon:Properties>
    <csmSon:Property name="FileName">Stock</csmSon:Property>
    <csmSon:Property name="FileType">all</csmSon:Property>
    <csmSon:Property name="RowType">iteration</csmSon:Property>
    <csmSon:Property name="ColumnType">attribute</csmSon:Property>
    <csmSon:Property name="Separator">,</csmSon:Property>
    <csmSon:Property name="DesignateEntityByName">1</csmSon:Property>
    <csmSon:Property name="OutputDirectory"/>
  </csmSon:Properties>
  <csmSon:ProbeReferences>
    <csmSon:ProbeReference>StockProbe</csmSon:ProbeReference>
  </csmSon:ProbeReferences>
</csmSon:ConsumerInstance>
```

### CSV Consumer Properties

| Property | Values | Purpose |
|----------|--------|---------|
| **FileType** | `all`, `iteration`, `entity`, `attribute` | How to organize files |
| **RowType** | `iteration`, `entity`, `attribute` | What varies between rows |
| **ColumnType** | `iteration`, `entity`, `attribute` | What varies between columns |
| **Separator** | `,`, `;`, `\t` | CSV delimiter |
| **DesignateEntityByName** | `0`, `1` | Use names vs IDs |

### File Organization Examples

#### All Data in One File (FileType="all")

```csv
iteration,entity,attribute,value
1,MyBar,Stock,10
1,Customer1,Satisfaction,7
1,Customer2,Satisfaction,3
2,MyBar,Stock,9
2,Customer1,Satisfaction,6
```

#### Separate Files per Entity (FileType="entity")

**Stock_MyBar.csv:**
```csv
iteration,Stock,NbWaiters
1,10,2
2,9,2
3,8,2
```

**Satisfaction_Customer1.csv:**
```csv
iteration,Satisfaction,Thirsty
1,7,false
2,6,false
3,7,true
```

### Real-Time Chart Consumer

Display data in real-time during simulation:

```xml
<csmSon:ConsumerInstance name="SatisfactionChart" type="QtGenericChartViewerGL">
  <csmSon:Properties>
    <csmSon:Property name="SelectionMode">AllEntities</csmSon:Property>
    <csmSon:Property name="Layout">GroupedByEntity</csmSon:Property>
    <csmSon:Property name="PlottingType">Curve</csmSon:Property>
    <csmSon:Property name="PlottingDimension">2D</csmSon:Property>
    <csmSon:Property name="XDataRepresents">SimulationStep</csmSon:Property>
    <csmSon:Property name="YDataRepresents">SimulationData</csmSon:Property>
    <csmSon:Property name="YAttributeReferenceType">EveryAttributes</csmSon:Property>
  </csmSon:Properties>
  <csmSon:ProbeReferences>
    <csmSon:ProbeReference>SatisfactionProbe</csmSon:ProbeReference>
  </csmSon:ProbeReferences>
</csmSon:ConsumerInstance>
```

### Chart Consumer Properties

| Property | Values | Purpose |
|----------|--------|---------|
| **PlottingType** | `Curve`, `Histogram`, `PointCloud` | Chart type |
| **Layout** | `GroupedByEntity`, `GroupedByAttribute`, `MultiGraph` | Organization |
| **XDataRepresents** | `SimulationStep`, `EntityID`, `SimulationData` | X-axis data |
| **YDataRepresents** | `SimulationStep`, `EntityID`, `SimulationData` | Y-axis data |

## Complete Simulation Configuration

### Simulation File Structure

```xml
<csmSon:Simulation xmlns:csmSon="http://www.cosmotech.com/csm_simulation/11.3">
  
  <!-- Model instance configuration -->
  <csmSon:ModelInstance type="Brewery" id="Model">
    <csmSon:Loader type="csmXMLInstantiation">
      <csmSon:Property name="File">MyBrewery</csmSon:Property>
    </csmSon:Loader>
  </csmSon:ModelInstance>
  
  <!-- Simulation scheduler -->
  <csmSon:SimulatorInstance>
    <csmSon:SchedulerInstance>
      <!-- Main simulation loop -->
    </csmSon:SchedulerInstance>
    
    <!-- Probe instances -->
    <csmSon:ProbeInstances>
      <!-- Stock monitoring -->
      <csmSon:ProbeInstance name="StockProbe">
        <!-- Configuration -->
      </csmSon:ProbeInstance>
      
      <!-- Customer satisfaction monitoring -->
      <csmSon:ProbeInstance name="SatisfactionProbe">
        <!-- Configuration -->
      </csmSon:ProbeInstance>
    </csmSon:ProbeInstances>
    
    <!-- Consumer instances -->
    <csmSon:ConsumerInstances>
      <!-- CSV export -->
      <csmSon:ConsumerInstance name="StockProbeCSV">
        <!-- Configuration -->
      </csmSon:ConsumerInstance>
      
      <!-- Real-time chart -->
      <csmSon:ConsumerInstance name="SatisfactionChart">
        <!-- Configuration -->
      </csmSon:ConsumerInstance>
    </csmSon:ConsumerInstances>
  </csmSon:SimulatorInstance>
  
</csmSon:Simulation>
```

## Advanced Probing Techniques

### Meso-State Monitoring

Monitor computed aggregate values:

```xml
<csmSon:ProbeInstance name="BarMetricsProbe" 
                      type="GenericAttributesSender" 
                      location="Simulation" 
                      loader="GenericAttributes" 
                      tag="1::1::1::0.8">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar</csmSon:Name>
    <csmSon:AttributeName>AverageSatisfaction</csmSon:AttributeName>
    <csmSon:AttributeName>NbThirstyCustomers</csmSon:AttributeName>
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

### Conditional Probing

Use probe activation to monitor only when needed:

```xml
<!-- Active only during specific conditions -->
<csmSon:ProbeInstance name="ConditionalProbe" 
                      active="1"
                      tag="1::1::1::0.5">
  <!-- Can be activated/deactivated programmatically -->
</csmSon:ProbeInstance>
```

### Environment Monitoring

Monitor environment-specific data like network connections:

```xml
<csmSon:ProbeInstance name="NetworkProbe" 
                      type="GraphProbe" 
                      location="Model" 
                      entity="MyBar" 
                      tag="1::1::1::0.3">
  <csmSon:Graph owner="MyBar">
    <csmSon:Entity type="Customer">
      <csmSon:Attribute>Satisfaction</csmSon:Attribute>
    </csmSon:Entity>
  </csmSon:Graph>
</csmSon:ProbeInstance>
```

## Analysis Workflows

### Performance Monitoring

Track key performance indicators:

```xml
<!-- System health indicators -->
<csmSon:ProbeInstance name="SystemHealthProbe">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar</csmSon:Name>
    <csmSon:AttributeName>Stock</csmSon:AttributeName>          <!-- Resource level -->
    <csmSon:AttributeName>NbThirstyCustomers</csmSon:AttributeName>  <!-- Demand -->
    <csmSon:AttributeName>AverageSatisfaction</csmSon:AttributeName> <!-- Quality -->
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

### Efficiency Metrics

Calculate and monitor derived metrics:

```xml
<!-- Service efficiency calculation -->
<csmSon:ProbeInstance name="EfficiencyProbe">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar</csmSon:Name>
    <csmSon:AttributeName>NbWaiters</csmSon:AttributeName>      <!-- Capacity -->
    <csmSon:AttributeName>NbThirstyCustomers</csmSon:AttributeName> <!-- Load -->
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

## Real-Time Monitoring Setup

### Debug Console Output

Quick text-based monitoring:

```xml
<csmSon:ConsumerInstance name="ConsoleOutput" type="ShellGenericTextViewer">
  <csmSon:Properties>
    <csmSon:Property name="ShowOnlyLastValue">1</csmSon:Property>
  </csmSon:Properties>
  <csmSon:ProbeReferences>
    <csmSon:ProbeReference>StockProbe</csmSon:ProbeReference>
  </csmSon:ProbeReferences>
</csmSon:ConsumerInstance>
```

### Live Dashboard

Multiple charts in a coordinated view:

```xml
<csmSon:ConsumerInstance name="Dashboard" type="QtGenericChartViewerGL">
  <csmSon:Properties>
    <csmSon:Property name="Layout">MultiGraph</csmSon:Property>
    <csmSon:Property name="UpdateRate">100</csmSon:Property>
    <csmSon:Property name="WindowTitle">Brewery Monitoring</csmSon:Property>
  </csmSon:Properties>
  <csmSon:ProbeReferences>
    <csmSon:ProbeReference>StockProbe</csmSon:ProbeReference>
    <csmSon:ProbeReference>SatisfactionProbe</csmSon:ProbeReference>
    <csmSon:ProbeReference>BarMetricsProbe</csmSon:ProbeReference>
  </csmSon:ProbeReferences>
</csmSon:ConsumerInstance>
```

## Data Export and Post-Processing

### Comprehensive CSV Export

Export all simulation data for external analysis:

```xml
<csmSon:ConsumerInstance name="ComprehensiveExport" type="CSVFileGenericConsumer">
  <csmSon:Properties>
    <csmSon:Property name="FileType">all</csmSon:Property>
    <csmSon:Property name="RowType">iteration</csmSon:Property>
    <csmSon:Property name="ColumnType">attribute</csmSon:Property>
    <csmSon:Property name="DesignateEntityByName">1</csmSon:Property>
    <csmSon:Property name="SerializeAtEachIteration">0</csmSon:Property>
    <csmSon:Property name="OutputDirectory">results/</csmSon:Property>
    <csmSon:Property name="FileName">brewery_simulation</csmSon:Property>
  </csmSon:Properties>
  <csmSon:ProbeReferences>
    <csmSon:ProbeReference>StockProbe</csmSon:ProbeReference>
    <csmSon:ProbeReference>SatisfactionProbe</csmSon:ProbeReference>
    <csmSon:ProbeReference>BarMetricsProbe</csmSon:ProbeReference>
  </csmSon:ProbeReferences>
</csmSon:ConsumerInstance>
```

### Selective Export

Export specific data subsets:

```xml
<!-- Export only final results -->
<csmSon:ConsumerInstance name="FinalResults" type="CSVFileGenericConsumer">
  <csmSon:Properties>
    <csmSon:Property name="FileType">entity</csmSon:Property>
    <csmSon:Property name="ExportLastIterationOnly">1</csmSon:Property>
  </csmSon:Properties>
</csmSon:ConsumerInstance>

<!-- Export aggregated data only -->
<csmSon:ConsumerInstance name="AggregatedData" type="CSVFileGenericConsumer">
  <csmSon:ProbeReferences>
    <csmSon:ProbeReference>BarMetricsProbe</csmSon:ProbeReference>
  </csmSon:ProbeReferences>
</csmSon:ConsumerInstance>
```

## Common Probing Patterns

### Pattern 1: System State Tracking

Monitor overall system health:

```xml
<!-- Resource levels -->
<csmSon:AttributeName>Stock</csmSon:AttributeName>
<csmSon:AttributeName>NbWaiters</csmSon:AttributeName>

<!-- Demand indicators -->
<csmSon:AttributeName>NbThirstyCustomers</csmSon:AttributeName>

<!-- Quality metrics -->
<csmSon:AttributeName>AverageSatisfaction</csmSon:AttributeName>
```

### Pattern 2: Individual Entity Tracking

Monitor specific entities in detail:

```xml
<csmSon:ProbeInstance name="Customer1DetailedProbe">
  <csmSon:Entity>
    <csmSon:Name>Model::MyBar::Customer1</csmSon:Name>
    <csmSon:AttributeName>Satisfaction</csmSon:AttributeName>
    <csmSon:AttributeName>Thirsty</csmSon:AttributeName>
    <csmSon:AttributeName>SurroundingSatisfaction</csmSon:AttributeName>
  </csmSon:Entity>
</csmSon:ProbeInstance>
```

### Pattern 3: Network Analysis

Monitor relationship-dependent data:

```xml
<csmSon:ProbeInstance name="SocialNetworkProbe" type="GraphProbe">
  <csmSon:Graph owner="MyBar">
    <csmSon:Entity type="Customer">
      <csmSon:Attribute>Satisfaction</csmSon:Attribute>
    </csmSon:Entity>
    <csmSon:Links>
      <csmSon:LinkAttribute>SatisfactionFlow</csmSon:LinkAttribute>
    </csmSon:Links>
  </csmSon:Graph>
</csmSon:ProbeInstance>
```

## Troubleshooting Common Issues

### Probe Not Collecting Data

Check these common issues:

```xml
<!-- Ensure active="1" -->
<csmSon:ProbeInstance active="1">

<!-- Verify correct entity path -->
<csmSon:Name>Model::MyBar</csmSon:Name>  <!-- Not just "MyBar" -->

<!-- Check attribute name spelling -->
<csmSon:AttributeName>Satisfaction</csmSon:AttributeName>  <!-- Case sensitive -->

<!-- Ensure probe timing is correct -->
<csmSon:ProbeInstance tag="1::1::1::0.5">  <!-- After entity updates -->
```

### Consumer Not Processing

Verify consumer configuration:

```xml
<!-- Check probe reference -->
<csmSon:ProbeReferences>
  <csmSon:ProbeReference>StockProbe</csmSon:ProbeReference>  <!-- Must match probe name -->
</csmSon:ProbeReferences>

<!-- Verify file permissions for CSV export -->
<csmSon:Property name="OutputDirectory">results/</csmSon:Property>
```

### Performance Issues

Optimize probe frequency:

```xml
<!-- Reduce frequency for heavy probes -->
<csmSon:ProbeInstance tag="1::10::1::0.5">  <!-- Every 10th iteration -->

<!-- Use selective attributes -->
<csmSon:AttributeName>AverageSatisfaction</csmSon:AttributeName>  <!-- Not all individual satisfactions -->
```

## Best Practices

### 1. Strategic Probe Placement

```xml
<!-- Collect aggregate data frequently -->
<csmSon:ProbeInstance name="SystemMetrics" tag="1::1::1::0.9">
  
<!-- Collect detailed data less frequently -->
<csmSon:ProbeInstance name="DetailedAnalysis" tag="1::5::1::0.9">
  
<!-- Collect expensive data rarely -->
<csmSon:ProbeInstance name="NetworkAnalysis" tag="1::25::1::0.9">
```

### 2. Meaningful Names

```xml
<!-- Good: descriptive names -->
<csmSon:ProbeInstance name="CustomerSatisfactionTrends">
<csmSon:ConsumerInstance name="SatisfactionCSVExport">

<!-- Avoid: generic names -->
<csmSon:ProbeInstance name="Probe1">
<csmSon:ConsumerInstance name="Consumer1">
```

### 3. Organized Output

```xml
<!-- Organized directory structure -->
<csmSon:Property name="OutputDirectory">results/satisfaction/</csmSon:Property>
<csmSon:Property name="OutputDirectory">results/stock/</csmSon:Property>
<csmSon:Property name="OutputDirectory">results/network/</csmSon:Property>
```

## Exercise: Complete Brewery Monitoring

Set up comprehensive monitoring for the brewery simulation:

### 1. System Overview Dashboard

Create probes and consumers for:
- Bar stock levels and waiter count
- Total number of thirsty customers
- Average customer satisfaction
- Real-time chart display

### 2. Customer Analysis

Monitor individual customer patterns:
- Satisfaction evolution over time
- Thirst patterns
- Social influence effects
- Export to separate CSV files

### 3. Performance Metrics

Track efficiency indicators:
- Service rate (customers served per time step)
- Stock utilization rate
- Customer satisfaction variance
- Peak demand periods

### 4. Network Effects

Analyze social influence:
- Satisfaction propagation through the network
- Influence strength between connected customers
- Network stability metrics

## Integration with External Tools

### Python Analysis

Export CSV data for Python analysis:

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load simulation results
df = pd.read_csv('results/brewery_simulation.csv')

# Analyze satisfaction trends
satisfaction_data = df[df['attribute'] == 'Satisfaction']
satisfaction_pivot = satisfaction_data.pivot(index='iteration', 
                                           columns='entity', 
                                           values='value')

# Plot results
satisfaction_pivot.plot(title='Customer Satisfaction Over Time')
plt.show()
```

### R Analysis

Statistical analysis in R:

```r
library(ggplot2)
library(dplyr)

# Load data
data <- read.csv("results/brewery_simulation.csv")

# Analyze satisfaction distribution
satisfaction <- data %>% 
  filter(attribute == "Satisfaction") %>%
  ggplot(aes(x = iteration, y = value, color = entity)) +
  geom_line() +
  labs(title = "Customer Satisfaction Trends")

print(satisfaction)
```

## Next Steps

You've now completed the core simulator tutorials! You understand:

- **Model Structure**: How to define entities and relationships
- **Behavior Implementation**: Rules, processes, and entity interactions  
- **Instance Creation**: Setting up specific simulation scenarios
- **Data Collection**: Probing and monitoring simulation results

### Advanced Topics to Explore

1. **Optimization**: Parameter tuning and scenario comparison
2. **Validation**: Model verification and sensitivity analysis
3. **Scalability**: Large-scale simulations and performance optimization
4. **Integration**: Connecting simulations to external data sources
5. **Visualization**: Advanced charting and interactive displays

The foundation you've built with these tutorials prepares you for complex real-world modeling challenges using the CoSMo platform!