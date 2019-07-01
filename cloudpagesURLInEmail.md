## Send subscriberkey to the Landing Page
This one is quite basic. This method will only send the subscribkey, automatically appended to the end of the string as `_subscriberkey`. If you want to attach other information, you can either use a lookup function on the landing page based off the subscriberkey or add the variables as needed.
```html
<a href="%%=CLOUDPAGESURL(XXXX)=%%">click here</a>
```
Send extra information along with the subscriberkey to the landing page
```html
%%[
SET @foo = [foo]
SET @bar = [bar]
]%%
<a href="%%=CLOUDPAGESURL(XXXX,, 'foo', @foo, 'bar', @bar)=%%">click here</a>
```
