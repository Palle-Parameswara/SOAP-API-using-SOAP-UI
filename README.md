# SOAP-API-using-SOAP-UI
Here's a step-by-step guide for integrating Salesforce with a SOAP API using SOAP UI, including generating the Enterprise WSDL, authenticating via the login method, and executing a SOQL query:

### Step 1: Generate the Enterprise WSDL in Salesforce

1. **Log in to Salesforce**:
   - Go to your Salesforce instance and log in with your credentials.

2. **Generate the Enterprise WSDL**:
   - Navigate to **Setup**.
   - In the Quick Find box, search for **API**.
   - Click on **API** under the **Integrations** section.
   - Under **API** documentation, you will see the **Generate Enterprise WSDL** link. Click on it.
   - A file download dialog will appear. Download the WSDL file (it will be named something like `enterprise.wsdl`).
   - ![image](https://github.com/user-attachments/assets/3e253b0f-e8fe-4328-9c8c-d8b6caa4a7d0)


### Step 2: Open the Enterprise WSDL in SOAP UI

1. **Open SOAP UI**:
   - Launch SOAP UI.

2. **Create a New SOAP Project**:
   - In SOAP UI, go to **File** → **New SOAP Project**.
   - In the **Project Name** field, enter a name for your project (e.g., `Salesforce Integration`).
   - In the **Initial WSDL** field, click on the folder icon and select the `enterprise.wsdl` file that you downloaded from Salesforce.
   - Click **OK** to create the project.

3. **Explore the WSDL**:
   - SOAP UI will load the WSDL and generate the available operations. You should see a set of operations under the **Request** section of the **Project Explorer**.

### Step 3: Change the Security Token and Use It in Login

1. **Find Your Security Token**:
   - Go to Salesforce and click on your **avatar** in the upper-right corner.
   - Select **Settings** → **My Personal Information** → **Reset My Security Token**.
   - Click **Reset Security Token**, and Salesforce will send a new token to your email.
   - Copy this token from your email.
     ![image](https://github.com/user-attachments/assets/1bab3916-8e56-4570-9ec4-7d64529c1110)


2. **Login Request**:
   - In the SOAP UI, expand the **enterprise.wsdl** project.
   - Under **Binding** → **login** → **Request 1**, you should see the login operation.
   - Open the **Request 1** window.

3. **Modify the Login Request**:
   - In the request XML, you’ll see something like this:

     ```xml
     <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                       xmlns:urn="urn:enterprise.soap.sforce.com">
        <soapenv:Header/>
        <soapenv:Body>
           <urn:login>
              <urn:username>Your_Username</urn:username>
              <urn:password>Your_PasswordYour_Security_Token</urn:password>
           </urn:login>
        </soapenv:Body>
     </soapenv:Envelope>
     ```

   - Replace `Your_Username` with your Salesforce username.
   - Replace `Your_Password` with your Salesforce password.
   - Append the **Security Token** (you copied in Step 1) to your password. For example, if your password is `password123` and your security token is `ABC123xyz`, the password should be `password123ABC123xyz`.

4. **Submit the Login Request**:
   - Click the green **Submit** button in the top-left corner of the request window.
   ![image](https://github.com/user-attachments/assets/4989820d-a34b-4954-803e-2692f0af1aef)


5. **Collect Session ID and Server URL**:
   - If the login is successful, you will get a response similar to this:

     ```xml
     <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                       xmlns:urn="urn:enterprise.soap.sforce.com">
        <soapenv:Body>
           <urn:loginResponse>
              <urn:result>
                 <urn:sessionId>Your_Session_ID</urn:sessionId>
                 <urn:serverUrl>Your_Server_URL</urn:serverUrl>
              </urn:result>
           </urn:loginResponse>
        </soapenv:Body>
     </soapenv:Envelope>
     ```

   - **Copy** the **Session ID** and **Server URL** from the response. These will be used in subsequent requests.

### Step 4: Use the Session ID and Server URL to Query Salesforce Data

1. **Create a New SOAP Request for Query**:
   - In the **Project Explorer**, navigate to **Binding** → **query** → **Request 1**.
   - Right-click and select **New Request** to create a new query request.

2. **Modify the Query Request**:
   - In the request XML, you will see something like this:

     ```xml
     <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                       xmlns:urn="urn:enterprise.soap.sforce.com">
        <soapenv:Header/>
        <soapenv:Body>
           <urn:query>
              <urn:sessionId>Your_Session_ID</urn:sessionId>
              <urn:queryString>SELECT Name, IsDeleted FROM Account</urn:queryString>
           </urn:query>
        </soapenv:Body>
     </soapenv:Envelope>
     ```

   - Replace `Your_Session_ID` with the session ID you got from the login response.
   - Replace `Your_Server_URL` with the server URL you got from the login response.
   - You can also modify the SOQL query inside `<urn:queryString>`; for example, the query `SELECT Name, IsDeleted FROM Account` will retrieve the `Name` and `IsDeleted` fields from all `Account` records.

3. **Set the Endpoint URL**:
   - In the **Request** window, set the **Endpoint** URL to the `serverUrl` you got from the login response (e.g., `https://na1.salesforce.com/services/Soap/u/50.0`).
     ![image](https://github.com/user-attachments/assets/57230008-ee9c-422b-9368-0326b70358ae)


4. **Submit the Query**:
   - Click **Submit** to send the query.
   - If successful, you’ll get a response with the query results. The response will contain the `Name` and `IsDeleted` values of the `Account` records.

   Example response:

   ```xml
   <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                     xmlns:urn="urn:enterprise.soap.sforce.com">
      <soapenv:Body>
         <urn:queryResponse>
            <urn:result>
               <urn:records>
                  <urn:Name>Account 1</urn:Name>
                  <urn:IsDeleted>false</urn:IsDeleted>
               </urn:records>
               <urn:records>
                  <urn:Name>Account 2</urn:Name>
                  <urn:IsDeleted>true</urn:IsDeleted>
               </urn:records>
            </urn:result>
         </urn:queryResponse>
      </soapenv:Body>
   </soapenv:Envelope>
   ```

### Summary of Key Steps:
- Generate the **Enterprise WSDL** in Salesforce.
- Use SOAP UI to open the WSDL and make a **login request** with the username, password, and security token.
- Collect the **session ID** and **server URL** from the login response.
- Use the **session ID** and **server URL** to make a **query request** to Salesforce and retrieve data using SOQL.

Let me know if you need any more details or run into any issues during the process!
