# Project 1: Exploratory Data Analysis
### UC Berkeley Data Analytics Bootcamp 2023

**Co-authors:** Brandon Ingalz, Michael Jardinico, Anushya Mani, Diahann Castellon, Jimmy Nguyen, Roland Abellano  
**Version:** 1.0  
**Date Created:** 11/10/2023

## Description
At SafetyNet Analytics Group, we recognize the prevalent issue of crime in San Francisco, a reality many of us have come to know through various channels such as media reports, word or mouth, and personal experiences, or anecdotes. Our investigation is primarily anchored in a study by "Upgraded Home," which identifies 15 neighborhoods in San Francisco as being the safest. Our objective is to delve deeper into the state of crime in the city by examining geospatial data related to criminal activities. This analysis aims to provide insightful assessments for those considering residency in the area, highlighting neighborhoods with the highest and lowest crime rates. Additionally, we will present trends observed in criminal activities over a span of five years..


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
    number_of_hoods = sf_orig_df['Analysis Neighborhood'].nunique()
    print(f"San Francisco, according to \"Police Department Incident Reports: 2018 to Present\" dataset, has {number_of_hoods} neighborhoods)


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
    unique_codes_sorted.to_csv("unique_codes_sorted.csv", index=False)


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

21. Incident Code Assignment:
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

24. 