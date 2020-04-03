# IFC.JSON Specification
This is the overall specification for IFC.JSON2.0.

## Table of Contents
1. Overview
	1.1 Objectives
	1.2 Development strategy
2. ifcJSON Document Specification
	2.1 No required objects, keys, or entities
	2.2 No empty values, no 'userdefined' values, no 'notdefined' values
	2.3 IFC prefix
	2.4 Header
	2.5 Elements
	2.6 Identifiers
	2.7 IfcOwnerHistory
	2.8 Tree Structure
	2.9 camelCase, CamelCaps, or snake_case
	2.10 PredefinedTypes and ObjectTypes and Classes
	2.11 Geometry
	2.12 Attributes, Properties and Property Sets
3. ifcJSON Schema
4. More information


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
"Class": "Project",
"GlobalId": "7e8368b59c66436aa047bebfe824ad81",
"Name": "0YvctVUKr0kugbFTf53O9L"
~~~

This is not valid:

~~~
"Class": "Project",
"GlobalId": "7e8368b59c66436aa047bebfe824ad81",
"Name": "0YvctVUKr0kugbFTf53O9L",
"Description": "",
"ObjectType": null,
"LongName": null,
"Phase": null,
"Type": "NOTDEFINED"
~~~

### 2.3 IFC prefix
The IFC prefix is dropped from the names.

This is valid:

~~~
Wall    
~~~

This is not valid:

~~~
IfcWall
~~~

### 2.4 Header
JSON is typically exchanged headerless. So this is not included in the specification. IFCJSON follows the overall guidelines documented below and can be recognized and interpreted accordingly, regardless of header.

### 2.5 Elements
IFCJSON can contain building elements. They are key elements of an IFCJSON file or snippet. IFCJSON Elements will nominally contain the following tags:

Class
Required
DataType: String
Description: The Class name refers to one of the standard IFC Classes.

GlobalID
Required
DataType: String
Description: A Version 4 UUID is a universally unique identifier that is generated using random numbers. 
Example: 028c968f-687d-484e-9c0a-5048a923b8c4

Name
Optional
DataType: String
Description: The name of the element

Description 
Optional
DataType: String
Description: The description of the element

Tag
Optional
DataType: String
Description: Tag that can be used for annotating the element

ObjectPlacement
Optional
DataType: A JSON Object 
Description: The location of the object. 

Representations 
Optional
DataType: A Collection of JSON Objects
Description: 2D and/or 3D representations of the object.

Example:

~~~
{
    "Class": "Wall",
    "GlobalId": "028c968f-687d-484e-9c0a-5048a923b8c4",            
    "Name": "my Wall",
    "Description": "Description of Wall",
    "ObjectPlacement": {  },
    "Representation": {  },
    "Tag": "wall"
}
~~~

### 2.6 Identifiers
An object is identified only by its GlobalID, which is a UUID according to https://tools.ietf.org/html/rfc4122. 

~~~
"GlobalId": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

Local IDs and line numbers should not be used. Objects can be referenced in JSON by using the ref key-value pair:	

~~~
"ref": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

The GlobalId property is recommended to be added to all elements in the IFC.JSON data.

### 2.7 IfcOwnerHistory
Does not exist. Is prohibited.

### 2.8 Tree Structure
Generally, the IFC.JSON structure is flexible. Objects may be nested inline, or referenced using "ref" tag and GlobalId value. It is recommended to use a tree structure (forward downward relationships) as much as possible. The names of the attribute keys should be the same as the attribute names in the original IFC specification. 

In the case of [IfcObjectDefinition](https://standards.buildingsmart.org/IFC/RELEASE/IFC4/ADD2_TC1/HTML/schema/ifckernel/lexical/ifcobjectdefinition.htm), the following attribute names should be used:
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
	"Class" : "Project",
	"GlobalId": "7e8368b59c66436aa047bebfe824ad81",
	"Name": "0YvctVUKr0kugbFTf53O9L",
	"Description": "",
	"ObjectType": null,
	"LongName": null,
	"Phase": null,
	"IsDecomposedBy" : [
        {
            "Class": "Site",
            "GlobalId": "561d1aeeb13f4eca846883793a293b45",
            "Name": "Default Site",
            "Description": "Description of Default Site",
            "ObjectType": null,            
            "Representation": null,
            "LongName": null,
            "CompositionType": ".ELEMENT.",
            "RefLatitude": null,
            "RefLongitude": null,
            "RefElevation": null,
            "LandTitleNumber": null,
            "SiteAddress": null,
            "IsDecomposedBy": [
                {   
                    "Class": "Building",
                    "IsDecomposedBy" : [
                     { 
                            "Class" : "Storey",
                            "ContainsElements" : []
                     }
                    ]
                }
            ]
        }
    ]   
}
~~~

### 2.9 camelCase, CamelCaps, or snake_case
Considering that there is no standard notation in JSON, we use the notation standards followed in IFC documentation, which is CamelCaps.

This is valid:
~~~
"GlobalId": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

This is not valid:
~~~
"globalId": "028c968f-687d-484e-9c0a-5048a923b8c4"
"global_id": "028c968f-687d-484e-9c0a-5048a923b8c4"
~~~

### 2.10 PredefinedTypes and ObjectTypes and Classes
There are multiple ways to define the object type of an object in the IFC EXPRESS schema. In IFC.JSON, there is only one way, and it is with the `Class` attribute. `predefinedType` and `objectType` are not to be used. 

This is valid:
~~~
{
	"Class": "Door",
    "GlobalId": "f3b96025-a1f3-42a8-b047-b6cc5b1880ff",
    "Name": "A common door",
    "Description": "Description of a standard door"
}
~~~

This is not valid:
~~~
{
	"Class": "Door",
    "GlobalId": "f3b96025-a1f3-42a8-b047-b6cc5b1880ff",
    "Name": "A common door",
    "Description": "Description of a standard door"
	"ObjectType" : "Door",
	"PredefinedType" : "LargeDoor"
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

Example:
~~~
{
	"Class": "Door",
    "GlobalId": "f3b96025-a1f3-42a8-b047-b6cc5b1880ff",
    "Name": "'A common door'",
    "Description": "'Description of a standard door'",
    "Representations": [
        {
			"Class": "ShapeRepresentation",
            "GlobalId": "dc12a77c-c560-45e3-af0f-e84f5afbe844",
			"RepresentationIdentifier": "Body",
			"RepresentationType": "Brep",
			"Data": [
				{
					"Class": "FacetedBrep",
					"Outer": {
						"Class": "ClosedShell",
						"CfsFaces": [
							{
								"Class": "Face",
								"Bounds": [
									{
										"Class": "FaceOuterBound",
										"Bound": {
											"Class": "PolyLoop",
											"Polygon": [
												{
													"Class": "CartesianPoint",
													"Coordinates": [
														500,
														100,
														2100
													]
												}, ...
											]
										)
									}, ...
								]
							}, ...
						]
					)
				}
			]
        },
        {
            "Class": "ShapeRepresentation",
            "ref": "29820bc5-4ddb-4ce4-a65a-e9c936997256"
        }
    ]
}
~~~

### 2.12 Attributes, Properties and Property Sets
In IFC.JSON, there is no distinction between attributes and properties. Properties may be associated with Elements using direct association. There is no structural distinction between a parameter and any other name / value pair associated with the element.

Example:

~~~
{
	"Class": "Wall",  
	"GlobalId": "561d1aeeb13f4eca846883793a293b45",
	"Name": "MyWall",
	"AccousticRating" : 5,
	"FireRating" : 10
}
~~~

## 3. ifcJSON Schema
Unlike the XSD schema specification and OWL ontology for IFC, which are automatic conversions from the EXPRESS (ISO 10303 part 1) representation of the IFC schema, there is no available methodology for automatic conversion between IFC EXPRESS and ifcJSON schema. In addition, there is no standardization on configuration of mapping between EXPRESS and JSON in general. 

Instead, the included ifcJSON schema definition tries to take into account the above guidelines primarily (modernisation), while maintaining the content of broad value and acceptance in the IFC4 EXPRESS definition.

ifcJSON schema defines a structure for what ifcJSON data is required to provide and therefore its schema defines the validation and interaction control of ifcJSON data.

Example ifcJSON data:
~~~
{
	"Class": "Wall",
	"GlobalId": "028c968f-687d-484e-9c0a-5048a923b8c4",
	"Name": "my wall",
	"Description": "Description of the wall",
	"ObjectPlacement": { },
	"Representation": { },
	"Tag": "267108"
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
		"Class": {
			"type": "string"
		},
		"GlobalId": {
			"type": "string",
			"maxLength": 36
		},  
		"Name": {
			"type": "string",
			"maxLength": 255
		},
		"Description": {
			"type": "string"
		},
		"ObjectPlacement": {
			"type": "object"
		},
		"Representations": { 
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
		"Tag": {
			"type": "string",
			"maxLength": 255
		}
	}
}
~~~

## 4. More information
Contributions are welcome in all possible ways. Your first starting point is creating GitHub issues. Feel free to get in touch with the people in the IFC.JSON-team.
