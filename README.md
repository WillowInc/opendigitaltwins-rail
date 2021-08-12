# WillowTwin DTDL Ontology - Rail
WillowTwin open digital twin definition language (DTDL) ontology for rail

## Prerequisites

Before getting started:
* Install a code editor such as [Visual Studio Code](https://code.visualstudio.com/) to view, manage, and update the files.
* Install git to allow for cloning and working with this repo and its submodules. For Windows, we recommend [Git for Windows](https://gitforwindows.org/).
* Learn [basic git techniques](https://docs.github.com/en/github/using-git) such as creating a branching, committing, pulling, and pushing.
* Learn about the [Digital Twin Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) which defines the language used to describe models of digital twins.
* Learn about [Azure Digital Twins](https://docs.microsoft.com/en-us/azure/digital-twins/) which is the service WillowTwin is built upon and this ontology gets loaded into as [Models](https://docs.microsoft.com/en-us/azure/digital-twins/concepts-models).

## Getting Started

* Clone this repo to your local machine by running `git clone`.

* Confirm all of the DTDL files have been downloaded by navigating to the Ontology directory in a command prompt and running `DTDLValidator.exe`. The output should include **Validated all files - Your DTDL is valid**. After making changes to the DTDL files, it is recommended to run this command prior to committing changes.

* Start browsing the Ontology which has the following top-level directories:

    | Directory | Description |
    | --------- | ----------- |
    | Willow | This is the set of WillowTwin DTDL models which are used for creating digital twins |

    NOTE: When creating twins in the WillowTwin, they are always based on a Willow DTDL model id which begins with `dtmi:com:willowinc:rail:`.

## Key Concepts

### Ontology Overview

A model which **extends** another model recusively inherits all of the Properties, Relationships, and Components from the referenced model. The following table describes the set of top-level models for this ontology. These form the base from which all other models extend.

| Model | Description |
| --------- | ----------- |
| Agent | Any basic types of stakeholder that can have roles or perform activities, e.g., people, companies, departments. |
| Asset | An object which has value and is owned and maintained, for example a track or a vehicle. Assets are typically products that have a manufacturer, model, and serial number. |
| Capability | A capability indicates the capacity of a entity, be it a Space, an Asset, or a System, to produce or ingest data. This is equivalent to the term trend in a historian database. Specific subclasses specialize this behavior: Sensor entities harvest data from the real world, Actuator entities accept commands from a digital twin platform, Parameter entities configure some capability or system, and State entities define the status. |
| Collection | An administrative grouping of entities that are addressed and treated as a unit for some purpose. These entities may have some spatial arrangement, however that is not a requirement (see, e.g., a portfolio consisting of spatially disjoint networks). |
| Document | A formal piece of written, printed or electronic matter that provides information or evidence or that serves as an official record, for example Contract, Specification, Warranty, Drawing, Image, etc. |
|Event | A spatiotemporally indexed entity with participants, something which occurs somewhere, and that has or takes some time, for example a Contract or a system-produced event. |
| Space | A contiguous part of the physical world that has a 3D spatial extent and that contains or can contain sub-spaces. For example a Region can contain many pieces of Land, which in turn can contain many Buildings, which in turn can contain Levels and Rooms. |

NOTE: Many of the models include **Components**. These are not intended to be instantiated as digital twins. They can be thought of as a reusable interface by many models or a folder to organize a related set of Properties and Relationships.

Models define the possible set of Properties, Relationships, and Components that a twin may have; however, a twin does not have to include all of these at runtime. A twin is only required to have an **id** and a reference **model**.

### Relationships

Relationships between twins are what give the WillowTwin its powerful capabilities to action data. The more relationships that exist in for a given set of twins, also known as a greater graph density, the more opportunity there is for descriptive, presctiptive, predicitive, and real-time analtyics.

We encourage twins to be created and updated with as many relationships as possible; however, it is important that users (and machines) creating twins have a common understanding of the real-world meaning of relationships just as they should have a common understanding of the real-world meaning of models. Having this alignment allows for the highest accuracy of the analytics and thus trust in the outcomes that are actioned and surfaced by the data.

Here are the commmon relationships found in the ontology:

| Relationship | Description |
| ------------ | ----------- |
| isPartOf | A simplified set of topological relations to connect sub- and super-entities within the top-level interface tree. "isPartOf" operates on entities of the same type, for example Spaces have only Spaces as parts, Assets have only Assets as parts, etc. |
| isCapabilityOf | Indicates that a Space, Asset or LogicalDevice has the ability to produce or ingest data represented by sensors, actuators, parameters, or states. |
| includedIn | Indicates that an entity is included in some Collection, for example a Region is included in a Network, or an Asset is included in a Network. |
| locatedIn | Indicates that a given Asset is physically located in a Space. |
| hostedBy | Indicates that a capability or logical device is hosted by another entity such as an asset. |
| servedBy | The coverage or area impacted by a given Asset or Sensor/Actuator. Note that Assets can also service one another. |
| startsAt | Indicates that a linear edge asset starts at a node asset or a linear edge network element starts at a node network element to define the typical direction of travel along with endsAt, for example Track Section starts at a Turnout or Route Section starts at a Junction.  |
| endsAt | Indicates that a linear edge asset ends at a node asset or a linear edge network element ends at a node network element to define the typical direction of travel along with startsAt, for example Track Section ends at a Turnout or Route Section ends at a Junction.  |
| hasDocument | Indicates that an entity has accompanying details or references in a Document such as a Drawing, Product Data, Warranty, or Lease Contract. |

Here are examples of how to use relationships:

| Relationship | Examples |
| ------------ | ------- |
| isPartOf | TrackRail-->isPartOf-->TrackSection<br>Building-->isPartof-->Land |
| isCapabilityOf | Actuator-->isCapabilityOf-->Asset<br>Sensor-->isCapabilityOf-->Asset |
| includedIn | Asset-->includedIn-->System<br>Asset-->includedIn-->Network<br>Network-->includedIn-->Portfolio |
| locatedIn | Asset-->locatedIn-->Room<br>Asset-->locatedIn-->Land |
| hostedBy | OnActuator-->hostedBy-->Controller<br>ObjectFlowSensor-->hostedby-->Controller |
| servedBy | Zone-->servedBy-->Asset<br>Asset-->servedBy-->Asset<br>Level-->servedBy-->Asset |
| startsAt | TrackSection-->startsAt-->Turnout<br>RouteSection-->startsAt-->Junction |
| endsAt | TrackSection-->endsAt-->Turnout<br>RouteSection-->endsAt-->Junction |
| hasDocument | Asset-->hasDocument-->Warranty<br>System-->hasDocument-->TestReport<br>Level-->hasDocument-->AsBuiltDrawing |

## Frequency Asked Questions

**How do I determine which model to use when deciding between a model which extends another?**

It is recommended to be as specific as possible when creating twins to provide the most detailed classification. As such, the "child" model which extends the "parent" should always be used. For example, when creating a twin of a Turnout that is known to have a left-hand orientation, the model `dtmi:com:willowinc:rail:LeftHandTurnout;1` should be used instead of a more generic model from which it extends such as `dtmi:willowinc:rail:SimpleTurnout;1`, `dtmi:willowinc:rail:TrackTurnout;1`, or `dtmi:willowinc:rail:TrackNode;1`.

**How do I determine whether to use a given relationship or its inverse?**

In general, relationships should be defined on the "children" twins with an outgoing relationship to a common "parent". For example, there are typically many components on a track section so the relationship would be defined as `TrackSleeper-->isPartOf-->TrackSection`. Similarly, there are many regions in a rail network so the relationship would be defined as `Region-->isPartOf-->Network`.

