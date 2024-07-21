# Scheda DnD 5e Backend

TODO: Finire descrizione pattern
TODO: Aggiustare class diagram
TODO: Spiegare come testare soprattutto per le WS
TODO: Sezione apposita per Websocket

######
[![Postgres](https://img.shields.io/badge/Made%20with-postgres-%23316192.svg?style=plastic&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![NPM](https://img.shields.io/badge/Made%20with-NPM-%23CB3837.svg?style=plastic&logo=npm&logoColor=white)](https://www.npmjs.com/)
[![NodeJS](https://img.shields.io/badge/Made%20with-node.js-6DA55F?style=plastic&logo=node.js&logoColor=white)](https://nodejs.org/en)
[![Express.js](https://img.shields.io/badge/Made%20with-express.js-%23404d59.svg?style=plastic&logo=express&logoColor=%2361DAFB)](https://expressjs.com/it/)
[![JWT](https://img.shields.io/badge/Made%20with-JWT-black?style=plastic&logo=JSON%20web%20tokens)](https://jwt.io/)
[![TypeScript](https://img.shields.io/badge/Made%20with-typescript-%23007ACC.svg?style=plastic&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Sequelize](https://img.shields.io/badge/Made%20with-Sequelize-52B0E7?style=plastic&logo=Sequelize&logoColor=white)](https://sequelize.org/)
[![Docker](https://img.shields.io/badge/Made%20with-docker-%230db7ed.svg?style=plastic&logo=docker&logoColor=white)](https://www.docker.com/)
[![Postman](https://img.shields.io/badge/Made%20with-Postman-FF6C37?style=plastic&logo=postman&logoColor=white)](https://www.postman.com/)
[![Redis](https://img.shields.io/badge/Made%20with-Redis-FF6C37?style=plastic&logo=redis&logoColor=white)](https://redis.io/)
[![RxJS](https://img.shields.io/badge/Made%20with-RxJS-FF6C37?style=plastic&logo=rxjs&logoColor=white)](https://rxjs.dev/)
[![Axios](https://img.shields.io/badge/Made%20with-Axios-5A29E3?style=plastic&logo=axios&logoColor=white)](https://axios-http.com/)

<a name="index"></a>
## 📘 Table of Contents

* [🎯 Project Goal](#Projectgoal)

* [📄 Use case diagram](#Usecasediagram)
  * [Actors](#Actors)
  * [Session management](#Sessionmanagement)
  * [Turn management](#Turnmanagement)
  * [Attack management](#Attackmanagement)
  * [Entity management](#Entitymanagement)
  * [History management](#Historymanagement)
* [🚩 App Routes](#AppRoutes)
  * [Session](#SessionRoutes)
  * [Turn](#TurnRoutes)
  * [Attack](#AttackRoutes)
  * [Entity](#EntityRoutes)
  * [History](#HistoryRoutes)
* [⏱ Sequence diagram](#Sequencediagram)
  * [Create Session](#CreatSession)
  * [Start Session](#StartSession)
  * [Enable Reaction](#EnableReaction)
  * [Connect To Session](#ConnectToSession)
* [📐 Class diagram](#Classdiagram)

* [🪄 Patterns used](#Patternsused)
  * [Middlewares: Chain of Responsability](#ChainofResponsability)
  * [Exceptions handling: Factory Method](#Factory)
  * [Data sources handling: Repository + Factory + Singleton](#RepFacSingleton)
  * [Websocket communication: Observer](#Observer)
  * [HOF](#hof) (nel type checking e nei generatori di middleware)

* [⚙️ Technologies used](#Technologiesused)
* [👨🏻‍💻 Authors](#Authors)

<a name="Projectgoal"></a>
## 🎯 Project Goal

### Complete Management of DnD Combat Sessions.

The backend is designed to offer complete management of combat sessions in "_Dungeons & Dragons 5e_", integrating directly with character created by players and npc sheets created by the master. These are created through the dedicated Flutter application, "_SchedaDnD5e_", which serves as the frontend for interaction with the system.

### Main Backend Features

1. **Combat Session Management**.
   - **Creation and Update**: Allows the creation of new combat sessions and the update of existing sessions. Each session includes attributes such as name, session status, map size, and participants (characters, NPCs, monsters).
   - **Status Monitoring**: Manages and updates session status (created, in progress, paused, ended), and maintains event history via historical messages.

2. **Entity Management**
   - **Characters and Monsters**: Manages character and monster sheets and their variations during combat. Such variations can include, for example, decreased HP, spell castings with consumable slots, and much more allowed by the game rules. Each entity can also have a reaction status with respect to other entities that can be enabled or disabled during play.
   - **Turn Management**: Monitors and updates the states and positions of entities during game turns, keeping track of their location on the map and turn index.

3. **Real-Time Interaction**
   - **WebSocket for Real-Time Updates**: Uses WebSocket to provide real-time updates during combat sessions. This includes handling dice rolls and other actions that require instant interactions between players and the master.
   - **Synchronization**: Ensures that all changes made by player actions or status changes are synchronized in real time with the frontend, enhancing the interactive gaming experience.

4. **Authentication and Security**
   - **JWT for Authentication**: Uses JSON Web Tokens (JWT) to manage user authentication. This ensures that only authorized users can access and modify combat sessions and entities.

5. **Performance and Caching**
   - **Data Caching**: Implements a caching system through Redis to improve the performance of frequent requests and reduce the load on the database. Temporarily stores information for quick access without having to query Sequelize or Firestore each time.
   - **Error Management and Feedback**: Provides clear and detailed responses for common errors, such as entities not found or attempts at impermissible actions.

6. **Persistence and Synchronization**.
   - **Data Persistence**: Ensures that all changes to entities and sessions are saved in the database. Uses Sequelize for data management, ensuring persistent and consistent updates.
   - **Synchronization with Frontend**: Maintains data synchronization between the backend and frontend, ensuring that changes to sessions and entities are reflected in real time in the user interface.

<a name="Usecasediagram"></a>
## 📄 Use case diagram

<a name="Actors"></a>
### Actors
The player roles can be mapped as follows. Note that although the client must be authenticated via JWT to participate in the combat session, there is still a route that does not require authentication, namely the `diceRoll/` route.

<img src="png/Actors.png" width="450rem">

<a name="Actors"></a>
### Session management
<img src="png/Session Management.png" width="550rem">

<a name="Turnmanagement"></a>
### Turn management
<img src="png/Turn Management.png" width="550rem">

<a name="Attackmanagement"></a>
### Attack management
<img src="png/Attack Management.png" width="550rem">

<a name="Entitymanagement"></a>
### Entity management
<img src="png/Entity State Management.png" width="550rem">

<a name="Historymanagement"></a>
### History management
<img src="png/History Management.png" width="550rem">

<a name="AppRoutes"></a>
## 🚩 App Routes

The API server endpoints are listed in the following table. Blank lines separate the routes following the semantic division of the previous use cases.

<a name="SessionRoutes"></a>
### Session Routes

| Type | Route | Parameters | Description |
| --- | --- | --- | --- |
| `GET` |`/sessions` | - | Provides the index of all sessions in which the authenticated user has the role of player or master. |
| `POST` |`/sessions` | characters, npc, monsters, mapSize | Creates a new session. Returns the new session. |
| `GET` |`/sessions/{sessionId}` | - | Returns all information from `sessionId`. |
| `DELETE` |`/sessions/{sessionId}` | - | Deletes `sessionId`.|
| `PATCH` |`/sessions/{sessionId}/start` | - | Starts `sessionId`. Its current status must be `created`. |
| `PATCH` |`/sessions/{sessionId}/pause` | - | Pauses `sessionId`. Its current status must be `ongoing`. |
| `PATCH` |`/sessions/{sessionId}/continue` | - | Resumes `sessionId`. Its current status must be `paused`. |
| `PATCH` |`/sessions/{sessionId}/stop` | - | Ends `sessionId`. Its current status must be `ongoing` or `paused`. |

<a name="TurnRoutes"></a>
### Turn Routes

| Type | Route | Parameters | Description |
| --- | --- | --- | --- |
| `GET` |`/sessions/{sessionId}/turn` | - | Provides the current turn of `sessionId`. |
| `PATCH` |`/sessions/{sessionId}/turn/postpone` | entityId, predecessorEntityId | Postpones the turn of the `entityId` after the turn of the `predecessorEntityId`. |
| `PATCH` |`/sessions/{sessionId}/turn/end` | entityId | Ends the turn of the `entityId`. Notifies the next playing entity. |

<a name="AttackRoutes"></a>
### Attack Routes

| Type | Route | Parameters | Description |
| --- | --- | --- | --- |
| `GET` |`/diceRoll` | diceList, modifier? | Rolls the dice in the `diceList` and adds up any `modifier`. The `diceList` must be non empty.  |
| `PATCH` |`/sessions/{sessionId}/attack` | entityId, attackInfo, attackType | Causes `attackerId` to attack an entity. The `attackType` must contain the type of attack being carried out that can be melee or enchantment. The `attackInfo` must contain the attempt dice roll. If this is greater than the target's AC, the attacker is asked to roll the damage dice. |
| `GET` |`/sessions/{sessionId}/savingThrow` | entitiesId, difficultyClass, skill | Requests all the `entitiesId` to make a save roll on `skill`. The result is positive if greater than `difficultyClass`.|
| `PATCH` |`/sessions/{sessionId}/effect` | entityId, effect | Assigns the `effect` to the `entityId`. If `effect` is null, the effects of the entities are deleted. |
| `PATCH` |`/sessions/{sessionId}/reaction` | entityId | Enables the reaction for the `entityId`. Notifies it. |

<a name="EntityRoutes"></a>
### Entity Routes

| Type | Route | Parameters | Description |
| --- | --- | --- | --- |
| `PATCH` |`/sessions/{sessionId}/entities` | entityType, entityInfo | Adds an entity to the `sessionId`. If the `entityType` is monster, `entityInfo` must contain all of its information. Otherwise it must only contain the uid.  |
| `DELETE` |`/sessions/{sessionId}/entities/{entityId}` | - | Removes `entityId` from `sessionId`. Fails if not found. |
| `GET` |`/sessions/{sessionId}/entities/{entityId}` | - | Returns all the info from `entityId`. Fails if not found in `sessionId`.|
| `PATCH` |`/sessions/{sessionId}/entities/{entityId}` | entityInfo | Updates the info of `entityId`. Fails if not found in `sessionId`. |

<a name="HistoryRoutes"></a>
### History Routes

| Type | Route | Parameters | Description |
| --- | --- | --- | --- |
| `GET` |`/sessions/{sessionId}/history` | actionType? | Returns the whole `sessionId` history. Filter it by `actionType` if provided. |
| `POST` |`/sessions/{sessionId}/history` | message | Adds a message to the `sessionId` history. Notifies all players except the one who posted the message. |


<a name="Sequencediagram"></a>
## ⏱ Sequence diagram

<a name="CreateSession"></a>
### Create Session
<img src="png/CreateSession.png" width="550rem">

<a name="StartSession"></a>
### Start Session
<img src="png/StartSession.png" width="550rem">

<a name="Attack"></a>
### Attack
<img src="png/attack.png" width="550rem">

<a name="EnableReaction"></a>
### Enable Reaction
<img src="png/EnableReaction.png" width="550rem">

<a name="ConnectToSession"></a>
### Connect To Session
<img src="png/ConnectToSession.png" width="550rem">

<a name="Classdiagram"></a>
## 📐 Class diagram
<img src="png/Class Diagram.png" width="550rem">

<a name="Patternsused"></a>
## 🪄 Patterns Used

Various software design patterns were used in the implementation to ensure a robust, maintainable and scalable code structure. The following are the patterns used:

<a name="Middleware"></a>
### Middleware
Used extensively to handle a variety of concerns, including validation, authentication, and authorisation, in a modular way. Middleware functions are used to process requests and responses in a sequence, which makes the code more organised and reusable. For example, in the dice roll endpoint, middleware functions check for mandatory parameters, validate their types, and perform specific checks before executing the main logic.
<a name="Factory*"></a>
### Factory Method
Used to create error objects, encapsulating the instantiation logic and providing a centralised way to generate different types of errors. It helps maintain a clean code structure and ensures consistent handling of errors across the application. Different factories are used to generate client-side and server-side errors, ensuring that errors are created in a standardised way based on their type and context.
<a name="RepFacSingleton*"></a>
### Repository + Factory + Singleton
Data sources are handled using three patterns: Repository, Factory and Singleton. The Repository pattern abstracts the data layer, providing a clean API. The Factory pattern is used to create repositories, ensuring consistent build logic. The Singleton pattern ensures that a single instance of each repository is used, providing a single source of truth and reducing resource overhead. Together, these patterns facilitate efficient caching, data retrieval and persistence across different storage solutions such as Redis, Sequelize and Firestore.
<a name="Observer*"></a>
### Observer
// TODO
<a name="Hof*"></a>
### HOF
// TODO

<a name="Technologiesused"></a>
## ⚙️ Technologies used

- **Database**: _Sequelize_ with support for _PostgreSQL_ for entity and session management.
- **Data Modeling**: _Sequelize ORM_ for defining models and managing relationships between entities.
- **Authentication**: _JWT_ to ensure secure and authorized access.
- **Caching**: _Redis_ for cache management and performance improvement.
- **API**: _RESTful API_ for communication between the frontend and backend, managed with _Express_.
- **WebSocket**: For real-time communication during combat sessions.
- **Package Management**: _NPM_ for package and dependency management.
- **Containerization**: _Docker_ for creating and managing isolated environments.
- **Reactive Programming**: _RxJS_ for handling data streams and asynchronous events.
- **HTTP requests**: _Axios_ for handling HTTP requests and interaction between the two API servers.
- **Testing**: _Postman_ for creating API requests used for testing.

<a name="Authors"></a>
## 👨🏻‍💻 Authors

| Name            | Email           | GitHub                        |
|-----------------|-------------------------------|-------------------------------|
| Valerio Morelli | s1118781@studenti.univpm.it    | [MrPio](https://github.com/MrPio) |
| Enrico Maria Sardellini | s1120355@studenti.univpm.it | [Ems01](https://github.com/Ems01)|
| Federico Staffolani | s1114954@studenti.univpm.it | [fedeStaffo](https://github.com/fedeStaffo) |