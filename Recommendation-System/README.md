# Recommendation System

## 1.1. Overview
In this lab we will deploy Recommendation system on Azure Machine Learning system. In this deployment we will try to identify the logic behind the matchbox recommender model which is the embedded recommendation solution in Azure Machine Learning Studio. Keeping in mind that there exist custom recommendation solutions available with R and Python scripting, we will focus on Train and Score MatchBox Recommender modules ready to use in Azure Machine Learning. In case you are interested in technical details of this model, you may refer to [paper](http://research.microsoft.com/apps/pubs/default.aspx?id=79460) published by Microsoft Research. This recommender system finds association rules between users and items or within items itself or users itself. It can help to identify:
    - Related items. i.e. Item A considered together with item B 
    - Related users. i.e. User A has similar tendency as user B
    - Item recommendation to specific or new users. Here the items may be DVDs, restaurants, bicycles, foods etc.
 
### 1.1.1. Objectives
This lab aims to demonstrate how to use Recommendation system within Azure Machine Learning workspace. It shows item recommendation to a new and existing users, shows how to find related items or related users that have same tendency. We will also walk through the basics of how to test a Machine Learning model in excel, and consume a model inside a simple C# application.  

## 1.2. Data
To be able to make some analysis at first sight, we will be using a small amount of a sythetic data to create a test experiment using the Matchbox Recommender modules. We will be using three CSV files located in the data folder:  
 -  users.csv
 -  items.csv
 -  ratings.csv

Each file is of the following formats:

**users.csv**
 
| customerId | type |
| --- | --- |
|c1	| v |
| ...	| ... |
|c7	| mv |
|c8	| m |
 
**items.csv**
 
| restaurantId | type |
| --- | --- |
| r1 |	m |
| ... | ... |
| r5 | v |
| r6 | mv |

 
**ratings.csv**
 
| customerId | restaurantId | rating |
| --- | --- | --- |
| c1 | r2 | 3 |
| c1 | r3 | 1 |
| ... | ... | ... |
| ... | ... | ... |
| c8 | r6 | 2 |


To use matchbox recommendation system we need **at least the ratings table**, other tables are just optional (use either the ratings table or the triplet). If using all tables, matchbox recommender will work in **collaborative filtering mode**, which means the features of the items and users will be taken into consideration in the recommendation process. 

    - Note: In the above data, we conciously select the very distinguishing features like vegeterian, grill restaurant etc. In the matchbox recommendation system, especially with this small amount of training data, it doesn't mean that the system will not recommend a grill restaurant to a vegetarien person! With big enough, quality data it is possible to succeed logically expected results but with very small amount of training data it is not always the case. 

 Lets start explore the recommendation system by using the above information, data tables.

### 1.2.1. Upload the Data  
To upload the datasets, follow steps 1-3 for each CSV file.  

1. Click new on the bottom left of the page.  
![](./imgs/4.2.1.i1.png)

2. Then click Dataset and choose from a local file.  
![](./imgs/4.2.1.i2.png)

3. Navigate to the the file "ratings.csv". Feel free to change the name of the dataset or enter a description. The click the checkmark button on the bottom right corner to upload the file.   
![](./imgs/8.2.1.i1.png)

## 1.3. Recommend items to users
For this lab, we walk through very simple Machine Learning solutions. There are many ways this lab can be extended to support more data manipulation, cleaning, splitting etc.  

1. Create a blank experiment.  

2. Build the following workflow by simply drag and dropping modules onto the canvas. The datasets are located under “Saved Datasets” -> “My Datasets”. The *Train Matchbox Recommender* can be found in "Machine Learning" -> "Train", and the *Score Matchbox Recommender* is under "Machine Learning" -> "Score".  
![](./imgs/8.2.1.i3.png) 

3. Set the *Train Matchbox Recommender* module's **Number of Traits** property to 3. This is generally equal to the maximum number of ratings per user, which is 3 in our case.  
![](./imgs/8.2.i003.png) 

4. Set the *Score Matchbox Recommender* module's **Recommender prediction kind** property to "Item Recommendation" and **Recommended item selection** property to "From all Items". Also set the "Maximum number of items to recommend..." to **3**.   
![](./imgs/8.2.i004.png) 

5. Run the experiment and visualize the output port of the *Score Matchbox Recommender* module.  
![](./imgs/8.2.i005.png)   
    In the output window, first column lists all the users and in order next columns shows the recommended items to that specific user in the same row.
    
    As we set the *maximum number of items to recommend* to 3, system tries to recommend the most appropriate first 3 item. Also If we look closer to the first row, we can see that the 3rd item (column with name Item3) recommended for the first user is **r6** which is the most appropriate choice within the available set but lets **try the next step**. 

6. Add the below two new rows of data into the rating.csv file. The follow steps in section 1.2.1 to replace the existing CSV file, or you can simply use "Enter Data Manually" module and copy/paste whole new CSV content into this moudule.  
![](./imgs/8.2.i005.3.png)  

7. "RUN" the experiment again and visualize the output port again.  
![](./imgs/8.2.i005.2.png)  

8. You will see that the recommended third item is changed from **r6** to **r3**. This is mainly because other users (two new rows that we added) with similar profile liked **r3** so much. So the system propose **r3** again to the customer **c1** (even he/she already rated it with one star before).

9. What if we set the parameter to *From Rated Items (...* and "minimum size of recommendation pool..." to *4*?  
![](./imgs/8.2.i007.png) 

10. Set the parameter values as above and Run the experiment, visualize the output port of the score matchbox recommender module.  
![](./imgs/8.2.i006.png) 

11. Here you can see that the system recommends only from a pool of items that user rated. Also there is no recommendation for user *c5, c6, c7*. Because these users rated less than four item but our pool condition parameter set to *4* in the above property window.  
    
12. You may have observed the two similar property value in almost all *Recommender prediction kind* type. These two options are either "From Rated Items" or "From All Items". In production mode, when you publish the solution as web service, your model property should set to "From All Items" but if you are evaluating the system, testing it etc. you can switch to "From Rated Items" to see if your model works as expected based on the existing rating information.   

## 1.4. Find related users
Assume you want to match your users', customers' profiles, try to find related users or users that have similar tendency. In such case you may use the "Related Users" value set in the **Recommender prediction kind** property.  

1. Set the *Recommender prediction kind** property to "Related Users".  
![](./imgs/8.2.i008.png) 

2. Run the experiment and visualize the output port.  
![](./imgs/8.2.i009.png) 

3. As you can see from the output, in order users c4, c6, c7, c3, c5 are related with user **c1**. If you check our data tables or the visual illustration of the user-item-useritem ratings triplets, you will see that users **c4, C6** and so forth, all like vegetables like user **c1**. Also as we constrained our system to propose 5 recommendations, it proposed user **c5** related to user **c1** even the profile is grill. Becuase c5 rated the same item (restaurant) as c1 (event dont liked it).

## 1.5. Find related items
Similar to the experiment in the previous lab session, it is possible to find related items. This approach can be used to recommend items to a user based on the related items. i.e. if item X and Y have similar profile or X and Y bought together, then you may recommend item Y to a user who bought or selects item X.

1. Set the *Recommender prediction kind** property to "Related Items"  
![](./imgs/8.2.i011.png) 

2. Run the experiment and visualize the output port.  
![](./imgs/8.2.i010.png)  

3. You will see the grill restaurants are related to each other and vegeterian restaurants too.  

## 1.6. Recommending to a brand new user
Above samples are using existing users. i.e. recommending a new, second or third item to buy to an existing user in the system. Or to advertise second, third related items to the one that the user is viewing etc. What about if we have a new user registered to the system but haven't made any rating yet? To be able recommend items etc. for a new user, you should definitely work with input data triplet (with optional data, users and items available). Also you must have the features of the new user (In our case it is the type column of the user table). 

### 1.6.1. Deploying the web service for a brand new user
1. Set parameters of "Score Matchbox Recommender" to **3** with below settings.  
![](./imgs/8.2.i012.png)  

2. Run the experiments.  

3. Create a predictive experiment.  
![](./imgs/8.2.i013.png)  

4. Add second web service input module as in the below workflow and Run the predictive experiment.
![](./imgs/8.2.1.i5.png)  

5. Deploy the web service.  
![](./imgs/8.2.i020.png)  

### 1.6.2. Testing the web service
1. The published web service dashboard will open. Because we have more then one webservice input, we can't use the "Test dialog input". So we will click on the "Excel app" link to download sample excel file|application to test our web service.   
![](./imgs/8.2.1.i6.png)  

2. Download the Excel file and open it. (You should click "enable content" button in case your computer's security doesnt allow to open Excel apps)

3. Click on the webservice name on the excel.  
![](./imgs/8.2.1.i7.png)  

4. On the top row of the excel sheet, in order, write the following values in each cell as in the below screenshot.
  
| A         | B   | C        | D          | E    |
| ---       | --- | ---      | ---        | ---  |
|customerId |type |customerId|restaurantId|rating|
| NEWC01    | v   | NEWC01   |            |      ||
  

5. As there are two web service input, you need to enter the customer id two times (you can fine tune input parameters with Select columns from dataset etc. New user **must have** unique userid (customer id) that does not exist in the training data. So we used **NEWC01** as our new user id. Also as mentioned earlier, new user data must contain the feature vector, feature values. In our case feature data is **v** which means the customer is vegeterian or like vegetable plates. You keep the ratings data empty (except the same unique id). Now adjust the Excel web service app's parameters. You need to specify the source cells for the web service's input output ports. Type "Sheet1!C1:E2" for input1, "Sheet1!A1:B2" for input2, and type "Sheet1!A5" for the address of the cell where the webservice output will be printed. Finally press *Predict* button. 

    - Please note that if there is an error caused by the inputs it could be because your inputs are swapped. Therefore, try typing  "Sheet1!A1:B2" for input1, and "Sheet1!C1:E2" for input2.  

    ![](./imgs/8.2.1.i8.png) 

6. Results are not surprising. For a new user who is vegeterian or who has a feature is **v**, recommended restaurants are in order: r5, r2, r3. Which are vegeterian oriented restaurants.   
![](./imgs/8.2.1.i9.png)   

### 1.6.3. Consuming the web service in an application
1. Open up Visual Studio 2017 or Visual Studio 2015.

2. Create a new project.  
![](./imgs/1.6.3.1.png) 

3. Select C# Console application and click OK to create a blank application template.  
![](./imgs/1.6.3.2.png)

4. Once the project created, in the “Solution Explorer” window, right click on the project name “ConsoleApplication1” (if you haven’t changed the default project name) and select “Manage NuGet Packages…” menu item in the pop-up menu.  
![](./imgs/1.6.3.3.png)

5. “NuGet Package Manager” window will open in a new tab. Type “Microsoft.AspNet.WebApi.Client” in the search box to filter specific package and then click on the “Install” button to have this package installed in our console application. This package is used for content negotiation over network with JSON format which is our web service requirement.
![](./imgs/1.6.3.4.png)

6. Once the package installed switch back to “Program.cs” file tab, or double click on the “Program.cs” file name in the “Solution Explorer” window. Here we will type our C# commands to call the web service and show the result.  

7. The C# code that we will write in “Program.cs” file is actually ready in the Azure Machine Learning portal. Switch back to the Web service page in Azure Machine Learning portal where we made the tests in previous section. On this web service page, click on the “REQUEST/RESPONSE” link under the “Default Endpoint” section.  
![](./imgs/1.6.3.5.png)

8. A new web page called Request Response API Documentation will open. Scroll to the section “Sample Code”. In the “Sample Code” section, C# tab is selected by default. Click on the “Select sample code” button on the right top corner of this section and copy all selected text into clipboard by pressing CTRL+C key combination.  
![](./imgs/1.6.3.6.png)

9. Paste the copied code into “Program.cs” file in Visual studio by deleting all other content that exist in the “Program.cs” file.  
![](./imgs/1.6.3.7.png)

10. Now we need to make few simple changes in this code that we copy/pasted. Find the code line that starts with:  
    ```c#
    const string apiKey = "abc123";
    ```  
    
    In this line we need to replace the “abc123” string with the original one. This is something like a password to access our web service. Without this key/password, it is not possible to call our web service. This password is mandatory, otherwise anyone who knows the webservice address can call the webservice many times which will increase the cost in Azure Machine Learning service charge. 

11. Go back to the Azure Machine Learning webservice page and copy the secret “API key”. Replace the above “abc123” string with this one.  
![](./imgs/1.6.3.8.png)

12. Update the following code to pass in valid Input parameters.   
![](./imgs/1.6.3.9.png)

13. Now we are ready to run the sample C# application. Press CTRL + F5 key combination. The following is a sample of the output. As you can see the restuarants r5, r2, and r3 were recommended for both the new users, which makes sense because we provided identical inputs.  
![](./imgs/1.6.3.10.png)

## 1.7. Conclusion  
You have successfully built, deployed, and consumed a Machine Learning solution using Azure Machine Learning and Visual Studio 2017!


