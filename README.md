# Regression_Housing_price
The first step in any model building is understanding the data you have.Majority of the time the data received will have missing values ,
unwanted features, duplicates and outliers. 
Following are the Features for the house prediction in the dataset provided:

'ID', 'area_type', 'availability', 'location', 'size', 'society','total_sqft', 'bath', 'balcony'.

'price' being the target or dependent variable.

## Dealing with unwanted features
Identiying unwanted features can be done through Univariate and Multivariate analysis using plots or other methods. 
Few common checks I do for quick analysis are :
1) Unique identifier columns like ID can be removed as it doesnot give any relevant information for the model prediction.    
2) Remove those with Majority of the entries are null
3) Remove those which Couldnot show any relation between the feature and the target or dependent value
4) Remove those with all the entries same (std dev =0)

Based on the above 'society','availability','area_type','ID' are unwanted features for the prediction.

## Dealing with incorrect data
The Next thing we can do is analying each important feature:
1) Identify mis-spelled or similar named entries and correct it.
	  for eg: incase of 'size' feature we can understand that it is talking about room size and can use value_counts() methods to identify the unique entries. if there are entries implying same but spelled differently like "Bedroom","BHK" combine those by replacing "Bedroom" with "BHK". Few entries might have mis-spelled like "RK" instead of "BHK"
2) Identify features with different Units and use the same unit for measurements. Either we can use conversion methods to to convert into same unit or ignore the unit feild completly
    for eg: "total_sqft" which is indeed a key feature for the price prediction should have same unit entries. For simplicity I have removed the measurement unit from the entry and kept as numerical field.
    But detailed analysis is pointing out that coverting the "Acres" or "cents" etc to square feet value could have given better data.
    Some enries are showing in ranges which also should be trated and converted ton sqft value.

## Missing Value Treatment    
3) Now let us do the missing value treatment:

    a) Dropping is easiest and simplest .My approach is Drop the row only if many features of that row is null.
    
    b) Another simplest way is Fill the categorical features missing value using Mode and numerical value using median or mean methods
    
       'size' and 'bath' features missed value filling is using the mode().
       
    c) For the dataset received , instead of simply taking mean or median ,approach used is creating groups and get the mean or median or mode out of that group.
    
       'total_sqft' field  has good relation with room size. So for filling the missing value, we can use the mean sqft value of the room size it belong to.
        df.groupby('size')['total_sqft'].mean()
	
        Similary for balcony used max() number of balconies for the room group. 
	
        df.groupby('size')['balcony'].max()
        
    d) The same concept is used for test data
    
 ## Utilizing Extra data available  
 4) Next step we can do is utilize the extra dataset provided .'Distance from city center' and 'average rent' .The prediction dataset and the supporting dataset have 'location' field as common. 
        Clean the 'location' feild using stripping and split functions.
        Remove the duplicate entries in all the datasets.
        Then do left join the datasets using 'location. Now we will have 'dist_from_city', and 'avg_2bhk_rent' also as inportant features.
        Merging of the three dataset has bring more null values . since the distance from city and average rent has key importance in the prediction, I took the binning approach.
	
   'Room group" and 'location group'
   
        Room Group: Room numbers 1 to 8 are treated individually and greater than 8 rooms are treated as 'Large BHK' group
        
        Location Group:Created 11 location group based on the distance from City . Keep in mind distance from City can never tell price is low or high as the proximity to airport and other facilities also will play a key role in the price.

## Removing Outliers
 
      a) Either we can remove using IQR method :The data points which fall below Q1 – 1.5 IQR or above Q3 + 1.5 IQR are outliers.
      
         This was removing a quite a good amount of rows from the datset and the prediction accuracy was less.
         
      b) identify key outliers and treat them . 
      
          for eg: 2 BHK rent greater than 50000 doesnt sound real . so treat those values greater than 50000  by replacing with mean value based on location group.
          df.groupby('loc_dist_gp')['avg_2bhk_rent'].mean()
      
 Data cleaning is not limited to above . It can be extended further .
 
 ## Encoding and Scaling
      Following steps is done :
      a) Dummy encoding categorical variables : pandas get_dummies()
      b) scaling the numerical variables : sklearn.preprocessing.StandardScaler()
      
## Model Building .
      
      Following Regression method are tried and XGB regression was showing minimal loss.
      
      a) Lasso Regression
      
      b) Ridge Regression
      
      c) Elastic Net Regression
      
      d) Ensemble - Voting
      
      e) Random Forest Regressor
      
      f) XGB Regression 
      
      Based on different model analysis ,Distance from city with in 3KM,6KM,  4 and 5 BHK room type, total square feet ,number of bathrooms plays a major role in the prediction
