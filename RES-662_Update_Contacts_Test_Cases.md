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
## Contributors

- **Developer**: Baba Pathan  
- **QA**: Vijayanthy

# Testing Approach 

| **Area**                          | **Approach**                                                                                                                                     |
|----------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Trigger Mechanism                | Manipulate data in source systems (Neo Passenger) to initiate SSIS                                                    |
| Package Execution                | Manually trigger SSIS job in test environment                                                                         |
| Database Testing                 | Use SQL queries to compare before and after states in Production_ED.Contact and Neo2_Prod.Passenger                   |
| Dataverse Validation             | Query Dataverse in Microsoft SSMS to confirm fields are/aren’t updated in Contact entity                              |
| API Verification for troubleshooting | Verify if CRM Wrapper API is (or isn't) triggered using API monitoring tool Datadog if there is no update to Dataverse                          |
| Switch Verification              | Set SSIS_CRMAPI config value to Y/N and SSIS_Pivotal config value to Y/N to verify the updates                                                  |
| Source                           | `[172.2.16.70].Neo2_Prod.Passenger`                                                                                                              |
| Target                           | `collette-dev.crm.dynamics.com.contact`<br>`[PIV16SQLTEST].Production_ED.contact`                                                              |
| API Version                      | V2         

# Test Cases 

| **Test Case #** | **Test Case Description** | **Pre-requisite** | **Test Steps** | **Test Data** | **Expected Result** | **Actual Result** | **Status** | **Notes** |
|-----------------|---------------------------|--------------------|----------------|----------------|----------------------|-------------------|------------|-----------|
| TC-662-01   | Verify that the Package execution is successful | - | 1. Run SSIS package | - | Package should be executed successfully with no errors | Package execution is success | Pass | - |
| TC-662-02 | Verify that Group Flag (HasBKGHistory) is updated in Production_ED.Contact table and Dataverse | Contact exists in both Production_ED.Contact and Dataverse.Contact with HasBKGHistory = 1 | 1. Run package<br>2. Validate Has_Bkg_History in Production_ED.Contact<br>3. Validate col_hasbkgHistory in Dataverse.contact | `0x00000000000727DA` | Package updates Has_Bkg_History to 0 in Production_ED and Dataverse | Has_Bkg_History in Production_ED.Contact updated to 0, so is col_hasbkgHistory in Dataverse.contact | Pass | Execute query to update Has_Bkg_History of a contact to 1 |
| TC-662-03 | Verify that Backfill Birthday from Passenger to Production_ED.Contact table | Neo2_Prod.Passenger has valid Birth_date and Contact table has no data in Birthday | 1. Run package<br>2. Validate Birthday, strBirthdate in Production_ED.Contact<br>3. Validate Birthday in Dataverse | `0x020000000009D6` | Birthday in Production_ED.Contact and Birthday in Dataverse.Contact should be updated | Birthday in Production_ED.Contact is updated and Birthday field in Dataverse.Contact is updated | Pass | To verify this test case, a contact is picked which exists in both Dataverse and Pivotal Production with Birthday null. Add booking to the contact and modify Birthday in Passenger screen. |
| TC-662-04 | Verify that the Web_Registered_Date is updated in Production_ED.Contact table and Dataverse | External User table in Neo has a record for a contact and the contact should exist in Dataverse | 1. Run package<br>2. Validate Web_Registered_Date in Production_ED.Contact<br>3. Validate col_webregistereddate in Dataverse.contact | `0x010D90000000424`<br>`0x00000000006CFA06`<br>`0x0000000006CC18C`<br>`0x0000000006CD204` | Package updates Web_Registered_Date for the contact with the Neo2_Prod.External_Use t.create_date in Production_ED.Contact and col_webregistereddate in Dataverse.contact | Web_Registered_Date in Production_ED.Contact updated and so is col_webregistereddate in Dataverse.contact | Pass | col_webregistereddate updated in contact table for the four contact records |
| TC-662-05 | Verify timestamp and updated_by fields in the contact table are modified correctly | - | 1. Run SSIS package |  `0x00000000000727DA`<br>`0x020000000009D6`<br>`0x010D90000000424`<br>`0x00000000006CFA06`<br>`0x0000000006CC18C`<br>`0x0000000006CD204` | Updated records should reflect new timestamp and user | Updated records reflect new timestamp and user | Pass | - |
| TC-662-06 | Verify no updates are made to Birthday column in contact table in Dataverse when SSIS_CRM_API config value is set to 'N' | Neo2_Prod.Passenger has valid Birth_date and Contact table has no data in Birthday in Production_ED and Dataverse<br>Neo2_Prod.External_Use has a record for Contact but Web_Registered_Date is null | 1. Run package<br>2. Validate Production_ED.Contact<br>3. Validate Dataverse | - | Birthday and Web_Registered_Date columns in Production_ED.Contact table updated but no update to Birthday and col_webregistereddate columns in Dataverse.contact | As expected | Pass | - |
| TC-662-07 | Verify no updates are made to Birthday column in contact table in Production_ED when SSIS_CRM_API config value is set to 'Y' | Neo2_Prod.Passenger has valid Birth_date and Contact table has no data in Birthday in Production_ED and Dataverse<br>Neo2_Prod.External_Use has a record for Contact but Web_Registered_Date is null | 1. Run package<br>2. Validate Production_ED.Contact<br>3. Validate Dataverse | - | Birthday and Web_Registered_Date columns in Production_ED.Contact table not updated but col_webregistereddate in Dataverse.Contact was updated | Birthday in Production_ED is not updated<br>Birthday in Dynamics is updated | Pass | - |
|


