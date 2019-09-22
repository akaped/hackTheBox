# 0-registration.md

The first challange of www.hackthebox.eu is to hack into the registration process generating a invitation key. 

* Go to the page: https://www.hackthebox.eu/invite
* Fire up the dev tools of your browser and take a look at the HTML code. 
The HTML code seems fine. So let's explore the js files that are loaded with the page. One of those has a name that should strike our attention: 

"<script defer="" src="/js/inviteapi.min.js"></script>"

Let's explore what is holding this file by surfing the page: 

https://www.hackthebox.eu/js/inviteapi.min.js

This is a minified file so the code doesn't have any identation, to visualise better the code I've used an online tool:
https://beautifier.io/

And got back this result:

```
function verifyInviteCode(code) {
    var formData = {
        "code": code
    };
    $.ajax({
        type: "POST",
        dataType: "json",
        data: formData,
        url: '/api/invite/verify',
        success: function(response) {
            console.log(response)
        },
        error: function(response) {
            console.log(response)
        }
    })
}

function makeInviteCode() {
    $.ajax({
        type: "POST",
        dataType: "json",
        url: '/api/invite/how/to/generate',
        success: function(response) {
            console.log(response)
        },
        error: function(response) {
            console.log(response)
        }
    })
}
```

We can see that there are two functions here. One is used to verify the invitation code that we don't have, while the other is a function that makes an invitation code!!!!
Let's go back to www.hackthebox.eu/invite, open the dev tools of the browser and go to the "Console" tab. 

digit the name of the function we just discovered and press enter to execute it:

makeInviteCode()

This returns:

{0: 200, success: 1, data: {…}}
0: 200
data:
data: "SW4gb3JkZXIgdG8gZ2VuZXJhdGUgdGhlIGludml0ZSBjb2RlLCBtYWtlIGEgUE9TVCByZXF1ZXN0IHRvIC9hcGkvaW52aXRlL2dlbmVyYXRl"
enctype: "BASE64"
__proto__: Object
success: 1
__proto__: Object

So we got a long string of gibberish with another info: enctype = encryption type BASE64. 

I went to https://www.base64decode.org/, pasted the "SW4gb3JkZXIgdG8gZ2VuZXJhdGUgdGhlIGludml0ZSBjb2RlLCBtYWtlIGEgUE9TVCByZXF1ZXN0IHRvIC9hcGkvaW52aXRlL2dlbmVyYXRl" and decoded it. The result is a very important info.

"In order to generate the invite code, make a POST request to /api/invite/generate".

Ok, we need to make a POST request. There are various ways to do this, I've used curl.

I've opened my Terminal and typed: curl -XPOST https://www.hackthebox.eu/api/invite/generate
My output was
{"success":1,"data":{"code":"VFhCWFItTEZYVFEtR05PSFUtU1NTQ1YtTlZHVk4=","format":"encoded"},"0":200}
, probably you will get something different since the invite codes are unique. Is this finished ? No, also this string is encoded ! 

I've tried again to use BASE64 as encoder and I was lucky, it decoded to: 
TXBXR-LFXTQ-GNOHU-SSSCV-NVGVN

Enter the code in http://hackthebox.eu/invite.
You are in, and you can now register a user account! 

COMPLETED. 
