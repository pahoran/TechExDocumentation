- - -
# Lab 5 - Implement the MFP adapter framework (Server Side)

If you have looked at the code in app.js in detail, you have noticed that the app is currently using the Employee service and Employee Details controllers.  These services use angular $http services to get the application data, which is stored in .json files locally on the device.  In the next section, you will create MFP adapters to get data from back-end services.  MFP Adapters provide a way to retrieve and manage data for your mobile client app on the server side.

The MobileFirst Adapter framework provides support for developing adapters in Java or JavaScript to interface with various back-end architectures such as HTTP, SQL and SAP among others.  The Adapter framework also automatically couples with the MFP security and analytics frameworks, enabling consistent security to back-end resources and ability to record, measure and compare the operational characteristics of the adapter traffic - volumes, servers, response times, etc...

For our lab, we will build a Java adapter to interact with a java class providing the interface to our backend system-of-record.  Our class provides the following interface:

<img src="images/Lab5-03b-pojoEmployee-docs.png" width="600" border="1"/>

We are provided one class, **pojoEmployee**, which has 2 methods - **getEmployees()** and **getEmployeeDetails()**.  The **getEmployees()** method takes no parameters and returns a JSON-formatted string of employees (our employee "list"), and **getEmployeeDetails()** takes an employee ID parameter and returns the details for that employee.  

Using imagination, the **pojoEmployee** class could be an accessor class provided by any kind of backend providing a Java interface or could be custom Java code that you write yourself to interact with various Java-friendly app servers, databases, queueing interfaces, API tiers and other systems of record.  The adapter framework allows you to easily adapt to changes in backend data formats or even completely replace with a different data source, without affecting the client app running on (dozens, hundreds, thousands or millions of) mobile devices.   Changes in your back-end can be addressed in the adapter tier without forcing you to rebuild and redistribute the client app.


>Note:  For this lab there are snippets files included in the **/snippets** folder of your workspace which can be used to quickly copy/paste the large source code changes in the lab steps below.

##Steps
### Create the adapter

1.  Change context back to IBMTechExBackEnd

        cd ../IBMTechExBackEnd

2. Add a Java-based adapter to your project

        mfp adapter add

   1. When prompted, name your adapter **`EmployeeServices`**
   
   2. For adapter type select : **`Java`**
   
      <!-- ![cli mfp adapter add](images/Lab5-01-mfp-adapter-add.png) -->
        <img src="images/Lab5-01-mfp-adapter-add.png" width="700"/>
   
   3. For Java package enter : **`com.ibm`** and press **`Enter`**.  You should get the following success message:
   
      <!-- ![cli mfp adapter add complete](images/Lab5-02-mfp-adapter-add.png) -->
        <img src="images/Lab5-02-mfp-adapter-add.png" width="700"/>

3. Looking at your file directory you should see the following structure/files

   <img src="images/Lab5-03a-project-folder.png" width="250"/>

   
### Implement the adapter procedures
The Java adapter implements the JAX-RS standard, allowing your adapter to also serve as a REST-ful endpoint.  The procedures in the adapter are linked to HTTP verbs such as GET and POST.  The adapter is created with sample procedures, which you can remove.  In the next several steps, you will add code to implement two new methods:

- **list()**
- **details()**

1. copy the dependency jar file **pojoEmployee.jar** from the **MobileRoadShow7.1/extras** folder to **MobileRoadShow7.1/IBMTechExBackEnd/adapters/lib** folder

        cp ../extras/pojoEmployee.jar adapters/EmployeeServices/lib/
        
   <img src="images/Lab5-03c-copy-pojoEmployee-jar2.png" width="700" border="1"/>
      
2. Using your favorite IDE open the **EmployeeServicesResource.java** file in the **IBMTechExBackEnd/adapters/EmployeeServices/src/com/ibm** directory.

3. Remove all of the methods from the class.  Your adapter should look like this:
   
   <!-- ![adapter source folder](images/Lab5-04-java-adapter.png) -->
   <img src="images/Lab5-04-java-adapter.png" width="650"/>
      
4. Modify the Path statement to root the adapter REST path at **`/services`** rather than `/users`.

    <img src="images/Lab5-04a-path-statement.png" width="350"/>

5. Add the import statement for our backend jar file after the other imports

   ``` java
      // import our java backend class
      import com.ibm.pojoEmployee;
   ```
    
5. Add the **employees** method just before the final curly brace.  This method implements the REST operation "/list", returning a list of all employees by calling into our **pojoEmployee** backend class and invoking the **getEmployees()** method.
   
   ``` java
    /* Path for method: "<server address>/IBMTechExBackEnd/adapters/EmployeeServices/services/list" */
    @GET
    @Produces("application/json")
    @Path("/list")
    public String employees() throws IOException{
        logger.info(">>EmployeeServicesResource -> in employees() ...");
        pojoEmployee rsp = new com.ibm.pojoEmployee();
        return rsp.getEmployees(); 
    }
   
   ```
   
    
6. Add the **getDetails** method just before the final curly brace.  This method implements the REST operation "/details/{empId}", returning the details of a given employee by calling into our **pojoEmployee** backend class and invoking the **getEmployeeDetails()** method with the supplied employee id.
  
   ``` java
    /* Path for method: "<server address>/IBMTechExBackEnd/adapters/EmployeeServices/services/details/{empId}" */
    @GET
    @Produces("application/json")
    @Path("/details/{empId}")
    public String getDetails(@PathParam("empId") String id) throws IOException{
      logger.info(">>EmployeeServicesResource -> in details() ...:" + id);
      pojoEmployee rsp = new com.ibm.pojoEmployee();
      return rsp.getEmployeeDetails(id);   
    }
   ```
 
  
8. **Save** your changes.
 
  Your adapter code should be like this now:

  <!-- ![java adapter 1](images/Lab5-05-adapter-source-1.png) -->
  <img src="images/Lab5-05-adapter-source.png" width="700"/>
  
### Test your adapter
The MFP CLI provides the ability to test adapters using command line commands.  This is not only helpful for manually testing your adapters during development, but it can be leveraged by automated test scripts as part of your DevOps process automation strategy.

1. To test your adapter using the MFP CLI, you must first build it and deploy it to the MFP Development server.
   
        mfp push
   
   <!-- ![mfp adapter call](images/Lab5-08-adapter-call.png) -->
   <img src="images/Lab5-08a-mfp-push.png" width="700"/>
  
   > **Note:** If you encounter compilation errors, you will need to correct them before moving forward.  You can get compilation error listings by using the -d switch on the push command:
   
        mfp push -d
  > Evaluate the results, edit your code and continue to push until your errors have been resolved.
   
2. Once your adapter builds correctly, open the **Operational Console**.  You should see that your EmployeeServices adapter has been deployed

        mfp console 

   <!-- ![console adapters](images/Lab5-09-console-adapters.png) -->
   <img src="images/Lab5-09-console-adapters.png" width="700"/>

3. Close the **browser**.
4. Test the **list** procedure using the CLI

        mfp adapter call 
        
   Use your keyboard arrow keys to highlight the endpoint **EmployeeService/services/list** and then press **Enter**.  The adapter response object will be printed in the console:
   <img src="images/Lab5-10-AdapterListResponse.png" width="600"/>

5. Test the **details** procedure using the CLI

        mfp adapter call 
        
   Use your keyboard arrow keys to highlight the endpoint **EmployeeService/services/details/{empId}** and then press **`Enter`**.  

   When prompted for the path parameters, enter **`/services/details/01800193`**, then press **`Enter`**.  This will retrieve the details record for employee Amy Jones.

   The adapter response object will be printed in the console:
   <img src="images/Lab5-11-AdapterDetailsResponse.png" width="600"/>
  
## Summary
In this lab, you added a Java-based MobileFirst adapter to your project.  You then edited the code to implement two procedures that will return a list of employees and employee details via REST interface calls from your mobile client.  You then used the MFP CLI to invoke your adapter procedures manually to confirm they work as expected.

If you were unable to complete this lab, you can catch up by running this command:

     git checkout -f step-5

