# API Reference for Knights and Sorcerers

## Contents
- [Overview](#overview)
  - [Quickstart Guide](#quickstartGuide)
  - [Categories of Resource Data](#resourceDataCategories)
  - [Region Codes](#regionCodes)
- [Authentication](#authentication)
- [Resources](#resources)
  - [Servers](#serverData)
  - [Characters](#characterData)

## 1. Overview <a name="overview"></a>

The Knights and Sorcerers (KAS) RESTful API offers detailed information about game servers and player characters. It is offered freely to support the third-party add-ons and applications that our playerbase has come to love. Its endpoints respond in JSON format and use standard HTTP response codes.

All requests must use the HTTP secure protocol: ```https```

By using the KAS API, you agree to our [terms of service](#overview).  

### 1.1. Quickstart Guide <a name="quickstartGuide"></a>

If you're just getting started, see our [developer quickstart guide](#overview). It will help you get authorized and explain how to make a simple request.

### 1.2. Categories of Resource Data <a name="resourceDataCategories"></a>

KAS API endpoints respond with data that is classified as either static, semi-static, or continuous. A single response may contain any combination of data classifications.

#### 1.2.1. Static

Static data changes infrequently, or not at all.

Examples of static data:
- character name
- server name

#### 1.2.2. Semi-Static

Semi-static data could change frequently, but it does not change continuously.

Examples of semi-static data:
- server status
- character achievements

#### 1.2.3. Continuous

Continuous data changes frequently. It may change between one request and the next.

Examples of continuous data:
- server population
- a character's kill count

### 1.3. Region Codes <a name="regionCodes"></a>

Region codes identify the geographic region that a server is in. Include region codes with your requests to filter results.

Our servers are deployed in the following three regions:
| Region                   | Region Code |
| :----------------------- | :---------- |
| United States of America | USA         |
| Europe                   | EU          |
| China                    | CN          | 

## 2. Authentication <a name="authentication"></a>

All requests to the KAS API must include an API key. If you don't have one, see our [developer quickstart guide](#overview).

The KAS API supports the following two methods of authentication:

1. Authenticating in the request header.
  
  ```
  curl -H 'Authentication: bearer <your-api-key>' '<baseurl>/<endpoint-path>/'
  ```

1. Authenticating in the query string.

  ```
  curl '<baseurl>/<endpoint-path>?api-key=<your-api-key>'
  ```  

Although the API supports both methods, **we recommend** attaching your API key to your request header. Doing so is more secure.

## 3. Resources <a name="resources"></a>

KAS endpoints build on the following base url:
```
https://BASEURL
```

### 3.1. Servers <a name="serverData"></a>

Server objects contain data about the identity and status of KAS game servers. The following endpoints provide general and targeted approaches to monitoring this data.

Endpoints:
```
GET  /listservers
GET  /listservers/:server-id
GET  /listservers?region-code=<regionCode>
GET  /listservers?server-name=<serverName>
```

Path parameters:
| Parameter   | Description | Data Type   |
| :---------- | :---------- | :---------- |
| ```server-id```    | Identifies a single server. A server id is four digits. | Integer |

Query string parameters:
| Parameter   | Description | Data Type   |
| :---------- | :---------- | :---------- |
| ```server-name``` | Identifies a server by its name and filters out all servers that do not have the given name. Two servers can only have the same name if they are in different regions. Maximum length for a server name is 32 characters. | String |
| ```region-code``` | Identifies a geographic location and filters out all servers that are not in the given regions. Region codes may be provided in a comma-separated list. Maximum length for a region code is five characters. | String |

Example request:
```
curl -H 'Authentication: bearer <your-api-key>' 'https://BASEURL/listservers'
```

Example response:
```JSON
{
  "servers": [
    {
      "server-id": 1001,
      "server-name": "Merlin",
      "region-code": "USA",
      "server-status": "online",
      "population": 97573
    },
    {
      "server-id": 2001,
      "server-name": "Merlin",
      "region-code": "EU",
      "server-status": "resetting",
      "population": 27699
    },
    {
      "server-id": 3007,
      "server-name": "Arthur",
      "region-code": "CN",
      "server-status": "offline",
      "population": 76509
    }
  ]
}
```

**Note:** The previous example only shows a subset of the server objects that would be in the actual response, which would be too long to display here.

The following table describes each variable contained in a server object.

| Response Item    | Item Description | Data Category | Data Type     |
| :--------------- | :--------------- | :------------ | :------------ |
| ```server-id```         | Identifies a specific server resource. The first digit indicates the server's region, and the following three digits indicate when that server was created. For example, if a server's first digit is "1", that server is in the USA. "Merlin" was our first server, so its last three digits are "001". So, an ```id``` of "1001" and "2001" identify the USA and EU instances of our first server, respectively. | Static | Integer |
| ```server-name```       | Identifies a server by its name. Two servers can only have the same name if they are in different regions. Maximum length for a server name is 32 characters. | Static | String |
| ```region-code```| Identifies a server's geographic location. Maximum length for a region code is five characters. | Static | String |
| ```server-status```     | Indicates whether a server is "online", "offline", or "resetting". A resetting server is offline but is expected to come back online shortly. | Semi-static | String |
| ```population``` | Indicates how many active players have characters on this server. An active player is one who has played for at least 10 hours in the past two months. Maximum population per server is 100,000. | Continuous | Integer |

### 3.2. Characters <a name="characterData"></a>

Character objects contain data about the state of player characters. Each character object is associated exclusively with one server. The following endpoints provide general and targeted approaches to monitoring player characters.

Endpoints:
```
GET /listcharacters/:character-id
GET /listcharacters?server-id=<serverId>
GET /listcharacters?character-name=<characterName>
```

Path parameters:
| Parameter   | Description | Data Type   |
| :---------- | :---------- | :---------- |
| ```character-id``` | Identifies a specific character object. Each id is unique. No two character objects (on the same server, or on different servers) will have the same id. A character id is five digits. | Integer |

Query string parameters:
| Parameter   | Description | Data Type   |
| :---------- | :---------- | :---------- |
| ```server-id``` | Identifies a set of game servers and filters out character objects that aren't on those servers. You may provide one server id or a comma-separated list of server ids. A server id is four digits.  | Integer |
| ```character-name``` | Identifies all character objects that possess the given name and filters out any character objects that don't possess the name. Maximum character name length is 32 characters. | String |

Example request:
```
curl -H 'Authentication: bearer <your-api-key>' 'https://BASEURL/listcharacters?character-name=Lushan'
```

Example response:
```JSON
{
    "characters": [
        {
            "character-id": 34719,
            "character-name": "Lushan",
            "server-id": 1001,
            "class": "Sorcerer",
            "level": 60,
            "character-status": "alive",
            "kills": 73
        },
        {
            "character-id": 66735,
            "character-name": "Lushan",
            "server-id": 3007,
            "class": "Knight",
            "level": 32,
            "character-status": "dead",
            "kills": 13
        }
    ]
}
```

The following table describes each variable contained in a character object.

| Response Item    | Item Description | Data Category | Data Type     |
| :--------------- | :--------------- | :------------ | :------------ |
| ```character-id``` | Identifies a specific character object. Each id is unique. No two character objects (on different servers or on the same server) will have the same id. A character id is five digits. | static | Integer |
| ```server-id``` | Identifies a set of game servers and filters out character objects that aren't on those servers. You may provide one server id or a comma-separated list of server ids. A server id is four digits. | static | Integer |
| ```character-name``` | Identifies all character objects that possess the given name and filters out any character objects that don't possess that name. Maximum character name length is 32 characters. | static | String |
| ```class``` | Indicates a character's class. Possible values are "sorcerer" and "knight". A character can only have one class. | static | String |
| ```level``` | Indicates a character's class level. Minimum value is 1, and the maximum value is 60. | semi-static | Integer |
| ```character-status``` | Indicates if the character is alive or dead. Possible values are "alive" and "dead". | continuous | String |
| ```kills``` | Indicates how many other player characters the character has killed in player vs. player combat. | continuous | Integer |
