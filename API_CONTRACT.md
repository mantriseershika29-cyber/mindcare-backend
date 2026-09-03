\# Dementia Cure App - API Contract



\## Base URL



http://localhost:5000



\---



\## Authentication



Protected APIs require a Firebase ID token.



Request header:



Authorization: Bearer <Firebase\_ID\_Token>



If no token is provided:



Status: 401 Unauthorized



Response:



{

&#x20; "error": "No token provided"

}



If the token is invalid or expired:



Status: 401 Unauthorized



Response:



{

&#x20; "error": "Invalid or expired token"

}



\---



\# 1. Health Check



\## GET /



Authentication: Not required



\### Response



{

&#x20; "message": "Dementia backend is running"

}



\---



\# 2. Create User



\## POST /users



Authentication: Not required



\### Request Body



{

&#x20; "name": "Test User",

&#x20; "age": 65

}



\### Success Response



Status: 201 Created



{

&#x20; "message": "User created successfully",

&#x20; "userId": "USER\_ID"

}



\---



\# 3. Get Difficulty



\## GET /difficulty/:gameType/:level



Authentication: Not required



\### Example



GET /difficulty/pattern\_recall/1



\### Supported Game Types



\- pattern\_recall

\- sequencing

\- face\_name\_matching

\- object\_placement



\### Example Response



{

&#x20; "gameType": "pattern\_recall",

&#x20; "level": 1,

&#x20; "label": "Beginner",

&#x20; "gridSize": 2,

&#x20; "itemCount": 4,

&#x20; "timeLimitSeconds": 90,

&#x20; "distractorCount": 0

}



\---



\# 4. Create Session



\## POST /sessions



Authentication: Required



\### Request Headers



Authorization: Bearer <Firebase\_ID\_Token>



Content-Type: application/json



\### Request Body



{

&#x20; "userId": "test-user",

&#x20; "gameType": "pattern\_recall",

&#x20; "level": 1

}



\### Optional Fields



{

&#x20; "startedAt": "2026-09-03T10:00:00Z",

&#x20; "completedAt": "2026-09-03T10:02:00Z",

&#x20; "durationSeconds": 120

}



\### Success Response



Status: 201 Created



{

&#x20; "message": "Session created successfully",

&#x20; "sessionId": "SESSION\_ID"

}



\---



\# 5. Get Session



\## GET /sessions/:id



Authentication: Required



\### Example



GET /sessions/SESSION\_ID



\### Success Response



{

&#x20; "sessionId": "SESSION\_ID",

&#x20; "userId": "test-user",

&#x20; "gameType": "pattern\_recall",

&#x20; "level": 1,

&#x20; "startedAt": "...",

&#x20; "completedAt": null,

&#x20; "durationSeconds": 0

}



\### If Session Does Not Exist



Status: 404 Not Found



\---



\# 6. Record Performance



\## POST /performance



Authentication: Required



\### Request Headers



Authorization: Bearer <Firebase\_ID\_Token>



Content-Type: application/json



\### Request Body



{

&#x20; "userId": "test-user",

&#x20; "sessionId": "SESSION\_ID",

&#x20; "gameType": "pattern\_recall",

&#x20; "level": 1,

&#x20; "score": 80,

&#x20; "accuracy": 80,

&#x20; "errors": 2,

&#x20; "responseTimeSeconds": 35

}



\### Required Fields



\- userId

\- sessionId

\- gameType

\- level

\- score

\- accuracy



\### Success Response



Status: 201 Created



{

&#x20; "message": "Performance recorded successfully",

&#x20; "performanceId": "PERFORMANCE\_ID"

}



\---



\# 7. Get User Performance



\## GET /performance/:userId



Authentication: Required



\### Example



GET /performance/test-user



\### Success Response



{

&#x20; "userId": "test-user",

&#x20; "count": 1,

&#x20; "performances": \[

&#x20;   {

&#x20;     "performanceId": "PERFORMANCE\_ID",

&#x20;     "userId": "test-user",

&#x20;     "sessionId": "SESSION\_ID",

&#x20;     "gameType": "pattern\_recall",

&#x20;     "level": 1,

&#x20;     "score": 80,

&#x20;     "accuracy": 80,

&#x20;     "errors": 2,

&#x20;     "responseTimeSeconds": 35

&#x20;   }

&#x20; ]

}



\---



\# 8. Get User Progress



\## GET /progress/:userId



Authentication: Required



\### Example



GET /progress/test-user



\### Success Response



{

&#x20; "userId": "test-user",

&#x20; "totalGames": 1,

&#x20; "averageAccuracy": 80,

&#x20; "gameTypeBreakdown": {

&#x20;   "pattern\_recall": 1

&#x20; },

&#x20; "currentLevel": {

&#x20;   "pattern\_recall": 1

&#x20; }

}



\---



\# Authentication Summary



| Endpoint | Method | Authentication |

|---|---|---|

| / | GET | Not required |

| /users | POST | Not required |

| /difficulty/:gameType/:level | GET | Not required |

| /sessions | POST | Required |

| /sessions/:id | GET | Required |

| /performance | POST | Required |

| /performance/:userId | GET | Required |

| /progress/:userId | GET | Required |



\---



\# Supported Game Types



\- pattern\_recall

\- sequencing

\- face\_name\_matching

\- object\_placement



\---



\# Difficulty Levels



| Level | Label | Grid Size | Items | Time Limit | Distractors |

|---|---|---:|---:|---:|---:|

| 1 | Beginner | 2 | 4 | 90 sec | 0 |

| 2 | Easy | 3 | 6 | 75 sec | 1 |

| 3 | Moderate | 4 | 8 | 60 sec | 2 |

| 4 | Challenging | 5 | 10 | 45 sec | 3 |



\---



\# Testing Status



The following APIs have been successfully tested with Firebase Authentication:



\- POST /sessions - PASS

\- GET /sessions/:id - PASS

\- POST /performance - PASS

\- GET /performance/:userId - PASS

\- GET /progress/:userId - PASS



Unauthenticated request test:



POST /sessions



Result:



401 Unauthorized



{

&#x20; "error": "No token provided"

}



\---



\# Frontend Integration Flow



1\. User logs in using Firebase Authentication.

2\. Firebase provides an ID token.

3\. Frontend sends the ID token with protected API requests.

4\. Backend verifies the token using verifyToken.

5\. Backend reads or writes data in Firestore.

6\. Backend returns a JSON response.



Flow:



User Login

&#x20;   ↓

Firebase Authentication

&#x20;   ↓

Firebase ID Token

&#x20;   ↓

Frontend API Request

&#x20;   ↓

Authorization: Bearer <Firebase\_ID\_Token>

&#x20;   ↓

Backend verifyToken

&#x20;   ↓

Firestore

&#x20;   ↓

JSON Response

