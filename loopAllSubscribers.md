## Loop All Subscribers

If you need to loop throught the status of a customer on a landing page, use the code below. You should not use the data view because the customer may open an old email which is more than 6 months ago and therefore by using the data view, it won't be able to see the customer's status.

```html
/*CHECK OF SUBSCRIBER STATUS FROM ALL SUBS*/
SET @rr = CREATEOBJECT("RetrieveRequest")
SETOBJECTPROPERTY(@rr, "ObjectType", "Subscriber")
ADDOBJECTARRAYITEM(@rr, "Properties", "Status")
SET @sfp = CREATEOBJECT("SimpleFilterPart")
SETOBJECTPROPERTY(@sfp, "Property", "SubscriberKey")
SETOBJECTPROPERTY(@sfp, "SimpleOperator", "equals")
ADDOBJECTARRAYITEM(@sfp, "Value", @subkey)
SETOBJECTPROPERTY(@rr, "Filter", @sfp)
SET @subobj = INVOKERETRIEVE(@rr, @rrStatus, @rrRequestID)

IF ROWCOUNT(@subobj) > 0 THEN

  SET @row = ROW(@subobj, 1)
  SET @status = FIELD(@row,"Status")

ENDIF
```
