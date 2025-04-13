**Zeotap to Salesforce Contact Integration**

**Objective** - Simulating an integration between _Zeotap & Salesforce service cloud_ , focusing specifically on the contact object 

**Goals** -
1. Push contact data from Zeotap into Salesforce
2. Handle creation of new contacts & updation of existing ones

----------------------------------------------------------------------------------------------

**Data Flow Overview** 
1. Zeotap receives customer data.
2. Sends data to the integration layer/API service.
3. The API service:
   
    3.1 Authenticates with Salesforce via OAuth 2.0.
   
    3.2 Queries if contact exists (via email).
   
    3.3 Creates or updates Contact in Salesforce.
   
5. Handles success/failure logging and retry.

   **Zeotap -> API gateway -> Authentication -> Salesforce Contact Object**

----------------------------------------------------------------------------------------------

   **Authentication Method**
   
   **Protocol** - OAuth 2.0
   
   _Username - Password_

   Steps :
      1. Register a connected app in salesforce
      2. Retrieve client_id , client_secret , username , password
      3. Send POST req :- https://login.salesforce.com/services/oauth2/token
  
         _**FORM DATA :**_
         
         {
           "grant_type": "password",
           "client_id": "<CLIENT_ID>",
           "client_secret": "<CLIENT_SECRET>",
           "username": "<USERNAME>",
           "password": "<PASSWORD+TOKEN>"
         }
         
   4. Extract **access_token** from response
   5. Use **Authorization: Bearer <access_token>** in subsequent API calls

----------------------------------------------------------------------------------------------

   **Key Endpoints**

   1. Create Contact -> **POST** -> /services/data/v59.0/sobjects/Contact/
   2. Update Contact -> **PATCH** -> /services/data/v59.0/sobjects/Contact/id
   3. Query (by email) -> **GET** -> /services/data/v59.0/query/?q=SOQL_QUERY

   ----------------------------------------------------------------------------------------------

   **Sample Requests & Responses** 

   _**Create Contact**_
   
      _**Request**_
     
      POST /services/data/v59.0/sobjects/Contact/
      Authorization: Bearer <access_token>
      Content-Type: application/json
      
      {
        "FirstName": "Alice",
        "LastName": "Smith",
        "Email": "alice@example.com",
        "Phone": "1234567890"
      }

      **_Update_**
      PATCH /services/data/v59.0/sobjects/Contact/003XXXXXXXXXXXXXXX
      Authorization: Bearer <access_token>
      Content-Type: application/json
      
      {
        "Phone": "0987654321"
      }

      **_Query_**
      GET /services/data/v59.0/query/?q=SELECT+Id+FROM+Contact+WHERE+Email='alice@example.com'
      Authorization: Bearer <access_token>

----------------------------------------------------------------------------------------------

**Error Handling**

**Errors:**

400 Bad Request (e.g., missing required fields)

401 Unauthorized (expired or invalid token)

404 Not Found

429 Too Many Requests (rate limit hit)

**Required Fields:**

LastName is required to create a Contact in Salesforce.

**Rate Limits:**

Depends on the Salesforce edition, e.g. 15,000 requests/day per user.

----------------------------------------------------------------------------------------------

**Assumptions & Edge Cases**

   1. Email is a unique way to identify a contact.
   
   2. If there are several email contacts, log and skip.
   
   3. Required fields that are missing should be skipped or flagged for manual repair.
   
   4. Three times before logging failure, attempt updating.
