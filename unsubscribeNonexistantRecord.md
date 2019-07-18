## Unsubscribe New Record from All Subscribers

Sometimes, you run into a problem where the subscriber you sent an email to does not exist in the all subscribers table within marketing cloud. Now, ideally you should not be sending to customers who do not exist in all subscribers, but on the off chance this does happen, all you need to do is add the following lines of code to get around this.

**NOTE:** Make sure you have a custom unsubcribe page before you implementing this code.

```html
SET @ts_sub = CREATEOBJECT("Subscriber")
SETOBJECTPROPERTY(@ts_sub,"SubscriberKey", @subkey)
SETOBJECTPROPERTY(@ts_sub, "EmailAddress", @emailaddr)
SETOBJECTPROPERTY(@ts_sub, "Status", "Active")
SET @ts_statusCode = INVOKECREATE(@ts_sub, @update_sub_status, @update_sub_errorcode, @options)

IF @update_sub_status == "Created Subscriber." THEN
  //success
ELSE
  //failure
ENDIF
```
The code will add the record to the all subscribers and the variable `@update_sub_status` will always be "Created Subscriber." for a new record. Based on this, you can then use the next set of lines to unsubscribe the newly added record into all subscribers.

```html
SET @lue = CREATEOBJECT("ExecuteRequest")
SETOBJECTPROPERTY(@lue,"Name", "LogUnsubEvent")

SET @lue_prop = CREATEOBJECT("APIProperty")
SETOBJECTPROPERTY(@lue_prop, "Name", "SubscriberKey")
SETOBJECTPROPERTY(@lue_prop, "Value", @subkey)
ADDOBJECTARRAYITEM(@lue, "Parameters", @lue_prop)

SET @lue_prop = CREATEOBJECT("APIProperty")
SETOBJECTPROPERTY(@lue_prop, "Name", "Email Address")
SETOBJECTPROPERTY(@lue_prop, "Value", @emailaddr)
ADDOBJECTARRAYITEM(@lue, "Parameters", @lue_prop)

SET @lue_prop = CREATEOBJECT("APIProperty")
SETOBJECTPROPERTY(@lue_prop, "Name", "JobID")
SETOBJECTPROPERTY(@lue_prop, "Value", @jobId)
AddObjectArrayItem(@lue, "Parameters", @lue_prop)

SET @lue_prop = CREATEOBJECT("APIProperty")
SETOBJECTPROPERTY(@lue_prop, "Name", "ListID")
SETOBJECTPROPERTY(@lue_prop, "Value", @listId)
ADDOBJECTARRAYITEM(@lue, "Parameters", @lue_prop)

SET @lue_prop = CREATEOBJECT("APIProperty")
SETOBJECTPROPERTY(@lue_prop, "Name", "BatchID")
SETOBJECTPROPERTY(@lue_prop, "Value", @batchid)
ADDOBJECTARRAYITEM(@lue, "Parameters", @lue_prop)

SET @lue_prop = CREATEOBJECT("APIProperty")
SETOBJECTPROPERTY(@lue_prop, "Name", "Reason")
SETOBJECTPROPERTY(@lue_prop, "Value", @reason)
ADDOBJECTARRAYITEM(@lue, "Parameters", @lue_prop)

SET @lue_statusCode = INVOKEEXECUTE(@lue, @overallStatus, @requestId)

SET @lue_Response = ROW(@lue_statusCode, 1)
SET @lue_Status = FIELD(@lue_Response,"StatusMessage")
SET @lue_Error = FIELD(@lue_Response,"ErrorCode")

IF @lue_Status == "OnMasterUnsubscribeList" OR @lue_Status == "Event posted" THEN

  // go to a thank you page

ENDIF
```
