# Package Name: Update Contacts from Neo (RES-662)
## Description 
This package updates Contact records in Microsoft Dataverse based on updates from the NEO system.
### Step 1: Group Flag Step
- Updates Field: HasBKGHistory in Production_ED.Contact
- Logic: Checks if there are mismatches between historical booking flags and actual travel behavior.
- Dataverse Relevance:  Mapped to col_hasbkghistory column in Contact table.
### Step 2: Backfill Birthday
- Updates Field: Birthday and strBirthdate in Production_ED.Contact
- Source: Birth_date from Neo2_Prod.Passenger
- Logic: Backfills birth dates for contact records from birthday column in Passenger table 
- Dataverse Relevance:  Mapped to Birhday in Contacttable. Str_Birthdate column is not required in Dataverse
### Step 3: 18_Agent_Web_Login
- Updates Field: Web_Registered_Date in Production_ED.Contact
- Logic: Gets registration date from external systems for the PivotalContactId.
- Dataverse Relevance: Mapped to col_webregistereddate column in Contact table

  ### Contact
- Dev  : Baba Pathan
- QA   : Vijayanthy
