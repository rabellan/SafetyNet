# Project 1: Exploratory Data Analysis
### UC Berkeley Data Analytics Bootcamp 2023
#### Title: San Francisco Crime Report (2018 - 2023)
**Co-authors:** Brandon Ingalz, Michael Jardinico, Anushya Mani, Diahann Castellon, Jimmy Nguyen, Roland Abellano  
**Version:** 1.0  
**Date Created:** 11/10/2023

## Description
At SafetyNet Analytics Group, we recognize the prevalent issue of crime in San Francisco, a reality many of us have come to know through various channels such as media reports, word or mouth, and personal experiences, or anecdotes. Our investigation is primarily anchored in a study by "Upgraded Home," which identifies 15 neighborhoods in San Francisco as being the safest. Our objective is to delve deeper into the state of crime in the city by examining geospatial data related to criminal activities. This analysis aims to provide insightful assessments for those considering residency in the area, highlighting neighborhoods with the highest and lowest crime rates. Additionally, we will present trends observed in criminal activities over a span of five years.

## Analysis ##
**SafetyNet's** [San Francisco Crime Report (2018-2023) ](https://github.com/rabellan/SafetyNet/blob/main/Group%203%20Project%20-%20SafetyNet.pdf)

## Installation
Before running the project, ensure the following libraries are installed in your Python environment:

1. Jupyter Notebook
2. matplotlib
3. pandas
4. pathlib
5. Git LFS


## Project Setup
Follow these steps to set up the project environment:

1. Create a new repository on GitHub named `SafetyNet`.
2. Clone the repository to your local machine.
3. Within your local repository, create a following:
    #### IMPORTANT:  The main code file is "RA_Cleaned_CSV_ASMAnalysis_Oct22.ipynb"
    ```
    - RA_Cleaned_CSV_ASMAnalysis_Oct22.ipynb (You can rename it as per your preference).
    - .gitignore 
    ```
4. Download and save the csv dataset in the root directory from the following link. 
   #### IMPORTANT: The file, which is approximately 225MB in size, is stored on the Git LFS server. It is available for download only in plain text format. You can then choose to convert it into a .csv file using Excel or another similar application.
   https://github.com/rabellan/SafetyNet/blob/main/SF_Crime_Data_from_2018_to_Present_CLEANED.csv 

5. Start by loading dependencies and settings inside `RA_Cleaned_CSV_ASMAnalysis_Oct22.ipynb` 
    ```
    import pandas as pd
    from pathlib import Path
    import matplotlib.pyplot as plt
    import scipy.stats as st
    from matplotlib.patches import Polygon
    import requests
    import numpy as np
    import json
    import requests
    ```

6. Set the path to the .csv file 
    ```
    sf_orig_clean = Path("SF_Crime_Data_from_2018_to_Present_CLEANED.csv")
    sf_orig_df = pd.read_csv(sf_orig_clean)
    sf_orig_df.tail()
    ```

7. Count the number of neighborhood based on the San Francisco "Police Department Incident Reports: 2018 to Present" dataset. There should be a total of 41 neighborhoods.

    #### Count the total number of neighborhoods:
    ```
    number_of_hoods = sf_orig_df['Analysis Neighborhood'].nunique()
    print(f"San Francisco, according to \"Police Department Incident Reports: 2018 to Present\" dataset, has {number_of_hoods} neighborhoods")
    ```


8. The dataframe, sf_by_neighborhood_ct, consist of stats grouped by 'Analysis Neighborhood'

    #### Acount the total number of incidents by neighborhood and group by 1 variable
    ```
    sf_by_neighborhood_ct=pd.DataFrame(sf_orig_df.groupby('Analysis Neighborhood').count())
    sf_by_neighborhood_ct
    ```

9. Group the total number of incidents per year
    ```
    sf_by_year_ct= pd.DataFrame(sf_orig_df.groupby('Analysis Neighborhood').count())
    df_sort=sf_by_year_ct.sort_values('Incident Datetime',ascending = True)[['Incident Datetime','Incident Year']][:15]
    df_sort
    ```

10. Count the total number of incidents by neighborhood, by year
    ```
    sf_neigh_yr = pd.DataFrame(sf_orig_df.groupby(['Analysis Neighborhood','Incident Year']).count())
    new= sf_orig_df.sort_values(by = ['Analysis Neighborhood','Incident Year'])
    df_index=sf_neigh_yr.reset_index()
    df=df_index.set_index('Incident Year')
    df    
    ```

11. Group the total number of incidents per year per neighborhood
    ```
    sf_by_year_ct= pd.DataFrame(sf_orig_df.groupby('Analysis Neighborhood').count())
    sf_by_year_ct.sort_values('Incident Datetime',ascending = False)
    sf_by_year_ct
    ```

12. Create a line chart of Least Crime for Incident Year Vs. Total Incidents from 2018 - 2023 
    ```
    count =0
    top_five=[]
    for neigh in df_sort.index:
        
        df[df['Analysis Neighborhood']==neigh].Latitude.plot(figsize=[16,8], 
            title='SF Neighborhoods, Least Crime')
        plt.ylabel('Total incidents')
        plt.legend(df_sort.index,loc='lower right')
        if neigh <= neigh:
            count=count+1
            if count <= 5: 
                top_five.append(neigh)
    print(f'The top five safest neighborhoods (with lowest crime incident count) are:{top_five}')       
    
    ```

13. Create a DataFrame that shows the unique crime code based on the 'Incident Code' and 'Incident Description' column
    ```
    unique_crime_codes = unique_crime_codes = sf_orig_df["Incident Code"].unique()
    unique_crime_description = sf_orig_df["Incident Description"].unique()

    #Create a list of tuples, each will contain crime code and its description
    unique_crime_tp = [(code, desc) for code, desc in zip(unique_crime_codes, unique_crime_description)]

    #Sort the list
    unique_crime_tp_sorted = sorted(unique_crime_tp, key=lambda x: x[0])

    #Unpack the sorted list
    unique_crime_codes_sorted, unique_crime_descrioption_sorted = zip(*unique_crime_tp_sorted)

    unique_codes_sorted = pd.DataFrame({
        "Incident Code": unique_crime_codes_sorted,
        "Incident Description": unique_crime_descrioption_sorted
    
    })
    print(len(unique_codes_sorted))
    unique_codes_sorted.head(50)["Incident Code"].unique()
    unique_crime_description = sf_orig_df["Incident Description"].unique()

    #Create a list of tuples, each will contain crime code and its description
    unique_crime_tp = [(code, desc) for code, desc in zip(unique_crime_codes, unique_crime_description)]

    #Sort the list
    unique_crime_tp_sorted = sorted(unique_crime_tp, key=lambda x: x[0])

    #Unpack the sorted list
    unique_crime_codes_sorted, unique_crime_descrioption_sorted = zip(*unique_crime_tp_sorted)

    unique_codes_sorted = pd.DataFrame({
        "Incident Code": unique_crime_codes_sorted,
        "Incident Description": unique_crime_descrioption_sorted
    
    })
    print(len(unique_codes_sorted))
    unique_codes_sorted.head(50)
    ```

14. Write the result of the sorted codes into a csv file for further analysis
    ```
    unique_codes_sorted.to_csv("unique_codes_sorted.csv", index=False)
    ```

15. Group the keywords in the SF Incident Description as Violent Crime
    ```
    violent_crime_keywords = ["suicide","assault","rape", "homicide", "shooting", "incest",\ 
                              "kidnapping", "death","sexual assault", "manslaughter",\
                              "sodomy","sex","sexual"]

    mask_violent_description = unique_codes_sorted["Incident Description"].str.contains("|".join(violent_crime_keywords), case=False)

    violent_crime_group = unique_codes_sorted[mask_violent_description]

    # Collect the corresponding "SF Incident Code" and save to a variable
    violent_crime_codes = violent_crime_group["Incident Code"].tolist()

    # Display the results:
    print(f"Total number of Violent Crime type: {len(violent_crime_codes)} of 840")
    print("List of Violent Crime by Incident Code")
    print(f"Incident Code: {violent_crime_codes}")

    ```

16. Group the keywords in the SF Incident Description as Property Crime
    ```
    property_crime_keywords = ["robbery", "burglary","theft","larceny", \
                            "access card","carjacking", "extortion","contraband","arson","fire","auto",\
                            "vehicle","lost","vandalism","carjacking","destruction"]

    mask_property_description = unique_codes_sorted["Incident Description"].str.contains("|".join(property_crime_keywords), case=False)

    property_crime_group = unique_codes_sorted[mask_property_description]

    # Collect the corresponding "Incident Code" and save to a variable
    property_crime_codes = property_crime_group["Incident Code"].tolist()

    # Display the results:
    print(f"Total number of Property Crime type: {len(property_crime_codes)}")
    print("List of Property Crime by Incident Code")
    print(f"Incident Code: {property_crime_codes}")

    ```

17. Group the keywords in the SF Incident Description as White Collor Crime
    ```
    white_collar_crime = ["fraud","embezzlement","personation", "impersonation","counterfeit",\
                          "imperson","counterfeiting","falsify","false","permit","forgery",\ "cryptocurrency","fraudulent"]

    mask_white_collar = unique_codes_sorted["Incident Description"].str.contains("|".join(white_collar_crime), case=False)

    white_collar_group = unique_codes_sorted[mask_white_collar]

    # Collect the corresponding "SF Incident Code" and save to a variable
    white_collar_codes = white_collar_group["Incident Code"].tolist()


    # Display the results:
    print(f"Total number of White collar crime type: {len(white_collar_codes)} of 840")
    print("List of White collar crime by Incident Code")
    print(f"Incident Code: {white_collar_codes}")
    ```

18. Group the keywords in the SF Incident Description as Drug and Alcolhol Crime
    ```
    drug_alcohol_crime = ["drug","alcohol","opiates","opium", "methamphetamine","needle",\
                          "barb","ingest","narcotic","marijuana","cocaine","substance",\
                          "hallucinogenic","hallucinogenics", "aerosol","heroin","methadone",\
                          "amphetamine","driving","barbiturates","controlled substance"]

    mask_drug_alcohol = unique_codes_sorted["Incident Description"].str.contains("|".join(drug_alcohol_crime), case=False)

    drug_alcohol_group = unique_codes_sorted[mask_drug_alcohol]

    # Collect the corresponding "SF Incident Code" and save to a variable
    drug_alcohol_codes = drug_alcohol_group["Incident Code"].tolist()
    # Display the results:
    print(f"Total number of Drug & Alcohol crime type: {len(drug_alcohol_codes)} of 840")
    print("List of Drug & Alcohol crime by Incident Code")
    print(f"Incident Code: {drug_alcohol_codes}")

    ```


19. Group the keywords in the SF Incident Description as Hate Crime
    ```
    hate_crime = ["threat","hate","swear", "peace", "riot","conspiracy","place of worship"]

    mask_hate_crime = unique_codes_sorted["Incident Description"].str.contains("|".join(hate_crime), case=False)

    hate_crime_group = unique_codes_sorted[mask_hate_crime]

    # Collect the corresponding "SF Incident Code" and save to a variable
    hate_crime_codes = hate_crime_group["Incident Code"].tolist()

    # Display the results:
    print(f"Total number of Hate crime type: {len(hate_crime_codes)} of 840")
    print("List of Hate crime by Incident Code")
    print(f"Incident Code: {hate_crime_codes}")
    ```

20. Group the keywords in the SF Incident Description as Quality of Life Disruptions
    ```
    quality_life_disrupt = ["biting","disrupt","swear", "graffiti", "trespass","vandal","mischief","animal","loit",\
                            "unusual","against","disorderly","traffic","warrant","weapon","malicious",\
                            "suspicious","non-criminal","missing","collision","investigation",\
                            "mental","harassing","detention","battery","conspiracy","eavesdropping",\ "prostitution", "interfering","fireworks","pimping","evading a police","disturbing","traffic","racing", "knife","permit","violation",\
                            "riot","bathroom hole","resisting","infractions","permit violation",\
                            "throwing injurious substance","disturbance"]

    mask_quality_life = unique_codes_sorted["Incident Description"].str.contains("|".join(quality_life_disrupt), case=False)

    quality_life_group = unique_codes_sorted[mask_quality_life]

    # Collect the corresponding "SF Incident Code" and save to a variable
    quality_life_codes = quality_life_group["Incident Code"].tolist()

    # Display the results:
    print(f"Total number of Quality of life disruptions: {len(quality_life_codes)} of 840")
    print("List of Quality of life disruptions by Incident Code")
    print(f"Incident Code: {quality_life_codes}")

    ```

21. Categorize the 840 unique incidents types into six (6) New Incident Code in the following:
    - 101 - Violent Crime
    - 102 - Property Crime
    - 103 - White Collar Crime
    - 104 - Drug & Alcohol Crime
    - 105 - Hate Crime
    - 106 - Quality-of-life Crime

22. Based on above code assignment create a column 'New Incident ID' in the DataFrame
    ```
    # Initialize "New Incident ID" with original values
    sf_orig_df["New Incident Code"] = sf_orig_df['Incident Code']

    # Assign 101 to Violent Crime codes and add "New Incident ID" column
    mask_violent = sf_orig_df['Incident Code'].isin(violent_crime_codes)
    sf_orig_df.loc[mask_violent, "New Incident Code"] = 101  
    
    # This will locate all Incident Codes under violent crime codes list and assign them as 101 in the new column called "New Incident ID"

    # Assign 102 to Property Crime codes 
    mask_property = sf_orig_df['Incident Code'].isin(property_crime_codes)
    sf_orig_df.loc[mask_property & ~mask_violent, "New Incident Code"] = 102  # Exclude already labeled as violent crime codes

    # Assign 103 to White Collar Crime codes
    mask_white_collar = sf_orig_df['Incident Code'].isin(white_collar_codes)
    sf_orig_df.loc[mask_white_collar & ~mask_violent & ~mask_property, "New Incident Code"] = 103  
    #Exclude already labeled as violent crime or property crime codes

    # Assign 104 to Drug & Alcohol Crime codes
    mask_drug_alcohol = sf_orig_df['Incident Code'].isin(drug_alcohol_codes)
    sf_orig_df.loc[mask_drug_alcohol & ~mask_violent & ~mask_property & ~mask_white_collar, "New Incident Code"] = 104   #Exclude already labeled as violent crime, property crime & white collar crime codes

    # Assign 105 to Hate Crime codes
    mask_hate = sf_orig_df['Incident Code'].isin(hate_crime_codes)
    sf_orig_df.loc[mask_hate & ~mask_violent & ~mask_property & ~mask_white_collar & ~mask_drug_alcohol, "New Incident Code"] = 105   #Exclude already labeled as violent crime, property crime, white collar crime & drug & alcohol crime codes

    # Assign 106 to Quality of life Crime codes
    mask_quality_life = sf_orig_df['Incident Code'].isin(quality_life_codes)
    sf_orig_df.loc[mask_quality_life & ~mask_violent & ~mask_property & ~mask_white_collar & ~mask_drug_alcohol & ~mask_hate, "New Incident Code"] = 106   #Exclude already labeled as violent crime, property crime, white collar crime, drug & alcohol crime and hate crime codes

    display(sf_orig_df.head(50))
    ```

23. Create a DataFrame that shows ['New Incident Code', 'Incident Datetime', 'Incident Date', 
    'Incident Time', 'Incident Year', 'Analysis Neighborhood']
    ```
    sf_orig_new= sf_orig_df[['New Incident Code','Incident Datetime','Incident Date','Incident Time','Incident Year','Analysis Neighborhood']]

    # Display
    sf_orig_new
    ```

24. Create and display a DataFrame showing the 'New Incident Code' column
    ```
    newcode_clean= sf_orig_new.loc[(sf_orig_new['New Incident Code']<=106)]
    newcode_clean.head(50)
    ```

25. Group by Neighborhood, year, incident type, incident type 
    ```
    newcode_clean_org = pd.DataFrame(newcode_clean.groupby(['Analysis Neighborhood','Incident Year','New Incident Code']).count())
    newcode_clean_org= newcode_clean_org.sort_values(by = ['Analysis Neighborhood','Incident Year'])

    newcode_clean_org.head(50)

    newcode_clean_org.reset_index()
    ```

26. Create a graph for the number or Incident by Crime Type vs Month in San Francisco between 2018 - 2023
    ```
    newcode_clean = newcode_clean.copy()
    newcode_clean['Incident Datetime'] = pd.to_datetime(newcode_clean['Incident Datetime'])
    newcode_clean['Month'] = newcode_clean['Incident Datetime'].dt.month

    # Dictionary
    crime_mapping = {
        101: 'Violent Crime',
        102: 'Property Crime',
        103: 'White Collar Crime',
        104: 'Drug & Alcohol Crime',
        105: 'Hate Crime',
        106: 'Quality-of-Life Crime'
    }

    # Create a separate line for each crime type
    plt.figure(figsize=(10, 6))

    for code, description in crime_mapping.items():
        # Filter rows for the specific crime code
        filtered_data = newcode_clean[newcode_clean['New Incident Code'] == code]
        
        # Group by 'Month' and count the occurrences
        incident_counts = filtered_data['Month'].value_counts().sort_index()
        
        # Plot the line for the crime type
        plt.plot(incident_counts.index, incident_counts.values, label=description, marker='o', linestyle='-')

    plt.title('Incidents by Crime Type and Month in San Francisco Between 2018-2023')
    plt.xlabel('Month')
    plt.ylabel('Count')
    plt.grid(True)

    months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
    plt.xticks(incident_counts.index, [months[month - 1] for month in incident_counts.index])
    plt.legend(loc='upper right')
    plt.show()
    
    ```  

27. Create a chart based on the number of incidents of the top two crimes (Violent Crime & Property Crime) in San Francisco between 2018 - 2023
    ```
    # Filtering the data for violent crimes (New Incident Code 101) and property crimes (New Incident Code 102)
    violent_crimes = newcode_clean[newcode_clean['New Incident Code'] == 101]
    property_crimes = newcode_clean[newcode_clean['New Incident Code'] == 102]

    # Grouping by neighborhoods and counting the occurrences for each type of crime
    violent_counts = violent_crimes['Analysis Neighborhood'].value_counts()
    property_counts = property_crimes['Analysis Neighborhood'].value_counts()

    # Combining the counts of both types of crime
    combined_counts = violent_counts.add(property_counts, fill_value=0)

    # Sorting neighborhoods by the sum of Violent Crimes and Property Crimes
    sorted_neighborhoods = combined_counts.sort_values(ascending=False)

    # Creating a horizontal bar chart with two bars for each neighborhood
    plt.figure(figsize=(10, 8))
    bar_width = 0.35
    index = range(len(sorted_neighborhoods))

    plt.barh(index, violent_counts[sorted_neighborhoods.index], bar_width, label='Violent Crimes', color='teal')
    plt.barh([i + bar_width for i in index], property_counts[sorted_neighborhoods.index], bar_width, label='Property Crimes', color='salmon')

    plt.yticks([i + bar_width / 2 for i in index], sorted_neighborhoods.index)
    plt.xlabel('Number of Incidents')
    plt.title('Violent and Property Crimes by Neighborhood in SF Between 2018-2023')
    plt.legend()
    plt.margins(y=0.01)
    plt.show()
    ``` 

28. Create a bar chart for the number of incidents by Crime Type of each Neighborhood
```
import pandas as pd
import matplotlib.pyplot as plt

# Dictionary
crime_mapping = {
    101: 'Violent Crime',
    102: 'Property Crime',
    103: 'White Collar Crime',
    104: 'Drug & Alcohol Crime',
    105: 'Hate Crime',
    106: 'Quality-of-Life Crime'
}

# Create a new column 'Crime Type' by mapping the code numbers to descriptions
newcode_clean['Crime Type'] = newcode_clean['New Incident Code'].map(crime_mapping)

# Group by neighborhoods and count the occurrences for each crime type
neighborhood_counts = newcode_clean.groupby(['Analysis Neighborhood', 'Crime Type']).size().unstack(fill_value=0)

# Calculate the total count of incidents (sum of codes 101-106) for each neighborhood
neighborhood_counts['Total'] = neighborhood_counts.sum(axis=1)

# Sort neighborhoods by the total count of incidents
sorted_neighborhoods = neighborhood_counts['Total'].sort_values(ascending=False)

# Create a horizontal stacked bar chart for each neighborhood
plt.figure(figsize=(10, 18))
index = range(len(sorted_neighborhoods))
colors = ['teal', 'hotpink', 'darkgreen', 'darkred', 'skyblue', 'purple']
bottom = [0] * len(sorted_neighborhoods)

for i, crime_type in enumerate(['Violent Crime', 'Property Crime', 'White Collar Crime', 'Drug & Alcohol Crime', 'Hate Crime', 'Quality-of-Life Crime']):
    counts = neighborhood_counts[crime_type][sorted_neighborhoods.index]
    plt.barh(index, counts, label=crime_type, left=bottom, color=colors[i])
    bottom = [x + y for x, y in zip(bottom, counts)]

plt.yticks(index, sorted_neighborhoods.index)
plt.xlabel('Number of Incidents')
plt.title('Incidents by Crime Type and Neighborhood')
plt.legend()
plt.margins(y=0.01)
plt.show()

```

29. Create a chart based on the top ten safest neighborhoods by crime type in San Francisco between 2018 - 2023
    ```
    # Filter for the top 10 safest neighborhoods
    top_10_safest_neighborhoods = sorted_neighborhoods.tail(10)

    # Define crime types and colors
    crime_types = ['Violent Crime', 'Property Crime', 'White Collar Crime', 'Drug & Alcohol Crime', 'Hate Crime', 'Quality-of-Life Crime']
    colors = ['teal', 'hotpink', 'darkgreen', 'darkred', 'skyblue', 'purple']

    # Creating a horizontal stacked bar chart for the top 10 safest neighborhoods
    plt.figure(figsize=(10, 10))
    index = range(len(top_10_safest_neighborhoods))
    bottom = [0] * len(top_10_safest_neighborhoods)

    # Iterate through each crime type and add bars to the chart
    for i in range(len(crime_types)):
        crime_type = crime_types[i]
        counts = neighborhood_counts[crime_type][top_10_safest_neighborhoods.index]
        plt.barh(index, counts, label=crime_type, left=bottom, color=colors[i])
        bottom = [x + y for x, y in zip(bottom, counts)]

    plt.yticks(index, top_10_safest_neighborhoods.index)
    plt.xlabel('Number of Incidents')
    plt.title('Top 10 Safest Neighborhoods by Crime Type in SF Between 2018-2023')
    plt.legend()
    plt.margins(y=0.01)
    plt.show()

    ```  


30. Create a chart based on the top 10 dangerous neighborhoods in San Francisco between 2018 - 2023

    ```
    # Filter for the top 10 dangerous neighborhoods
    top_10_dangerous_neighborhoods = sorted_neighborhoods.head(10)

    # Define crime types and colors
    crime_types = ['Violent Crime', 'Property Crime', 'White Collar Crime', 'Drug & Alcohol Crime', 'Hate Crime', 'Quality-of-Life Crime']
    colors = ['teal', 'hotpink', 'darkgreen', 'darkred', 'skyblue', 'purple']

    # Creating a horizontal stacked bar chart for the top 10 dangerous neighborhoods
    plt.figure(figsize=(10, 10))
    index = range(len(top_10_dangerous_neighborhoods))
    bottom = [0] * len(top_10_dangerous_neighborhoods)

    # Iterate through each crime type and add bars to the chart
    for i in range(len(crime_types)):
        crime_type = crime_types[i]
        counts = neighborhood_counts[crime_type][top_10_dangerous_neighborhoods.index]
        plt.barh(index, counts, label=crime_type, left=bottom, color=colors[i])
        bottom = [x + y for x, y in zip(bottom, counts)]

    plt.yticks(index, top_10_dangerous_neighborhoods.index)
    plt.xlabel('Number of Incidents')
    plt.title('Top 10 Dangerous Neighborhoods by Crime Type in SF Between 2018-2023')
    plt.legend()
    plt.margins(y=0.01)
    plt.show()

    ```

31. Exported data frame to excel to conduct analysis by crime type, per neighborhood, over time. Analyzed top safest and least safest areas per article against findings from our data set.
    Excel files titled : Excel_Crime_Top5.xlsm and Excel_Crime_Worst5.xlsm.
    
32. Micro analysis of McLaren Park/Amazon-Crocker Neighborhood: the "safest" neighborhood in San Francisco (Roland Abellano's analysis of McLaren Neighborhood)

    ```
    Based on the article, "The 15 Safest Neighborhoods In San Francisco", by Jessica Stone in the website, Upgraded Homes, Crocker-Amazon was one of the 15 safest neighborhood on San Francisco. As a local San Franciscan living only a mile east of Crocker-Amazon neighborhood, I have a personal stake at the accuracy of what Ms. Stone's article is claiming. I would like to find out if District 10, where Amazon-Crocker and my local neighborhood, Bayview, have significant crime rate rating difference in comparison to the rest of San Francisco.

    Website link: https://upgradedhome.com/safest-neighborhoods-in-san-francisco/
       
    ```
    #### Null Hypothesis: Crocker Amazon is considered the safest neighborhood in the city.

    ```
    # Extract the unique values from the 'Analysis Neighborhood' column
    unique_neighborhoods = sf_orig_df['Analysis Neighborhood'].unique()

    # Create a new DataFrame with the unique neighborhoods
    unique_neighborhoods_df = pd.DataFrame(unique_neighborhoods, columns=['Unique Neighborhoods'])

    unique_neighborhoods_df   
        
    ```

33. Show the crime rate of the 40 neighborhoods in San Francisco for the Year 2020
    ```
    # Filter data for 'Incident Year' 2020
    crimes_2020 = sf_orig_df[sf_orig_df['Incident Year'] == 2020]

    # Group by 'Analysis Neighborhood' and count unique 'Incident ID'
    neighborhood_counts = crimes_2020.groupby('Analysis Neighborhood')['Incident ID'].nunique().sort_values()

    # Print or use the results as needed
    print(neighborhood_counts)

    # Print analysis
    print(f"\nThe neighborhood with the LOWEST CRIME INCIDENT in the year 2020 is {neighborhood_counts.idxmin()}")
    ```

34. Show a line graph indicating the number of crime incidents in McLaren Park/Crocker-Amazon for Year 2020. Spoiler Alert: Monday seems to be the day when the most crimes were committed in McLaren Park/Crocker-Amazon for the year 2020

    ```
    # Analyze data to show what day of the week is crime commited the most in McLAren Park/Cro
    # Filter data for McLaren Park and the year 2020
    mclaren_park_2020_data = sf_orig_df[(sf_orig_df['Analysis Neighborhood'] == 'McLaren Park') & (sf_orig_df['Incident Year'] == 2020)]

    # Group data by day of the week and calculate the total number of crimes
    crimes_per_day_2020 = mclaren_park_2020_data['Incident Day of Week'].value_counts().sort_index()

    # Define the order of days for proper sorting in the line graph
    days_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

    # Create a line graph
    plt.figure(figsize=(10, 6))
    crimes_per_day_2020[days_order].plot(marker='o', linestyle='-', color='b')
    plt.title('Total Number of Crimes in McLaren Park per Day of the Week (2020)')
    plt.xlabel('Day of the Week')
    plt.ylabel('Number of Crimes')
    plt.grid(True)
    plt.show()
    ```

35. For the sake of comparison, show the same week-long crime trend for McLaren Park/Crocker Amazon for a period of 5 years (2018-2023). Spoiler Alert: From year 2018 to year 2023, Friday seems to be the day when most crimes were committed in McLaren Park/Crocker-Amazon neighborhood

    ```
    # Filter data for McLaren Park
    mclaren_park_data_2018_2023 = sf_orig_df[sf_orig_df['Analysis Neighborhood'] == 'McLaren Park']

    # Group data by day of the week and calculate the total number of crimes
    crimes_per_day_2018_2023 = mclaren_park_data_2018_2023['Incident Day of Week'].value_counts().sort_index()

    # Define the order of days for proper sorting in the line graph
    days_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

    # Create a line graph
    plt.figure(figsize=(10, 6))
    crimes_per_day_2018_2023[days_order].plot(marker='o', linestyle='-', color='b')
    plt.title('Total Number of Crimes in McLaren Park per Day of the Week: 2018-2023')
    plt.xlabel('Day of the Week')
    plt.ylabel('Number of Crimes')
    plt.grid(True)
    plt.show()
    ```

36. Show the types of crime committed in McLaren Park/Crocker-Amazon from year 2018 to 2023. Spoiler Alert: Larceny-Theft is the most committed crime in McLaren Park/Crocker-Amazon from 2018-2023

```
mclaren_park_data_incident_types = sf_orig_df[(sf_orig_df['Analysis Neighborhood'] == 'McLaren Park') & (sf_orig_df['Incident Year'].between(2018, 2023))]

# Count the occurrences of each incident category
incident_category_counts = mclaren_park_data_incident_types['Incident Category'].value_counts()

# Print the result
print("Incident Categories and Counts in McLaren Park/Crocker-Amazon from 2018 to 2023:")
print(incident_category_counts)

# Print total incident count
total_incident_count_mclaren = mclaren_park_data_incident_types.shape[0]
print(f"\nTotal Incident Count in McLaren Park/Crocker-Amazon from 2018 to 2023: {total_incident_count_mclaren}")

# Total incident count for all of San Francisco
total_sf_incident_count_sf = sf_orig_df.shape[0]
print(f"\nTotal Incident Count in San Francisco from 2018 to 2023: {total_sf_incident_count_sf}")

# Calculate McLaren Park incident percentage over SF incidents from 2018 to 2023
# and compare it to the rest of San Francisco

mclaren_percentage_2018_2020 = (total_incident_count_mclaren / total_sf_incident_count_sf) * 100

print(f"The percentage of Mclaren Park/Crocker-Amazon incidents in comparison to the entire city of San Francisco from 2018 to 2023 is {mclaren_percentage_2018_2020})
```

37. Filter the incidents for McLaren Park/Crocker-Amazon and compare to the rest of San Francisco
    ```
    # Filter incidents for McLaren Park/Crocker-Amazon
    mclaren_park_data = sf_orig_df[(sf_orig_df['Analysis Neighborhood'] == 'McLaren Park') & (sf_orig_df['Incident Year'].between(2018, 2023))]

    # Filter incidents for the rest of San Francisco
    rest_of_sf_data = sf_orig_df[(sf_orig_df['Analysis Neighborhood'] != 'McLaren Park') & (sf_orig_df['Incident Year'].between(2018, 2023))]

    # Calculate percentages
    total_sf_incidents = sf_orig_df[(sf_orig_df['Incident Year'].between(2018, 2023))].shape[0]
    percentage_mclaren_park = (mclaren_park_data.shape[0] / total_sf_incidents) * 100
    percentage_rest_of_sf = (rest_of_sf_data.shape[0] / total_sf_incidents) * 100

    # Print the results
    print(f"Percentage of Incidents in McLaren Park/Crocker-Amazon from 2018 to 2023: {percentage_mclaren_park:.2f}%")
    print(f"Percentage of Incidents in Rest of SF from 2018 to 2023: {percentage_rest_of_sf:.2f}%")

    ```

38. To show a bit of comparison with McLaern Park/Crocker Amazon's crime rate for the year 2020, show the neighborhood that has the most crime committed in San Francisco in the same year

    ```
    # Filter incidents for the year 2020
    sf_2020_data = sf_orig_df[sf_orig_df['Incident Year'] == 2020]

    # Group by neighborhood and count incidents
    neighborhood_crime_count = sf_2020_data.groupby('Analysis Neighborhood').size().reset_index(name='Incident Count')

    # Find the neighborhood with the highest crime count
    highest_crime_neighborhood = neighborhood_crime_count.loc[neighborhood_crime_count['Incident Count'].idxmax()]

    # Print the result
    print(f"The neighborhood with the highest crime rate in 2020 is: {highest_crime_neighborhood['Analysis Neighborhood']}")
    print(f"Total incidents in {highest_crime_neighborhood['Analysis Neighborhood']} in 2020: {highest_crime_neighborhood['Incident Count']}")
    ```

39. Based on the DataSF dataset, the Tenderloin, a neighborhood in the heart of San Francisco, considered to be the grittiest place in the enture city, is the neighrhood with the most crime in the Year 2020
    ```
    # Filter incidents for the year 2020
    sf_2020_data = sf_orig_df[sf_orig_df['Incident Year'] == 2020]

    # Group by neighborhood and count incidents
    neighborhood_crime_count = sf_2020_data.groupby('Analysis Neighborhood').size().reset_index(name='Incident Count')

    # Find the neighborhood with the highest crime count
    lowest_crime_neighborhood = neighborhood_crime_count.loc[neighborhood_crime_count['Incident Count'].idxmin()]

    # Print the result
    print(f"The neighborhood with the highest crime rate in 2020 is: {lowest_crime_neighborhood['Analysis Neighborhood']}")
    print(f"Total incidents in {lowest_crime_neighborhood['Analysis Neighborhood']} in 2020: {lowest_crime_neighborhood['Incident Count']})

    ```

40. Create a line chart that shows how McLaren Park/Crocker-Amazon's crime rate stacks up against the Tenderloin, the crittiest part of San Francisco, and two of the most expensive neighborhoods in the city, Marina District, and Pacific Heights
    ```
    # Group data by "Incident Year" and "Analysis Neighborhood"
    grouped_data = sf_orig_df.groupby(['Incident Year', 'Analysis Neighborhood']).size().reset_index(name='Incident Count')

    # Filter data for selected neighborhoods
    selected_neighborhoods = ['McLaren Park', 'Tenderloin', 'Marina', 'Pacific Heights', ]
    filtered_data = grouped_data[grouped_data['Analysis Neighborhood'].isin(selected_neighborhoods)]

    # Pivot data for plotting
    pivot_data = filtered_data.pivot(index='Incident Year', columns='Analysis Neighborhood', values='Incident Count')

    # Plot the line graph
    pivot_data.plot.line(marker='o', figsize=(10, 6))
    plt.title('Crime Incidents from 2018 to 2023 - McLaren Park vs Marina vs Pacific Heights')
    plt.xlabel('Incident Year')
    plt.ylabel('Incident Count')
    plt.legend(title='Neighborhood')
    plt.grid(True)
    plt.show()
    ```
42. Export the clean CSV file "SF_Crime_Data_from_2018_to_Present_CLEANED.csv" and upload into Tableau. Begin my creating a calculated field to incorporate the 6 geneeral levels of crime as previously defined:'Violent Crime', 'Property Crime', 'White Collar Crime', 'Drug & Alcohol Crime', 'Hate Crime', and 'Quality-of-Life Crime'. Create a calulcated field that defines "day" vs. "night". With these two calulcated fields in place, we can use the crime levels and day and night as filters for our visualizations. A dashboard is constructed with a series of three pie charts, one for day to night crime distribution, one for day of the week distribution, and one for the 6 categories of crime. Filters for year, community, day vs. night, day of the week and type of crime were used to filter the 5 visualizations below, allowing the user to quickly see the distribution of crime for any of those many factors alone or in combination. 


*** END OF CODE ***
