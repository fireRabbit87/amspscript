## Unsubscribe from Publication List

If there is only one BU for a client, but different companies, then it's best to use a publication list to segment out the data. Here's how to unsubscribe a customer/record using the publication list method.

First step is to find the customer within the the publication list.

```html
/*LOOP THROUGH PUBLICATION LIST*/
SET @clientID = CREATEOBJECT("ClientID")
SETOBJECTPROPERTY(@clientID, "ID", "[Your MID]")

SET @rr = CREATEOBJECT("RetrieveRequest")
SETOBJECTPROPERTY(@rr,"ObjectType", "ListSubscriber")
ADDOBJECTARRAYITEM(@rr,"Properties", "ListID")
ADDOBJECTARRAYITEM(@rr,"Properties", "Status")

SET @sfp1 = CREATEOBJECT("SimpleFilterPart")
SETOBJECTPROPERTY(@sfp1, "Property", "SubscriberKey") 
SETOBJECTPROPERTY(@sfp1, "SimpleOperator", "equals")
ADDOBJECTARRAYITEM(@sfp1, "Value", @SubscriberKey)

SET @sfp2 = CREATEOBJECT("SimpleFilterPart")
SETOBJECTPROPERTY(@sfp2, "Property", "ListID")
SETOBJECTPROPERTY(@sfp2, "SimpleOperator", "equals")
ADDOBJECTARRAYITEM(@sfp2, "Value", "[Your Publication List ID]")

SET @cfp = CREATEOBJECT("ComplexFilterPart")
SETOBJECTPROPERTY(@cfp, "LeftOperand", @sfp1)
SETOBJECTPROPERTY(@cfp, "LogicalOperator", "AND")
SETOBJECTPROPERTY(@cfp, "RightOperand", @sfp2)

SETOBJECTPROPERTY(@rr, "Filter", @cfp)

SET @ListSubObj = INVOKERETRIEVE(@rr, @rrStatus, @rrRequestID)
```
The `@listSubObj` should tell if you the customer exists (they should!) within your publication list. After this, it's just a simple if statement.

```html
IF ROWCOUNT(@ListSubObj) > 0 THEN

  SET @sub = CREATEOBJECT("Subscriber")
  SETOBJECTPROPERTY(@sub, "EmailAddress", @Email)
  SETOBJECTPROPERTY(@sub, "SubscriberKey", @SubscriberKey)

  SET @List = CREATEOBJECT("SubscriberList")
  SETOBJECTPROPERTY(@List, "ID", "[Your Publication List ID]")
  SETOBJECTPROPERTY(@List, "Status", "Unsubscribed")
  ADDOBJECTARRAYITEM(@sub, "Lists", @List)

  SET @options = CREATEOBJECT("UpdateOptions")
  SET @save = CREATEOBJECT("SaveOption")
  SETOBJECTPROPERTY(@save, "SaveAction", "UpdateAdd")
  SETOBJECTPROPERTY(@save, "PropertyName", "*")
  ADDOBJECTARRAYITEM(@options, "SaveOptions", @save)

  SET @update_sub = INVOKEUPDATE(@sub, @update_sub_status, @update_sub_errorcode, @options)
  
 ENDIF
 ```
 
 If you want to update a data extension you can do this:
 
```html
IF ROWCOUNT(@ListSubObj) > 0 THEN

  SET @sub = CREATEOBJECT("Subscriber")
  SETOBJECTPROPERTY(@sub, "EmailAddress", @Email)
  SETOBJECTPROPERTY(@sub, "SubscriberKey", @SubscriberKey)

  SET @List = CREATEOBJECT("SubscriberList")
  SETOBJECTPROPERTY(@List, "ID", "[Your Publication List ID]")
  SETOBJECTPROPERTY(@List, "Status", "Unsubscribed")
  ADDOBJECTARRAYITEM(@sub, "Lists", @List)

  SET @options = CREATEOBJECT("UpdateOptions")
  SET @save = CREATEOBJECT("SaveOption")
  SETOBJECTPROPERTY(@save, "SaveAction", "UpdateAdd")
  SETOBJECTPROPERTY(@save, "PropertyName", "*")
  ADDOBJECTARRAYITEM(@options, "SaveOptions", @save)

  SET @update_sub = INVOKEUPDATE(@sub, @update_sub_status, @update_sub_errorcode, @options)
  IF @update_sub == "OK" THEN
    UPSERTDATA("DE_NAME", 1, "SubscriberKey", @SubscriberKey, "LastModifiedDate", SYSTEMDATETOLOCALDATE(NOW()))
    REDIRECT(CLOUDPAGESURL(XXX, "s", @SubscriberKey, "message", "success"))
  ENDIF
  
 ENDIF
 ```
