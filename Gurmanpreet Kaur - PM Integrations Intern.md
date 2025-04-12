**Zeotap to Salesforce Contact Integration**

**Objective** - Simulating an integration between _Zeotap & Salesforce service cloud_ , focusing specifically on the contact object 

**Goals** -
1. Push contact data from Zeotap into Salesforce
2. Handle creation of new contacts & updation of existing ones

**Data Flow Overview** 
1. Zeotap receives customer data.
2. Sends data to the integration layer/API service.
3. The API service:
   
    3.1 Authenticates with Salesforce via OAuth 2.0.
   
    3.2 Queries if contact exists (via email).
   
    3.3 Creates or updates Contact in Salesforce.
   
5. Handles success/failure logging and retry.

   **Zeotap -> API gateway -> Authentication -> Salesforce Contact Object**

   **Authentication Method**
   **Protocol** - OAuth 2.0
   _Username - Password_

   
   
