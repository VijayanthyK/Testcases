### Dev Contact
- Baba Pathan
- QA
- Vijayanthy
### Step 1: Group Flag Step
- Updates Field: HasBKGHistory in Production_ED.Contact
- nan
- Logic: Checks if there are mismatches between historical booking flags and actual travel behavior.
- nan
- Dataverse Relevance:  Mapped to col_hasbkghistory column in Contact table.
### Step 2: Backfill Birthday
- Updates Field: Birthday and strBirthdate in Production_ED.Contact
- nan
- Source: Birth_date from Neo2_Prod.Passenger
- nan
- Logic: Backfills birth dates for contact records from Passenger table
- nan
- Dataverse Relevance:  Mapped to Birhday in Contact table. Str_Birthdate column is not required in Dataverse
### Step 3: 18_Agent_Web_Login
- Updates Field: Web_Registered_Date in Production_ED.Contact
- nan
- Logic: Gets registration date from external systems.
- nan
- Dataverse Relevance: Mapped to col_webregistereddate column in Contact table