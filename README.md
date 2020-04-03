# IFC.JSON
This repository contains the specification for IFC.JSON - version +++

## What
JSON is used throughout the world for exchanging and using data. Building data needs to be available in JSON. Therefore, IFC needs to be available in JSON format. 

IFC.JSON aims primarily at addressing the following problems with IFC:
1. Many developers have never seen/used EXPRESS or STP instance files before, which increases the effort required to extract data required from them. 
2. IFC instance populations are typically exchanged as files, which is at odds with linked, distributed, and rapidly changing data seen on most design and construction projects and products.

Multiple strategies can be followed, leading to different kinds of JSON. This repository aims to specify an *advanced* JSON specification for IFC, following these criteria:
- Human-readability
- Integration with code
- Clear referencing structure
- Direct usability

This means that the following criteria are NOT followed:
- Backwards compatibility
- Round-trip
- Parallel to EXPRESS schema

A less disruptive IFC.JSON version is investigated in https://github.com/IFCJSON-Team/IFC.JSON.

## Getting started
The repository is organised in three sections:
- Documentation: your starting point to find out what this IFC.JSON is about
- Samples: IFC.JSON data examples
- Schemas: IFC.JSON schemas

## More information
Contributions are welcome in all possible ways. Your first starting point is creating GitHub issues. Feel free to get in touch with the people in the IFC.JSON-team.