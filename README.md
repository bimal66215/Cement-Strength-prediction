# Cement-Strength-prediction

****Problem Statement****

To build a regression model to predict the concrete compressive strength based on the different features in the training data. 

****Architecture****

![image](https://user-images.githubusercontent.com/82671006/162594745-63da8165-0560-48c0-a187-a0df3761a4c9.png)

  
****Data Description****
Given is the variable name, variable type, the measurement unit and a brief description. 
The concrete compressive strength is the regression problem. The order of this listing 
corresponds to the order of numerals along the rows of the database. 

![image](https://user-images.githubusercontent.com/82671006/162594774-d9224095-a5e6-4e0c-af51-682628ffdd4a.png)
![image](https://user-images.githubusercontent.com/82671006/162594821-1311b1f0-386d-4543-91a2-bc515d46f236.png)

Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.
 
**Data Validation ******

In this step, we perform different sets of validation on the given set of training files.  
1.	 Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder."

2.	 Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad_Data_Folder."


3.	 Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder".

4.	 The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".


5.	Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad_Data_Folder".



**Data Insertion in Database******
 
1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".
 
**Model Training****** 

1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
2) Data Preprocessing  
   a) Check for null values in the columns. If present, impute the null values using the KNN imputer
   b) transform the features using log transformation
   c) Scale the training and test data separately 
3) Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
   To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.
4) Model Selection - After clusters are created, we find the best model for each cluster. We are using two algorithms, "Random forest Regressor" and “Linear Regression”. For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the Rsquared scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction. 
 
**Prediction Data Description******
 
Client will send the data in multiple set of files in batches at a given location. Data will contain climate indicators in 8 columns.
Apart from prediction files, we also require a "schema" file from client which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.

**Data Validation ****** 

In this step, we perform different sets of validation on the given set of training files.  
1) Name Validation- We validate the name of the files on the basis of given Name in the schema file. We have created a regex pattern as per the name given in schema file, to use for validation. After validating the pattern in the name, we check for length of date in the file name as well as length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder". 
2) Number of Columns - We validate the number of columns present in the files, if it doesn't match with the value given in the schema file then the file is moved to "Bad_Data_Folder". 
3) Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder". 
4) Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If dataype is wrong then the file is moved to "Bad_Data_Folder". 
5) Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "Bad_Data_Folder".  

**Data Insertion in Database ******

1) Database Creation and connection - Create database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" on the basis of given column names and datatype in the schema file. If table is already present then new table is not created, and new files are inserted the already present table as we want training to be done on new as well old training files.     
3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".


**Prediction****** 
 
1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.
2) Data Preprocessing   
   a) Check for null values in the columns. If present, impute the null values using the KNN imputer
   b) transform the features using log transformation
   c) Scale the training and test data separately 
3) Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.
4) Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.
5) Once the prediction is made for all the clusters, the predictions along with the original names before label encoder are saved in a CSV file at a given location and the location is returned to the client.
 
**Deployment******

We will be deploying the model to the Pivotal Web Services Platform. 
This is a workflow diagram for the prediction of using the trained model.                  
                                                      

Now let’s see the Cement_Strength project folder structure.
 
![image](https://user-images.githubusercontent.com/82671006/162594874-52e63975-e06d-4dae-9fa5-9aa64626c2db.png)


requirements.txt file consists of all the packages that you need to deploy the app in the cloud.

 
![image](https://user-images.githubusercontent.com/82671006/162594878-5e84169f-89ef-4402-af62-c880931cacf9.png)

main.py is the entry point of our application, where the flask server starts. 

 ![image](https://user-images.githubusercontent.com/82671006/162594881-9de2d1d4-36ef-48d2-beb3-7e2f6b331518.png)

This is the predictionFromModel.py file where the predictions take place based on the data we are giving input to the model.
 
 ![image](https://user-images.githubusercontent.com/82671006/162594886-d054e37b-7d79-461a-88b0-124ebd5b1d8d.png)

manifest.yml:- This file contains the instance configuration, app name, and build pack language.

![image](https://user-images.githubusercontent.com/82671006/162594889-be0c52da-1ff3-4e44-bc12-775677d8640f.png)

 
Procfile :- It contains the entry point of the app.

 ![image](https://user-images.githubusercontent.com/82671006/162594893-7eb95d75-f5da-4b24-bac6-20448d1d0657.png)

runtime.txt:- It contains the Python version number.

_Visit the official website https://pivotal.io/platform._
 
 ![image](https://user-images.githubusercontent.com/82671006/162594916-185d04b9-4c43-4ff5-9324-23fb72819d9d.png)

Scroll Down to see the Start Trial Button


![image](https://user-images.githubusercontent.com/82671006/162594922-e9fe0750-9d07-4db2-aa97-94df8a5926b8.png)

 
Click on the start trial button and the next interface will open. Then I will click on I’m ready to continue

![image](https://user-images.githubusercontent.com/82671006/162594955-52cfb28d-a89b-4144-808d-1112e4d6d32a.png)

Click on Download for Windows 64 bit, and then zip file will be downloaded. Keep it for future uses.

![image](https://user-images.githubusercontent.com/82671006/162594981-e4438b73-cfe7-45b8-b52b-50a2e269bce4.png)

Now click on Let’s Keep Going

![image](https://user-images.githubusercontent.com/82671006/162594992-ec7654ad-99f8-42eb-beb5-8a73c941463d.png)

Click on Create Your Account

![image](https://user-images.githubusercontent.com/82671006/162594997-21ffa876-9383-473b-937e-c0eeb17beaaa.png)

Fill Up your Details For registration
Do the email verification
Then login in again

![image](https://user-images.githubusercontent.com/82671006/162595005-408bca5a-c49d-4700-b8ea-857e72016345.png)

 
After logging you will see this screen below and start your free trial.
Write any Company or which one you prefer

![image](https://user-images.githubusercontent.com/82671006/162595013-5a509cc9-15db-4e90-b351-05e3127c3f8b.png)

Enter your Mobile Number for Verification

 ![image](https://user-images.githubusercontent.com/82671006/162595020-9a96c9d2-9f4c-44fa-9864-3e447e2d61c5.png)

Click on Pivotal Web Services

 ![image](https://user-images.githubusercontent.com/82671006/162595024-3adfac50-99a5-4e39-87b9-10c0712d950f.png)

Give any Org name


![image](https://user-images.githubusercontent.com/82671006/162595066-6a5634df-d3c4-4bc1-9d74-9f072a05aa2c.png)


 
Now you are inside your Org, and by default, development space is created in your org. You can push your apps here.
The cloud signup process is done, and the setup is ready for us to push the app.

Previously you have downloaded the CLI.zip file. Unzip the file and install the .exe file with admin rights.
After a successful installation, you can verify by opening your CMD and type cf. 
Then you will get a screen which is shown below
 
![image](https://user-images.githubusercontent.com/82671006/162595076-0db7fd52-1755-4c32-81b3-771c0ac3d0c8.png)

If you see this screen in your CMD, the installation is successful.
Now type the command to login via cf-cli

Next, enter your email and password. Now you are ready to push your app.
Now let’s go to the app which we have built.
 
![image](https://user-images.githubusercontent.com/82671006/162595086-b6ad72d0-6a53-4c54-9d80-94b2dd7d3a92.png)

Navigate to the project folder after downloading from the given below link:-

Then write cf push in the terminal.
 
![image](https://user-images.githubusercontent.com/82671006/162595090-eaa2d7cb-8a9f-41b9-8039-00e47095f209.png)

After the app is successfully deployed in the cloud, you will see the screen below with the route.


![image](https://user-images.githubusercontent.com/82671006/162595094-aaa927e8-9e49-4c23-a2eb-1ecc7a59bd3e.png)

Finally, the app is pushed in the cloud.
Lets Open Postman and see the result.

![image](https://user-images.githubusercontent.com/82671006/162595101-24a88637-e096-4896-9cc3-2e19eb7c4693.png)


