## Format Date in CRM

### Part 1 : Retrieve the Customer from the Publication List
```html
%%[
  SET @subkey = _subscriberkey
  SET @campName = "Black Friday 2025"
  SET @activationDate = FORAMAT(NOW(), "yyyy-MM-ddThh:mm:ssZ")
  SET @createEvent = CreateSalesforceObject("Customer_Event__c", 3, "ContactName__c", @subkey, "Name__c", @campName, "Date_Opted_In__c", @activationDate)
]%%
```
