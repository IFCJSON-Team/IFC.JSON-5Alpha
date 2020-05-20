# IFC.JSON Specification
This is the overall specification for IFC.JSON2.0.

## Table of Contents
  * [1. Overview](#1-overview)
    + [1.1 Objectives](#11-objectives)
    + [1.2 Development strategy](#12-development-strategy)
  * [2. ifcJSON Document Specification](#2-ifcjson-document-specification)
    + [2.1 No required objects, keys, or entities](#21-no-required-objects--keys--or-entities)
    + [2.2 No empty values, no 'userdefined' values, no 'notdefined' values](#22-no-empty-values--no--userdefined--values--no--notdefined--values)
    + [2.3 IFC prefix](#23-ifc-prefix)
    + [2.4 Header](#24-header)
    + [2.5 Elements](#25-elements)
    + [2.6 Identifiers](#26-identifiers)
    + [2.7 IfcOwnerHistory](#27-ifcownerhistory)
    + [2.8 Tree Structure](#28-tree-structure)
    + [2.9 camelCase, CamelCaps, or snake_case](#29-camelcase--camelcaps--or-snake-case)
    + [2.10 PredefinedTypes and ObjectTypes and Classes](#210-predefinedtypes-and-objecttypes-and-classes)
    + [2.11 Geometry](#211-geometry)
    + [2.12 Attributes, Properties and Property Sets](#212-attributes--properties-and-property-sets)
  * [3. ifcJSON Schema](#3-ifcjson-schema)
  * [4. More information](#4-more-information)

## 1. Overview

### 1.1 Objectives
JSON is used throughout the world for exchanging and using data. Building data needs to be available in JSON. Therefore, IFC needs to be available in JSON format. 

IFCJSON aims primarily at addressing the following problems with IFC:

1.	Many developers have never seen/used EXPRESS or STP instance files before, which increases the effort required to extract data required from them. 
2.	IFC instance populations are typically exchanged as files, which is at odds with linked, distributed, and rapidly changing data seen on most design and construction projects and products.

Multiple strategies can be followed, leading to different kinds of JSON. This document aims to specify the single recommended JSON specification for IFC.

### 1.2 Development strategy
A number of criteria could be followed for making a JSON version for IFC.

1.	Backwards compatibility
2.	Round-trip
3.	Human-readability
4.	Flexibility and extensibility
5.	Integration with code
6.	Parallel to XML format
7.	Parallel to EXPRESS schema
8.	Clear referencing structure (flat or nesting)

We cannot support all criteria in one format. Choices have to be made; or multiple JSON flavours need to be enabled. In this document, we aim to support 1 and 1 only JSON format for IFC. 

The following criteria are followed:
1.	Human-readability
2.	Integration with code
3.	Clear referencing structure
4.	Direct usability

The following criteria are considered to be of less importance, so they are excluded. An alternative JSON format may be targeted, aiming to support these criteria, but that would be at the expense of the above criteria (human-readability, integration with code, clear structure, direct usability), so it is decided otherwise for the scope of this document.

The following criteria are NOT followed:
1.	Backwards compatibility
2.	Round-trip
3.	Parallel to EXPRESS schema

## 2. ifcJSON Document Specification
In the below sections, a more detailed specification is given for ifcJSON. We don't provide a full schema here, but rather list a number of general principles.

### 2.1 No required objects, keys, or entities
There are no required objects, entities or keys. In other words, the following is valid IFC.JSON:

~~~
[  ]
~~~

Requirements and constraints can be defined as "View" extensions of the JSON Schema.

### 2.2 No empty values, no 'userdefined' values, no 'notdefined' values
There are no empty values, no 'userdefined' values, no 'notdefined' values. General practice is *not* to export empty or null pairs. Just transfer the data that you have.

This is valid:

~~~
"type": "IfcProject",
"globalId": "7e8368b59c66436aa047bebfe824ad81",
"name": "The project"
~~~

This is not valid:

~~~
"type": "IfcProject",
"globalId": "7e8368b59c66436aa047bebfe824ad81",
"name": "The project",
"description": "",
"objectType": null,
"longName": null,
"phase": null,
"predefinedType": "NOTDEFINED"
~~~

### 2.3 Header
JSON is typically exchanged headerless. So this is not included in the specification. IFCJSON follows the overall guidelines documented below and can be recognized and interpreted accordingly, regardless of header.

### 2.4 Elements
IFCJSON can contain building elements. They are key elements of an IFCJSON file or snippet. IFCJSON Elements will nominally contain the following tags:

type  
Required  
DataType: String  
Description: The Class name refers to one of the standard IFC Classes.  

globalID  
Required  
DataType: String  
Description: A Version 4 UUID is a universally unique identifier that is generated using random numbers.  
Example: 028c968f-687d-484e-9c0a-5048a923b8c4  

name  
Optional  
DataType: String  
Description: The name of the element  

description  
Optional  
DataType: String  
Description: The description of the element  

tag  
Optional  
DataType: String  
Description: Tag that can be used for annotating the element  

objectPlacement  
Optional  
DataType: A JSON Object  
Description: The location of the object.  

representations  
Optional  
DataType: A Collection of JSON Objects  
Description: 2D and/or 3D representations of the object.  

Example:

~~~
{
  "type": "IfcWall",
  "globalId": "028c968f-687d-484e-9c0a-5048a923b8c4",            
  "name": "my Wall",
  "description": "Description of Wall",
  "objectPlacement": {  },
  "representation": {  },
  "tag": "wall"
}
~~~

### 2.6 Identifiers
An object is identified only by its globalID, which is a UUID according to https://tools.ietf.org/html/rfc4122. 

~~~
"globalId": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

Objects can be referenced in JSON by using the globalId attribute. In the example below, a reference is made to the ownerHistory object, which has the globalId `6d7919fd-2c83-497b-b21c-d4209e5162bf`.	
 
~~~
"type" : "IfcBuilding",
"globalId" : "98fa75b8-371d-412a-be42-2326c68dfcf5",
"ownerHistory" : "6d7919fd-2c83-497b-b21c-d4209e5162bf",
"name" : "Default Building"
~~~

### 2.7 IfcOwnerHistory
Does not exist. Track change history in your native software and use the JSON version of IFC to transmit snippets of data from system to another.

### 2.8 Tree Structure
Generally, the IFC.JSON structure is flexible. Objects may be nested inline, or referenced using the globalId value as a reference. It is recommended to use a tree structure (forward downward relationships) as much as possible. The names of the attribute keys should be the same as the attribute names in the original IFC specification. 

In the case of [IfcObjectDefinition](https://standards.buildingsmart.org/IFC/RELEASE/IFC4/ADD2_TC1/HTML/schema/ifckernel/lexical/ifcobjectdefinition.htm), for example, the following attribute names should be used:
- HasAssignments
- Nests
- IsNestedBy
- HasContext
- IsDecomposedBy
- Decomposes
- HasAssociations

Furthermore, IfcRelationship objects are to be avoided / *NOT* used:
- IfcRelAssigns
- IfcRelNests
- IfcRelNests
- IfcRelDeclares
- IfcRelAggregates
- IfcRelAggregates
- IfcRelAssociates

As a result, this nested tree structure does not include 'empty relationship objects', and relations/attributes instead point directly from relating object to related object(s): `IsDecomposedBy : YourSite`.

Example:

~~~
{
  "type" : "IfcProject",
  "globalId": "7e8368b59c66436aa047bebfe824ad81",
  "name": "0YvctVUKr0kugbFTf53O9L",
  "description": "",
  "longName": null,
  "phase": null,
  "isDecomposedBy" : [
  {
    "type": "IfcSite",
    "globalId": "561d1aeeb13f4eca846883793a293b45",
    "name": "Default Site",
    "description": "Description of Default Site",
    "isDecomposedBy": [
    {   
      "type": "IfcBuilding",
      "isDecomposedBy" : [
      { 
        "type" : "IfcStorey",
        "containsElements" : []
      }]
    }]
  }]  
}
~~~

### 2.9 camelCase, CamelCaps, or snake_case
The most commonly used notation in JSON is camelCase, mainly because of its alignment with a JavaScript and Python audience. Hence, the JSON version of IFC uses camelCase as much as possible.

This is valid:
~~~
"globalId": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

This is not valid:
~~~
"GlobalId": "028c968f-687d-484e-9c0a-5048a923b8c4"
"global_id": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

### 2.10 PredefinedTypes and ObjectTypes and Classes
There are multiple ways to define the object type of an object in the IFC EXPRESS schema. In IFC.JSON, there is only one way, and it is with the `type` attribute. `predefinedType` and `objectType` are not to be used. If you need subtypes or alternative types, extend the schema for the case of your project and transfer the JSON data as you wish.

This is valid:
~~~
{
  "type": "Door",
  "globalId": "f3b96025-a1f3-42a8-b047-b6cc5b1880ff",
  "name": "A common door",
  "description": "Description of a standard door"
}
~~~

This is not valid:
~~~
{
  "type": "Door",
  "globalId": "f3b96025-a1f3-42a8-b047-b6cc5b1880ff",
  "name": "A common door",
  "description": "Description of a standard door"
  "objectType" : "Door",
  "predefinedType" : "LargeDoor"
}
~~~

### 2.11 Geometry
Geometry may be treated in multiple ways. Compared to the EXPRESS schema, geometry is included in a simplified manner. Representation styles are excluded, and IFC.JSON limits to the actual geometry instead. Hence, geometry can be included in a more direct manner (`Representations`, `Data`). 

- Geometry formats: in principle, any geometry description can be used and handled, including OBJ, GTLF, point cloud, STEP, and a potentially scalable set of future geometry types and formats.
- Referencing geometry:
	- Nested inline, embedded in the JSON tree - recommended when including STEP geometry (CSG geometry).
	- Nested inline, with a simple string - recommended for any BREP-focused geometry (triangulated mesh geometry).
	- Referenced elsewhere in the JSON data using a GlobalID
	- Referenced externally by URI

Example 1:
~~~
{
  "type": "IfcDoor",
  "globalId": "f3b96025-a1f3-42a8-b047-b6cc5b1880ff",
  "name": "A common door",
  "description": "Description of a standard door",
  "representation": {
    "type": "IfcProductDefinitionShape",
    "representations": [
      {
        "type": "IfcShapeRepresentation",
        "globalId": "dc12a77c-c560-45e3-af0f-e84f5afbe844",
        "representationIdentifier": "Body",
        "representationType": "Brep",
        "items": [
          {
            "type": "IfcFacetedBrep",
            "outer": {
              "type": "IfcClosedShell",
              "cfsFaces": [
                {
                  "type": "IfcFace",
                  "bounds": [
                    {
                      "type": "IfcFaceOuterBound",
                      "bound": {
                        "type": "IfcPolyLoop",
                        "polygon": [
                          {
                            "type": "IfcCartesianPoint",
                            "coordinates": [
                              500,
                              100,
                              2100
                            ]
                          }, ...
                        ]
                      }
                    }
                  ]
                }
              ]
            }
          }
        ]
      }
    ]
  }  
}
~~~
Example 2:
~~~
[
  {
    "type": "IfcDoor",
    "name": "A common door",
    "description": "Description of a standard door",
    "globalId": "fc88bae7-5dc3-4235-b0a9-813256fb4134",
    "representations": [
      {
        "type": "ShapeRepresentation",
        "ref": "3d687576-c2da-4317-bc32-42cd2155b7b3"
      }
    ]
  },
  {
    "type": "ShapeRepresentation",
    "globalId": "3d687576-c2da-4317-bc32-42cd2155b7b3",
    "representationIdentifier": "Body",
    "representationType": "OBJ",
    "items": [
      "v 19.68503937007874 7.874015777364492 1.8773116482173788e-06\\nv 19.68503937007874 3.9370078446827543 82.67716347701906\\nv 19.68503937007874 7.874015777364492 82.67716347701906\\nv 19.68503937007874 7.874015777364492 1.8773116482173788e-06\\nv 19.68503937007874 3.9370078446827543 1.8773116482173788e-06\\nv 19.68503937007874 3.9370078446827543 82.67716347701906\\nv 49.21259842519685 7.874015777364492 82.67716347701906\\nv 49.21259842519685 7.874015777364492 1.8773116482173788e-06\\nv 19.68503937007874 7.874015777364492 82.67716347701906\\nv 19.68503937007874 7.874015777364492 82.67716347701906\\nv 49.21259842519685 7.874015777364492 1.8773116482173788e-06\\nv 19.68503937007874 7.874015777364492 1.8773116482173788e-06\\nv 49.21259842519685 7.874015777364492 82.67716347701906\\nv 49.21259842519685 3.9370078446827543 82.67716347701906\\nv 49.21259842519685 3.9370078446827543 1.8773116482173788e-06\\nv 49.21259842519685 7.874015777364492 82.67716347701906\\nv 49.21259842519685 3.9370078446827543 1.8773116482173788e-06\\nv 49.21259842519685 7.874015777364492 1.8773116482173788e-06\\nv 19.68503937007874 3.9370078446827543 82.67716347701906\\nv 19.68503937007874 3.9370078446827543 1.8773116482173788e-06\\nv 49.21259842519685 3.9370078446827543 82.67716347701906\\nv 49.21259842519685 3.9370078446827543 82.67716347701906\\nv 19.68503937007874 3.9370078446827543 1.8773116482173788e-06\\nv 49.21259842519685 3.9370078446827543 1.8773116482173788e-06\\nv 49.21259842519685 3.9370078446827543 1.8773116482173788e-06\\nv 19.68503937007874 7.874015777364492 1.8773116482173788e-06\\nv 49.21259842519685 7.874015777364492 1.8773116482173788e-06\\nv 49.21259842519685 3.9370078446827543 1.8773116482173788e-06\\nv 19.68503937007874 3.9370078446827543 1.8773116482173788e-06\\nv 19.68503937007874 7.874015777364492 1.8773116482173788e-06\\nv 49.21259842519685 7.874015777364492 82.67716347701906\\nv 19.68503937007874 3.9370078446827543 82.67716347701906\\nv 49.21259842519685 3.9370078446827543 82.67716347701906\\nv 49.21259842519685 7.874015777364492 82.67716347701906\\nv 19.68503937007874 7.874015777364492 82.67716347701906\\nv 19.68503937007874 3.9370078446827543 82.67716347701906\\nf 1 2 3\\nf 4 5 6\\nf 7 8 9\\nf 10 11 12\\nf 13 14 15\\nf 16 17 18\\nf 19 20 21\\nf 22 23 24\\nf 25 26 27\\nf 28 29 30\\nf 31 32 33\\nf 34 35 36"
    ]
  }
]
~~~

### 2.12 Attributes, Properties and Property Sets
In IFC.JSON, there is no distinction between attributes and properties. Properties may be associated with Elements using direct association. There is no structural distinction between a parameter and any other name / value pair associated with the element.

Example:

~~~
{
  "type": "Wall",  
  "globalId": "561d1aeeb13f4eca846883793a293b45",
  "name": "MyWall",
  "accousticRating" : 5,
  "fireRating" : 10
}
~~~

## 3. ifcJSON Schema
Unlike the XSD schema specification and OWL ontology for IFC, which are automatic conversions from the EXPRESS (ISO 10303 part 1) representation of the IFC schema, there is no available methodology for automatic conversion between IFC EXPRESS and this JSON5 schema for IFC. In addition, there is no standardization on configuration of mapping between EXPRESS and JSON in general. 

Instead, the included ifcJSON schema definition tries to take into account the above guidelines primarily (modernisation), while maintaining the content of broad value and acceptance in the IFC4 EXPRESS definition.

ifcJSON schema defines a structure for what ifcJSON data is required to provide and therefore its schema defines the validation and interaction control of ifcJSON data.

Example ifcJSON data:
~~~
{
  "type": "Wall",
  "globalId": "028c968f-687d-484e-9c0a-5048a923b8c4",
  "name": "my wall",
  "description": "Description of the wall",
  "objectPlacement": { },
  "representation": { },
  "tag": "267108"
}
~~~

Corresponding ifcJSON Schema snippet (validated with above snippet using https://www.jsonschemavalidator.net/):
~~~
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "http://example.com/product.schema.json",
  "title": "Wall",
  "description": "IFC.JSON for Wall",
  "type": "object",
  "properties": {
    "type": {
      "type": "string"
    },
    "globalId": {
      "type": "string",
      "maxLength": 36
    },  
    "name": {
      "type": "string",
      "maxLength": 255
    },
    "description": {
      "type": "string"
    },
    "objectPlacement": {
      "type": "object"
    },
    "representations": { 
      "type": "object",
      "oneOf": [
        {
          "type": "string"
        },
        { 
          "type": "object",
          "allOf": [{ "$ref": "#/properties/ShapeRepresentation" }]
        }
      ]
    },
    "tag": {
      "type": "string",
      "maxLength": 255
    }
  }
}
~~~

## 4. More information
Contributions are welcome in all possible ways. Your first starting point is creating GitHub issues. Feel free to get in touch with the people in the IFC.JSON-team.
