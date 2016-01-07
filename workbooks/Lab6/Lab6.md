- - -
# Lab 6 - Use the MFP adapter framework (Client side)

With the MFP adapter in place on the server side, you will now add code to the client app to access the adapter.

>Note:  For this lab there are snippets files included in the **/snippets** folder of your workspace which can be used to quickly copy/paste the large source code changes in the lab steps below.

##Steps

###1. Modify app.js to invoke the adapter procedure

The EmployeeService service in app.js returns the list of employees to the controller.  We will replace the Angular **$http** service logic with MobileFirst WLResourceRequest API.  WLResourceRequest is a REST client api provided in the MobileFirst client SDKs (hybrid and native) that integrates with MobileFirst security and analytics.

1. Open the **app.js** file in **IBMTechEXEmployee/www/js**.  
2. Find the **EmployeeService** method.  You will replace the method implementation to use MFP Client API instead of the Angular $http services.
3. Replace the implementation of the **EmployeeService** method with the code, below (*use the corresponding snippet file in **/snippets** to copy/paste*):

   ``` javascript
     
     ibmApp.factory("EmployeeService", function($http){
        console.log( ">> in EmployeeService ...");
        var employees = [];
        var resourceRequest = new WLResourceRequest(
            "/adapters/EmployeeServices/services/list", WLResourceRequest.GET
        );
        return {
            getEmployeeList: function(){
                return resourceRequest.send().then(function(response){
                    employees = response.responseJSON.employees;
                    return employees;
                }, function(response){
                    console.log("error:" + response);
                    return null;
                });
            },
            getEmployee: function(index){
                return employees[index];
            },
            getEmployeeById: function(id){
                var _emp;
                angular.forEach(employees, function(emp) {
                    console.log(">> getEmployeeById :" + id + " ==  " + emp._id );
                    if(emp._id == id){ _emp = emp; }
                });
                return _emp;
            }
        };
    })

   ```
  
  **Screenshot Before:**  
  <img src="images/Lab6-01-dollar-http.png" width="650"/>

  **Screenshot After:**
    <img src="images/Lab6-02-WLResourceRequest.png" width="650"/>

4. **Save** your updates!

### 2. Test the changes

1. Return to the command line and ensure you are in the `IBMTechEXEmployee` folder 

2. Run the application using

        mfp cordova preview 
         - or -  
        mfp cordova emulate

   > If you get an error using the mfp cordova preview (or you simply get a blank employee list) make sure you clean your browser cache.  Also make sure you are invoking the command from the IBMTechEXEmloyee directory.

3. You should see that the Employee List is populated from the data provided by the MobileFirst Platform adapter.  There are now more employees and they have unique images for each.  But, if you tap on any employee you will see that the details are still the template values, not those provided by the adapter.  You will correct that next.
<img src="images/Lab6-11-test-employee-list.png" width="600"/>

4. Close the **browser**

###3. Modify the EmployeeDetailsService service to invoke the adapter procedure

EmployeeDetailsService returns details for the specified employee id.  This set of steps will replace the Angular $http processing with WLResourceRequest.

1. Locate the **EmployeeDetailsServices** method in **IBMTechEXEmployee/www/js.app.js**.  You will also replace the current Angluar $http implementation of this service with calls to the MobileFirst Client API.  The service only needs to get the employee details as provided by the adapter.  There is no need to parse and massage the data returned from the server. The filtering (heavy lifting) was done on the adapter side.  When you have hundreds or thousands of employees this is huge time saving which done on the server side.

2. Replace the implementation of the **EmployeeDetailsService** method with the code, below (*use the corresponding snippet file in **/snippets** to copy/paste*):

   ``` javascript
      
      ibmApp.factory("EmployeeDetailsService", function($http){
        console.log( ">> in EmployeeDetailsService ...");
        return {
            getEmployeeDetails: function(empId){
                //using path param.
                var resourceRequest = new WLResourceRequest(
                    "/adapters/EmployeeServices/services/details/" + empId, WLResourceRequest.GET
                );
                return resourceRequest.send().then(function(response){
                    return response.responseJSON.details;
                }, function(response){
                    console.log("error:" + response);
                    return null;
                });
            }};
     })
   
   ```
   
    **Before:**
    
    <img src="images/Lab6-05-emp-details-before.png" width="450"/>  

    **After:**
   
    <img src="images/Lab6-06-emp-details-after.png" width="650"/>  
    
3. **Save** your updates!
    
    
###4. Modify the employeeDetailCtrl controller to match the EmployeDetailsService

We no longer need the 'for' loop to parse the complete set of detail data, as our adapter provides a single set of employee detail data for the requested employee id.  We can simplify the client controller to take adavantage of having moved that "business logic" to the server.  

This is a very simple example of offloading logic to the adapter tier, but you can imagine much more complex variations with date/time formatting and calculations, phone numbers, mashups of multiple back-end data sources, calculations, etc...  The adapter modularizes the solution, simplifying and reducing the logic required on the mobile device.

1.  Replace the implementation of the **employeeDetailCtrl** method with the code, below (*use the corresponding snippet file in **/snippets** to copy/paste*):
 

   ``` javascript
   ibmApp.controller('employeeDetailCtrl', function($scope, EmployeeService,
                                 employeeDetailList , empId ,$ionicHistory) {
      $scope.employee = {
            "first_name" : "",
            "last_name" : "",
            "_id" : ""
      }
      $scope.employeeDetails = {}
      console.log(">> in - employeeDetailCtrl:" + employeeDetailList);
      //Employee service cached the list of employee
      $scope.employee = EmployeeService.getEmployeeById(empId);
      $scope.employeeDetails = employeeDetailList;
      $scope.employeeDetails.email =  angular.lowercase($scope.employeeDetails.email);
   
   })

   ```
  
    **Before:**
    
    <img src="images/Lab6-08-emp-det-ctrl-after.png" width="650"/>  

    **After:**

    <img src="images/Lab6-07-emp-det-ctrl-before.png" width="650"/>
    
2.  **Save** your updates!  

###5. Verify your changes

1. Run the application and view the results

        mfp cordova preview
2.  You should now see both the employee list and the employee details are coming from the data supplied by the adapter.
<img src="images/Lab6-12-test-employee-details.png" width="600"/>



## Summary

In this lab, you modified the client code to use the MobileFirst adapter instead of Angular $http calls to retrieve the employee list and detail data.

If you were unable to complete this lab, you can catch up by running this command:

     git checkout -f step-6
     
.






