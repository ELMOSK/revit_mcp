# Revit Model Context Protocol (MCP)

## Executive Summary

The Revit Model Context Protocol (MCP) is an open standard that enables seamless communication between AI assistants and Revit Building Information Models (BIM). By implementing this protocol, organizations can leverage the power of AI to enhance BIM workflows, improve model coordination, automate tedious tasks, and enable natural language interactions with Revit models.

This document defines the structure, components, and implementation guidelines for the Revit MCP, providing a comprehensive framework for developers and BIM managers to integrate AI capabilities with Revit environments.

## Table of Contents

1. [Introduction](#1-introduction)
2. [Core Architecture](#2-core-architecture)
3. [Data Models and Schemas](#3-data-models-and-schemas)
4. [API Endpoints and Operations](#4-api-endpoints-and-operations)
5. [Security and Permissions](#5-security-and-permissions)
6. [Integration with Revit API](#6-integration-with-revit-api)
7. [Implementation Components](#7-implementation-components)
8. [Extension Points](#8-extension-points)
9. [Deployment Models](#9-deployment-models)
10. [Implementation Guidelines](#10-implementation-guidelines)
11. [Use Cases and Examples](#11-use-cases-and-examples)
12. [References](#12-references)

## 1. Introduction

### 1.1 Purpose

The Revit Model Context Protocol (MCP) provides a standardized method for exposing Revit model data and operations to AI assistants. It enables AI models to understand and manipulate Revit models through a well-defined interface, decoupling the AI from the specifics of the Revit API.

### 1.2 Background

The Model Context Protocol was created by Anthropic as an open standard for connecting AI assistants to the systems where data lives. The Revit MCP extends this standard to the domain of Building Information Modeling, allowing AI assistants to interact with Revit models in a consistent and secure way.

### 1.3 Benefits

Implementing the Revit MCP offers several key benefits:

- **Standardized Integration**: A common method for integrating AI with Revit
- **Improved Model Coordination**: AI-assisted detection of discrepancies across disciplines
- **Interoperability**: Easier integration with other BIM tools and platforms
- **Automation and Efficiency**: Offloading repetitive tasks to AI assistants
- **Secure Data Handling**: Control over what data is exposed and what operations are allowed
- **Scalability and Reusability**: Modular architecture that can be extended and reused

### 1.4 Scope

This document covers:
- The architecture and components of the Revit MCP
- Data models and schemas for representing Revit elements
- API endpoints and operations for interacting with Revit
- Security considerations and permission models
- Integration with the Revit API
- Implementation guidelines and best practices
- Deployment models and extension points

## 2. Core Architecture

### 2.1 Client-Server Architecture

The Revit MCP follows the standard MCP client-server architecture:

- **MCP Host**: AI applications like Claude or specialized Revit assistants that initiate connections
- **MCP Client**: Protocol clients that maintain 1:1 connections with Revit MCP servers
- **MCP Server**: A lightweight server that exposes Revit model data and operations through the standardized protocol
- **Revit Application**: The Revit software instance containing the BIM model data

![MCP Architecture Diagram](architecture_diagram.png)

### 2.2 Communication Flow

1. The AI assistant (MCP Host) sends requests to the MCP Client
2. The MCP Client forwards these requests to the MCP Server
3. The MCP Server interacts with Revit through the Revit API
4. Results are returned through the same path back to the AI assistant

### 2.3 Key Components

#### 2.3.1 MCP Host
The MCP Host is an AI application that uses the MCP to interact with Revit. This could be:
- A general-purpose AI assistant like Claude
- A specialized Revit AI assistant
- An integrated development environment (IDE) with AI capabilities
- A custom application built on top of an AI model

#### 2.3.2 MCP Client
The MCP Client is responsible for:
- Establishing and maintaining connections with MCP Servers
- Formatting requests from the AI assistant
- Sending requests to the appropriate server
- Receiving and parsing responses
- Handling authentication and security

#### 2.3.3 MCP Server
The MCP Server is responsible for:
- Exposing Revit data and operations through standardized endpoints
- Translating MCP requests into Revit API calls
- Managing Revit transactions
- Handling security and permissions
- Converting Revit objects to standardized schemas

#### 2.3.4 Revit Integration
The Revit integration component:
- Connects the MCP Server to the Revit API
- Manages Revit application events
- Handles Revit-specific exceptions and errors
- Optimizes performance for Revit operations

## 3. Data Models and Schemas

### 3.1 Revit Element Representation

Revit elements are represented in a standardized JSON format that includes:

```json
{
  "elementId": "123456",
  "category": "Walls",
  "family": "Basic Wall",
  "type": "Generic - 8\"",
  "parameters": {
    "Width": 0.2,
    "Height": 3.0,
    "Length": 5.0,
    "Volume": 3.0,
    "Comments": "Exterior wall"
  },
  "geometry": {
    "boundingBox": {
      "min": {"x": 0.0, "y": 0.0, "z": 0.0},
      "max": {"x": 5.0, "y": 0.2, "z": 3.0}
    }
  },
  "location": {
    "level": "Level 1",
    "startPoint": {"x": 0.0, "y": 0.0, "z": 0.0},
    "endPoint": {"x": 5.0, "y": 0.0, "z": 0.0}
  },
  "relationships": {
    "hostingElements": [],
    "hostedElements": ["123457", "123458"]
  }
}
```

### 3.2 Project Information Schema

Project-level information includes:

```json
{
  "projectInfo": {
    "name": "Office Building",
    "number": "PRJ-001",
    "client": "ABC Corporation",
    "address": "123 Main St",
    "buildingType": "Commercial"
  },
  "levels": [
    {"id": "100001", "name": "Level 1", "elevation": 0.0},
    {"id": "100002", "name": "Level 2", "elevation": 4.0}
  ],
  "views": [
    {"id": "200001", "name": "Floor Plan: Level 1", "type": "FloorPlan"},
    {"id": "200002", "name": "North Elevation", "type": "Elevation"}
  ],
  "categories": [
    {"id": "OST_Walls", "name": "Walls"},
    {"id": "OST_Doors", "name": "Doors"}
  ],
  "familyTypes": [
    {"id": "300001", "category": "Walls", "family": "Basic Wall", "type": "Generic - 8\""},
    {"id": "300002", "category": "Doors", "family": "Single-Flush", "type": "36\" x 84\""}
  ]
}
```

### 3.3 Operation Request Schema

Requests to perform operations follow this schema:

```json
{
  "operation": "CreateWall",
  "parameters": {
    "startPoint": {"x": 0.0, "y": 0.0, "z": 0.0},
    "endPoint": {"x": 5.0, "y": 0.0, "z": 0.0},
    "levelId": "100001",
    "wallTypeId": "300001",
    "height": 3.0
  }
}
```

### 3.4 Operation Response Schema

Responses from operations follow this schema:

```json
{
  "success": true,
  "result": {
    "elementId": "123456",
    "category": "Walls",
    "family": "Basic Wall",
    "type": "Generic - 8\""
  },
  "message": "Wall created successfully"
}
```

## 4. API Endpoints and Operations

### 4.1 Query Operations

#### 4.1.1 GetElement
Retrieves information about a specific element by ID.

**Request:**
```json
{
  "operation": "GetElement",
  "parameters": {
    "elementId": "123456"
  }
}
```

**Response:**
```json
{
  "success": true,
  "result": {
    "elementId": "123456",
    "category": "Walls",
    "family": "Basic Wall",
    "type": "Generic - 8\"",
    "parameters": {
      "Width": 0.2,
      "Height": 3.0,
      "Length": 5.0
    }
  }
}
```

#### 4.1.2 QueryElements
Searches for elements based on criteria.

**Request:**
```json
{
  "operation": "QueryElements",
  "parameters": {
    "category": "Walls",
    "filter": {
      "parameter": "Comments",
      "value": "Exterior wall",
      "operator": "contains"
    },
    "limit": 10
  }
}
```

#### 4.1.3 GetSelectedElements
Gets information about currently selected elements.

#### 4.1.4 GetElementsInView
Gets elements visible in the current view.

#### 4.1.5 GetProjectInfo
Retrieves project-level information.

#### 4.1.6 GetAvailableFamilyTypes
Lists available family types by category.

### 4.2 Modification Operations

#### 4.2.1 CreateElement
Creates new elements (walls, doors, windows, etc.).

**Request:**
```json
{
  "operation": "CreateWall",
  "parameters": {
    "startPoint": {"x": 0.0, "y": 0.0, "z": 0.0},
    "endPoint": {"x": 5.0, "y": 0.0, "z": 0.0},
    "levelId": "100001",
    "wallTypeId": "300001",
    "height": 3.0
  }
}
```

#### 4.2.2 ModifyElement
Changes element parameters or properties.

#### 4.2.3 DeleteElement
Removes elements from the model.

#### 4.2.4 MoveElement
Relocates elements.

#### 4.2.5 CopyElement
Duplicates elements.

#### 4.2.6 GroupElements
Creates element groups.

### 4.3 Analysis Operations

#### 4.3.1 ColorElements
Applies color coding to elements based on parameters.

**Request:**
```json
{
  "operation": "ColorElements",
  "parameters": {
    "category": "Walls",
    "parameterName": "Volume",
    "colorScheme": "BlueToRed",
    "minValue": 0,
    "maxValue": 10
  }
}
```

#### 4.3.2 MeasureDistance
Calculates distances between elements.

#### 4.3.3 CheckInterference
Detects clashes between elements.

#### 4.3.4 AnalyzeParameters
Analyzes parameter values across elements.

### 4.4 Documentation Operations

#### 4.4.1 CreateTag
Generates tags for elements.

**Request:**
```json
{
  "operation": "CreateTag",
  "parameters": {
    "elementId": "123456",
    "tagTypeId": "400001",
    "position": {"x": 2.5, "y": 0.1, "z": 1.5},
    "viewId": "200001"
  }
}
```

#### 4.4.2 CreateDimension
Creates dimension elements.

#### 4.4.3 AddText
Adds text notes.

#### 4.4.4 CreateSheet
Generates documentation sheets.

### 4.5 Advanced Operations

#### 4.5.1 ExecuteCode
Runs custom C# code for specialized operations.

**Request:**
```json
{
  "operation": "ExecuteCode",
  "parameters": {
    "code": "// C# code to execute\nvar wall = doc.GetElement(new ElementId(123456)) as Wall;\nreturn wall.Width.ToString();"
  }
}
```

#### 4.5.2 RunTransaction
Executes multiple operations as a single transaction.

#### 4.5.3 ImportData
Imports external data into the model.

#### 4.5.4 ExportData
Exports model data to external formats.

## 5. Security and Permissions

### 5.1 Authentication

#### 5.1.1 LocalAuthentication
Authentication for local MCP servers.

#### 5.1.2 RemoteAuthentication
Authentication for remote MCP servers.

#### 5.1.3 TokenManagement
Handling of authentication tokens.

### 5.2 Authorization

#### 5.2.1 PermissionLevels
Different levels of access (Read, Write, Admin).

#### 5.2.2 OperationRestrictions
Limiting which operations can be performed.

#### 5.2.3 ElementRestrictions
Limiting which elements can be accessed or modified.

### 5.3 Data Protection

#### 5.3.1 SensitiveDataHandling
Protection of sensitive project information.

#### 5.3.2 LocalProcessing
Ensuring data remains within secure environments.

#### 5.3.3 AuditLogging
Tracking of all operations performed through MCP.

## 6. Integration with Revit API

### 6.1 API Mapping

#### 6.1.1 RevitAPIWrapper
Mapping between MCP operations and Revit API calls.

#### 6.1.2 TransactionManagement
Handling of Revit transactions.

#### 6.1.3 EventHandling
Managing Revit application events.

#### 6.1.4 ExceptionHandling
Dealing with Revit API exceptions.

### 6.2 Performance Considerations

#### 6.2.1 BatchProcessing
Optimizing multiple operations.

#### 6.2.2 DataCaching
Caching frequently accessed data.

#### 6.2.3 AsyncOperations
Handling long-running operations asynchronously.

## 7. Implementation Components

### 7.1 Server Components

#### 7.1.1 RevitMCPServer
Main server implementation.

#### 7.1.2 RevitAPIConnector
Connection to Revit API.

#### 7.1.3 EndpointHandlers
Handlers for different API endpoints.

#### 7.1.4 SchemaConverters
Conversion between Revit objects and MCP schemas.

### 7.2 Client Components

#### 7.2.1 RevitMCPClient
Client implementation for AI assistants.

#### 7.2.2 RequestFormatter
Formatting of requests to the server.

#### 7.2.3 ResponseParser
Parsing of server responses.

#### 7.2.4 ConnectionManager
Managing server connections.

## 8. Extension Points

### 8.1 Custom Operations

#### 8.1.1 OperationRegistry
Registration of custom operations.

#### 8.1.2 PluginArchitecture
Support for plugins to extend functionality.

### 8.2 Custom Data Models

#### 8.2.1 SchemaExtensions
Extensions to the standard schemas.

#### 8.2.2 CustomPropertyMapping
Mapping of custom properties.

## 9. Deployment Models

### 9.1 Local Deployment

#### 9.1.1 DesktopIntegration
Integration with desktop Revit installations.

#### 9.1.2 LocalServerConfiguration
Configuration for local servers.

### 9.2 Remote Deployment

#### 9.2.1 CloudDeployment
Deployment in cloud environments.

#### 9.2.2 EnterpriseIntegration
Integration with enterprise systems.

## 10. Implementation Guidelines

### 10.1 Server Implementation

#### 10.1.1 Setting Up the Development Environment

**Prerequisites:**
- Visual Studio 2022 or later
- .NET Framework 4.8 or .NET 6.0+
- Revit API SDK (compatible with your Revit version)
- MCP SDK for .NET (C# or F#)

**Project Setup:**
1. Create a new Class Library project in Visual Studio
2. Add references to the Revit API assemblies:
   - RevitAPI.dll
   - RevitAPIUI.dll
3. Install the MCP SDK NuGet package:
   ```
   Install-Package ModelContextProtocol.SDK
   ```
4. Set up the project structure:
   ```
   RevitMCPServer/
   ├── Core/
   │   ├── RevitAPIConnector.cs
   │   ├── SchemaConverter.cs
   │   └── TransactionManager.cs
   ├── Endpoints/
   │   ├── QueryEndpoints.cs
   │   ├── ModificationEndpoints.cs
   │   ├── AnalysisEndpoints.cs
   │   └── DocumentationEndpoints.cs
   ├── Models/
   │   ├── RevitElementSchema.cs
   │   ├── ProjectInfoSchema.cs
   │   └── OperationSchema.cs
   ├── Security/
   │   ├── Authentication.cs
   │   └── Authorization.cs
   └── RevitMCPServerPlugin.cs
   ```

#### 10.1.2 Creating a Basic Server

```csharp
using Autodesk.Revit.UI;
using ModelContextProtocol.SDK;
using ModelContextProtocol.SDK.Server;

namespace RevitMCPServer
{
    public class RevitMCPServerPlugin : IExternalApplication
    {
        private MCPServer _mcpServer;
        
        public Result OnStartup(UIControlledApplication application)
        {
            // Initialize the MCP server
            _mcpServer = new MCPServer("RevitMCPServer", "1.0.0");
            
            // Register endpoints
            RegisterEndpoints();
            
            // Start the server
            _mcpServer.Start(8080);
            
            return Result.Succeeded;
        }
        
        public Result OnShutdown(UIControlledApplication application)
        {
            // Stop the server
            _mcpServer.Stop();
            
            return Result.Succeeded;
        }
        
        private void RegisterEndpoints()
        {
            // Register query endpoints
            _mcpServer.RegisterEndpoint("GetElement", new GetElementEndpoint());
            _mcpServer.RegisterEndpoint("QueryElements", new QueryElementsEndpoint());
            
            // Register modification endpoints
            _mcpServer.RegisterEndpoint("CreateWall", new CreateWallEndpoint());
            _mcpServer.RegisterEndpoint("ModifyElement", new ModifyElementEndpoint());
            
            // Register analysis endpoints
            _mcpServer.RegisterEndpoint("ColorElements", new ColorElementsEndpoint());
            
            // Register documentation endpoints
            _mcpServer.RegisterEndpoint("CreateTag", new CreateTagEndpoint());
        }
    }
}
```

#### 10.1.3 Implementing API Endpoints

**Example: GetElement Endpoint**

```csharp
using Autodesk.Revit.DB;
using ModelContextProtocol.SDK.Server;

namespace RevitMCPServer.Endpoints
{
    public class GetElementEndpoint : IEndpoint
    {
        public EndpointResponse Execute(EndpointRequest request, RevitAPIConnector connector)
        {
            // Extract parameters from the request
            string elementIdStr = request.Parameters["elementId"].ToString();
            ElementId elementId = new ElementId(int.Parse(elementIdStr));
            
            // Get the element from the Revit document
            Document doc = connector.GetActiveDocument();
            Element element = doc.GetElement(elementId);
            
            if (element == null)
            {
                return new EndpointResponse
                {
                    Success = false,
                    Message = $"Element with ID {elementIdStr} not found"
                };
            }
            
            // Convert the element to the schema format
            var schemaConverter = new SchemaConverter();
            var elementSchema = schemaConverter.ConvertElementToSchema(element);
            
            // Return the response
            return new EndpointResponse
            {
                Success = true,
                Result = elementSchema
            };
        }
    }
}
```

#### 10.1.4 Error Handling and Logging

```csharp
public EndpointResponse Execute(EndpointRequest request, RevitAPIConnector connector)
{
    try
    {
        // Endpoint implementation
        // ...
        
        return new EndpointResponse
        {
            Success = true,
            Result = result
        };
    }
    catch (Autodesk.Revit.Exceptions.InvalidOperationException ex)
    {
        Logger.LogError($"Revit operation error: {ex.Message}");
        return new EndpointResponse
        {
            Success = false,
            Message = $"Revit operation error: {ex.Message}"
        };
    }
    catch (Exception ex)
    {
        Logger.LogError($"Unexpected error: {ex.Message}");
        return new EndpointResponse
        {
            Success = false,
            Message = "An unexpected error occurred"
        };
    }
}
```

### 10.2 Client Implementation

#### 10.2.1 Setting Up the Development Environment

**Prerequisites:**
- Development environment for your target platform (Node.js, Python, .NET, etc.)
- MCP SDK for your programming language
- HTTP client library

#### 10.2.2 Creating a Basic Client

**Python Example:**

```python
from modelcontextprotocol.client import MCPClient

class RevitMCPClient:
    def __init__(self, server_url, api_key=None):
        """Initialize the Revit MCP client.
        
        Args:
            server_url: URL of the Revit MCP server
            api_key: Optional API key for authentication
        """
        self.client = MCPClient(server_url)
        
        if api_key:
            self.client.set_api_key(api_key)
    
    def get_element(self, element_id):
        """Get information about a specific element.
        
        Args:
            element_id: ID of the element to retrieve
            
        Returns:
            Element information if successful, None otherwise
        """
        response = self.client.execute_operation(
            "GetElement",
            {"elementId": element_id}
        )
        
        if response.success:
            return response.result
        else:
            print(f"Error: {response.message}")
            return None
    
    def create_wall(self, start_point, end_point, level_id, wall_type_id, height):
        """Create a new wall.
        
        Args:
            start_point: Start point coordinates (x, y, z)
            end_point: End point coordinates (x, y, z)
            level_id: ID of the level
            wall_type_id: ID of the wall type
            height: Height of the wall
            
        Returns:
            Created wall information if successful, None otherwise
        """
        response = self.client.execute_operation(
            "CreateWall",
            {
                "startPoint": start_point,
                "endPoint": end_point,
                "levelId": level_id,
                "wallTypeId": wall_type_id,
                "height": height
            }
        )
        
        if response.success:
            return response.result
        else:
            print(f"Error: {response.message}")
            return None
```

### 10.3 Performance Optimization

#### 10.3.1 Optimizing Query Operations

- Use Revit's built-in filtering mechanisms (FilteredElementCollector)
- Apply filters at the database level rather than in memory
- Use parameter filters for efficient querying
- Combine multiple filters for complex queries
- Use element IDs when possible instead of iterating through elements

#### 10.3.2 Optimizing Modification Operations

- Group related operations in a single transaction
- Use sub-transactions for complex operations
- Commit transactions as soon as possible
- Avoid nested transactions when possible
- Implement proper rollback mechanisms

#### 10.3.3 Caching Strategies

- Cache frequently accessed elements
- Cache project information and metadata
- Implement cache invalidation on model changes
- Use memory-efficient caching for large models
- Support time-based and event-based cache expiration

### 10.4 Security Best Practices

#### 10.4.1 Securing Local Servers

- Implement strong authentication for local servers
- Use token-based authentication
- Support integration with Windows authentication
- Implement session management
- Secure token storage

#### 10.4.2 Securing Remote Servers

- Use industry-standard authentication protocols (OAuth 2.0, OpenID Connect)
- Implement multi-factor authentication
- Support single sign-on integration
- Use secure password storage
- Implement account lockout policies

#### 10.4.3 Managing Permissions

- Define clear permission levels (Read, Write, Admin)
- Support granular permissions for specific operations
- Implement permission inheritance
- Support temporary permission elevation
- Audit permission changes

## 11. Use Cases and Examples

### 11.1 AI-Assisted Model Coordination

The Revit MCP enables AI assistants to help with model coordination across disciplines. For example:

1. An architect and structural engineer are working on separate Revit models
2. The AI assistant connects to both models through MCP servers
3. The assistant analyzes the models for discrepancies:
   - Structural elements not aligned with architectural walls
   - Missing structural supports for architectural elements
   - Clashes between structural and architectural elements
4. The assistant generates a report of issues and suggests solutions
5. The assistant can even make corrections automatically (with approval)

**Example Interaction:**

User: "Check if all load-bearing walls have proper structural support."

AI Assistant:
```
I'll analyze the architectural and structural models for proper support of load-bearing walls.

Analysis Results:
- 15 load-bearing walls found in the architectural model
- 12 walls have proper structural support
- 3 walls are missing structural support:
  - Wall ID 123456 on Level 2 (North Wing)
  - Wall ID 123789 on Level 3 (East Wing)
  - Wall ID 124567 on Level 3 (South Wing)

Would you like me to:
1. Generate a detailed report with locations and images
2. Create structural elements to support these walls
3. Tag these walls for review
```

### 11.2 Automated Documentation

The Revit MCP enables AI assistants to automate documentation tasks:

1. The AI assistant connects to the Revit model through an MCP server
2. The user requests documentation for a specific area or system
3. The assistant:
   - Creates appropriate views
   - Adds dimensions and tags
   - Generates schedules
   - Creates sheets with proper titleblocks
   - Organizes views on sheets
   - Adds notes and annotations

**Example Interaction:**

User: "Create documentation for the HVAC system on Level 2."

AI Assistant:
```
I'll create documentation for the Level 2 HVAC system.

Steps:
1. Creating floor plan view filtered for HVAC elements on Level 2
2. Adding dimensions and tags to major equipment
3. Generating equipment and duct schedules
4. Creating section views through main duct runs
5. Creating detail views of mechanical rooms
6. Organizing views on sheets with proper titleblocks
7. Adding general notes and specifications

Documentation created:
- Sheet M-201: Level 2 HVAC Plan
- Sheet M-202: Level 2 HVAC Sections
- Sheet M-203: Level 2 HVAC Details
- Sheet M-204: Level 2 HVAC Schedules
```

### 11.3 Parameter Analysis and Visualization

The Revit MCP enables AI assistants to analyze and visualize parameter values:

1. The AI assistant connects to the Revit model through an MCP server
2. The user requests analysis of specific parameters
3. The assistant:
   - Extracts parameter values from relevant elements
   - Performs statistical analysis
   - Creates visualizations (color coding, charts)
   - Identifies outliers or issues
   - Suggests optimizations

**Example Interaction:**

User: "Analyze wall types by thermal performance and visualize the results."

AI Assistant:
```
I'll analyze wall types by thermal performance (R-value) and visualize the results.

Analysis Results:
- 8 wall types found in the model
- R-values range from 10 to 45
- Average R-value: 22.5
- Exterior walls average R-value: 30.2
- Interior walls average R-value: 15.8

Visualization:
- Created color-coded 3D view "Thermal Analysis" showing walls by R-value
- Red: R-value < 15 (poor performance)
- Yellow: R-value 15-30 (moderate performance)
- Green: R-value > 30 (good performance)

Findings:
- 3 exterior walls on the north facade have R-values below recommended levels
- Conference room walls have inconsistent thermal properties
- Mechanical room walls exceed necessary thermal requirements

Would you like me to suggest wall type modifications to optimize thermal performance?
```

### 11.4 Natural Language Model Manipulation

The Revit MCP enables AI assistants to manipulate the model using natural language:

1. The AI assistant connects to the Revit model through an MCP server
2. The user describes changes or additions in natural language
3. The assistant:
   - Interprets the user's intent
   - Identifies relevant elements and parameters
   - Plans the necessary operations
   - Executes the operations through the MCP
   - Reports the results

**Example Interaction:**

User: "Add a row of windows along the south wall of the conference room, spaced 6 feet apart, starting 3 feet from the east corner."

AI Assistant:
```
I'll add windows to the south wall of the conference room as specified.

Steps:
1. Identifying the conference room and its south wall
   - Found conference room "CR-101" on Level 1
   - South wall ID: 234567, length: 24'-0"

2. Planning window placement
   - Starting 3'-0" from east corner
   - Windows spaced 6'-0" apart
   - Using default window type: "Fixed - 36" x 48""
   - This will result in 4 windows along the wall

3. Creating windows
   - Window 1: 3'-0" from east corner - Created (ID: 345678)
   - Window 2: 9'-0" from east corner - Created (ID: 345679)
   - Window 3: 15'-0" from east corner - Created (ID: 345680)
   - Window 4: 21'-0" from east corner - Created (ID: 345681)

Windows have been added successfully. Would you like me to:
1. Adjust the window heights
2. Change the window type
3. Add window tags in the floor plan
```

## 12. References

### 12.1 Anthropic MCP Documentation
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [MCP Specification](https://modelcontextprotocol.io/docs/specification)

### 12.2 Revit API Documentation
- [Revit API Developer's Guide](https://www.autodesk.com/developer-network/platform-technologies/revit)

### 12.3 Related Projects
- [revit-mcp-plugin](https://github.com/example/revit-mcp-plugin)
- [revit-mcp](https://github.com/example/revit-mcp)

### 12.4 Articles and Blog Posts
- [Revit Gen AI: MCP, RAG and Vibe](https://thebuildingcoder.typepad.com/blog/2025/03/revit-gen-ai-mcp-rag-and-vibe.html)
- [Revit MCP: Discover Revit with AI Agent Capabilities](https://archilabs.ai/posts/revit-model-context-protocol)
