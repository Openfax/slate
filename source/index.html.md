---
title: Openfax CloudFax API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - PHP

toc_footers:
  - <a href='https://openfax.com/signup'>Sign Up for an Account</a>
  - <a href='https://portal.openfax.com'>Portal Login</a>

includes:
  - errors

search: true
---

# Getting Started

The Openfax Cloud Fax API is designed to allow developers quick and easy access to global facsimile services.  With Openfax's fax APIs you can:

* Send a single fax document to a single location.
* Send a single fax document to many locations (fax broadcast).
* Send a customized fax document to many locations based on a Microsoft DOCx template (mail merge broadcast).
* Configure inbound fax numbers.
* Receive a fax to your email, cloud storage on AWS S3, or webhook postback.

##Account & API Key

**To get Started** you'll need to [sign-up for an account](https://openfax.com/signup).

* An account is required to use the API.
* An account number is required to use the API.
* An API key is required to use the API.

Once you have an established account you will be able to login to the [portal](https://portal.openfax.com/) to obtain your API key from your account settings.

<aside class="notice">
Contact us after you have completed your registration for free developer trial credits.
</aside>
## Getting Help
1. By Phone at 1-866-OPENFAX(673-6329) or +1.847.221.1979
2. Chat at [openfax.com](https://openfax.com/)
3. Email [broadcast@openfax.com](mailto:broadcast@openfax.com)
4. Slack by invite, email us at [broadcast@openfax.com](mailto:broadcast@openfax.com)
5. Skype by invite, email us your skype name to [broadcast@openfax.com](mailto:broadcast@openfax.com)

# Sending Faxes

Overview

Rates
Funding

##Send a Single Fax
```php

// Sending a single outbound fax request

<?php

$pdf = "/home/docs/mydoc.pdf";     // Fax Document PDF location

$pdf_encode = base64_encode(file_get_contents($pdf));

$account_id = ‘#######’;           // Your Account ID
$apikey = 'Your API Key';          // API Key for your Account ID

$faxnum = '15555551212’;           // Destination Fax Number US & Canada Dialing Only
$to_header = 'My Customer';        // Fax To Header Value
$from_header = 'My Company"';      // Fax From Header Value 
$paper_size = '0';                 // Paper size 
                                   // 0 - US Letter   8.5"x11"
                                   // 1 - US Legal  8.5"X14"
                                   // 2 - A4  8.27" × 11.69"

$fax_res = '0';                    // Fax Resolution:
                                   // 0 - Standard Resolution
                                   // 1 - Fine Resolution


$priority = 'Normal';              //Priority value for the fax:
                                   // Low - Slower priorty
                                   // Normal - Faster priority

$post = array(

'AccountID'      => $account_id,
'FaxNumber'      => $faxnum,
'toHeader'       => $to_header,
'fromHeader'     => $from_header,
'faxResolution'  => $fax_res,
'APIKey'         => $apikey,
'PDF_encode'     => $pdf_encode,
'paperSize'     => $paper_size,
'Priority' => $priority  
);

$ch   = curl_init();
$url  = 'https://api.openfax.com/faxSendSubmit.php';
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$data1 = curl_exec($ch);
print_r($data1);
?>
``` 
###HTTPS POST:
`https://api.openfax.com/faxSendSubmit.php`

Sending a single fax allows you to provide one fax number and one pdf file for transmission. Openfax will attempt to deliver the fax conducting up to 3 redial attempts with a 4-minute wait duration between each redial.

After your fax has been delivered or all retries have been exhausted Openfax will provide reporting details for the fax using a variety of options:


* Automatic Webhook Post Back
* Query by OutTransID
* Query by DateRange


<aside class="note">
Note: All parameters bold denotes required value.
</aside>
### Submission Parameters
Name | Type | Len | Desc | Example
-------------- | -------------- | --------------  | --------------  | --------------
**AccountID** | Int | 9 | Openfax Account ID | 123456789
**APIKey** | VarChar | 255 | Set in portal account settings | IEYRtw2cd8aGa54
**FaxNumber** | BigInt | 20 | 11-digit North American Number | 15555551212
toHeader | VarChar | 15 | Appears in the To field of fax the header. | My Customer
frHeader | VarChar | 15 | Appears in the Fr field of fax the header. | My Company
**paperSize** | Int | 1 | Specifies input PDF paper size: <br/> 0 - US Letter (8.5 x 11.0 in / 216 x 279 mm) </br>1 - US Legal (8.5 x 14.0 in /216 x 356 mm) <br/> 2 - A4 (8.5 x 11.0 in / 216 x 279 mm) | 0
**faxResolution** | Int | 1 | Fax resolution used for transmission:<br/>0 - Standard Resolution (204 x 98 dpi) <br/>1 - Fine Resolution (204 x 196 dpi)<br/><br/> *Note: Fine mode may increase the documents transmission time. Depending on your pricing arrangement transmission times may impact the cost of the fax transmission.* | 0
**Priority** | VarChar | 6 | Transactional faxing will always receive a higher priority on the Openfax network over other fax broadcast traffic.  This affects how quickly your fax will be attempted in relation to other fax traffic on the network. <br/><br/>It is recommended to always use “Normal”, though if your fax is less urgent you may Specify “Low” to give Normal transactional faxes preference over those specific with a Low priority.  It is not possible to change a transactional fax priority once it has been submitted.<br/><br/> Values are case sensitive. <br/>Normal - High transactional priority <br/>Low - Lower transactional priority, other transactions will receive preference within the queue. | Normal
**PDF_encode** | Base64 | 50MB | The PDF file is limited to 50MB max and must be Base64 encoded. | n/a


<aside class="warning">
You must use the proper paper size for your fax destination's country. Using an improper paper size may cause your fax to fail.
</aside>
```json
{"Response":"Success","OutTransID":"INT"}

{"Response":"Failed"}

{"Response":"InvalidAuthentication"}

{"Response":"AccountInactive"}

{"Response":"RestrictedFaxNumber"}

{"Response":"InvalidFaxNumberLength"}

{"Response":"InvalidFaxResolution"}

{"Response":"InvalidPapersize"}

{"Response":"InvalidPriority"}

```

### Response Parameters
Reponse |  Condition 
-------------- | --------------
Success | Returns the OutTransID for the submitted outbound fax transaction.
Failed | The Transaction request has failed.
InvalidAuthentication |  The account number or API key provided is invalid.
AccountInactive |  The account has been depleted of funds or has been deactivated. Check balance, apply payment or contact support.
Restricted Number | The attempted number is restricted from dialing either due to the phone number owner’s request, compliance list or another reason.  Contact customer support for details if required.
InvalidFaxNumberLength | The FaxNumber is not valid for dialing.   <br/>North American dialing 1NPANXXXXX is required.   <br/>International dialing must not include + and be contain the country code and local number such as 861069445000.
InvalidFaxResolution | Specified faxResolution value is invalid. <br/>Value must be 0 for standard or 1 for fine resolution.
InvalidPaperSize | Specified paperSize value is invalid.   <br/>Value must be 0 - US Letter, 1 - US Legal  or 2 - A4
InvalidPriority | Specified Priority value is invalid.  <br/> Values are case sensitive, *Normal* or *Low*.
##Send a Standard Broadcast
##Send a Mail Merge Broadcast
##International Dialing


# Fax Status & Reporting
## Acccount Balance

```php

// Get your real-time account balance

<?php

$account_id = your_account_id;
$apikey = 'your_api_key';
$post = array(

'AccountID' => $account_id,
'APIKey' => $apikey

);s

$ch   = curl_init();
$url  = 'https://api.openfax.com/getAvailableCredit.php';
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$data1 = curl_exec($ch);
print_r($data1);
?>

```

Get your real-time account credit balance.

###HTTPS GET:
`https://api.openfax.com/getAvailableCredit.php`
### Submission Parameters

Name | Type | Len | Desc | Example
-------------- | -------------- | --------------  | --------------  | --------------
**AccountID** | Int | 9 | Openfax Account ID | 123456789
**APIKey** | VarChar | 255 | Set in portal account settings | IEYRtw2cd8aGa54

```json
[{"AvailableCredit":8956.6637749994}]
```
### Response Parameters

Name | Type | Desc
-------------- | -------------- | -------------- 
AccountBalance | BigInt | Account Balance |

*A positive number reflects available credit in your account.*
##Status Codes
###LastStatus
The last status code is provided for the latest result of the fax attempt.  If the report detail is recieved by webhook postback then the transaction is finalized.  When requesting real-time report data the transaction may still have available redials.  The LastStatus is the result for the individal HistortOutTransID associated to the OutTransID fax request.

Code |  Description | Redial | Explaination 
-------------- | -------------- | -------------- | --------------
-1 | Recieved | No | The fax request has been received and is in processing.
0 | Successful | No | The fax was delivered.
1 | No Answer | Yes | There was no answer when placing the call.
2 | Busy | Yes | The line ring response was busy.
3 | Not Fax | No | A fax answer was not detected.
4 | All Circuits Busy | Yes | Carrier replied all routes busy.
5 | Bad Number | No | The number is not a valid telephone number.
6 | Call Disconnected | Yes | The call was prematurely disconnected, possible voice answer.
7 | Unkown / Other | Yes | The network did not receive a PAGE OK reponse, possibly not a fax or line noise or not a fax.
8 | Doc Conversion Failed | No | The document could not be converted.
9 | Credit Check Failed | No | There is insufficient credit to process the fax.

###Current State
Code |  Description 
-------------- | -------------- 
0 | Received
1 | Document Rendering
2 | Checking Credit
3 | On Outbound Table / Waiting for queue space
4 | In Outbound Queue / In queue for node
5 | At Fax Node / Fax dialing
6 | Reporting Engine
7 | Waiting for Redial
8 | Finalized / Fax Successful
9 | Finalized / Fax Failed
10 | Finalized / Fax Failed due to document conversion
11 | Credit Hold
12 | Retry Eligible
13 | Canceled

## Single Fax Webhook Post Back
```php
<?php 

$outtransactionid = $_REQUEST['OutTransID'];  //Transaction ID for this fax
$accountid = $_REQUEST['AccountID'];          //Account Number used for this request
$receivedfrom = $_REQUEST['From'];            //Fax Fr Header
$receivedto = $_REQUEST['To'];                //Fax To Header
$pages = $_REQUEST['PagesSent'];              //Number of pages sent
$callduration = $_REQUEST['CallDuration'];    //Total transmission time of fax in seconds
$status = $_REQUEST['LastStatus'];            //Deliver status code of the fax
$cost = $_REQUEST['RealCost'];                //Cost for this fax

print_r(json_encode($_REQUEST));  //prints JSON formatted response

?>

```
Configure Webhook postpack is the easiest way to get notified of your fax requests result.

###Enable Webhook Postback
1. Login to your portal account
2. Select settings
3. Enable Post Back for Outbound Faxes
4. Provide your public postback 

Example:
`https://your-public-url/path-to-script/getPostBack.php`





##Single Fax Outbound By Date
```php
// Gets Single Fax Outbound Activty by Date
<?php

$account_id = your_account_id;      // Your Account ID
$apikey = 'your_api_key';           // API Key for your Account ID
$from_date = '2016-05-27';          // Start date in YYYY-MM-DD
$to_date = '2016-05-31';            // End date in YYYY-MM-DD
$detailtype = 1;                    // 0 for summary (finalized only), 1 for detail (all)
$lasthistoryid = 936600;            // Optional starting history ID, returns 9366001+
$limit = 5;                         // Max number of rows returned, max 1,0000

$post = array(

'AccountID' => $account_id,
'APIKey' => $apikey,
'FromDate' => $from_date,
'ToDate' =>  $to_date,
'DetailType' => $detailtype,
'LastHistoryID' => $lasthistoryid,
'Limit' => $limit

);

$ch   = curl_init();
$url  = 'https://api.openfax.com/outboundDetailsbyDate.php';
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$data1 = curl_exec($ch);
print_r($data1);

?>
```

###HTTPS GET:
`https://api.openfax.com/outboundDetailsbyDate.php`

It is suggested to use Single Fax Webhook Postback for tracking or billing of fax activity. This method provides the ability to retrieve all single transacational fax requests for your account in a summary or all attempt format. 

### Types of Reporting

* **Summary**<br/>
Returns only the last and finalized call record for each transaction.

* **Detail**<br/>
Returns all attempts for each fax request in detail.

### Submission Parameters

Name | Type | Len | Desc | Example
-------------- | -------------- | --------------  | --------------  | --------------
**AccountID** | Int | 9 | Openfax Account ID | 123456789
**APIKey** | VarChar | 255 | Set in portal account settings | IEYRtw2cd8aGa54
**FromDate** | Date | 10 | Start date YYYY-MM-DD | 2017-05-27
**ToDate** | Date | 10 | End date YYYY-MM-DD | 2017-05-31
**DetailType** | Int | 1 |0 Summary Only, 1 Detail Report | 0


### Response Parameters

Name | Type | Len | Desc | Example
-------------- | -------------- | --------------  | --------------  | --------------
HistoryOutboundFaxID | BigInt | 255 | Sequential call record ID | 921363
**OutTransID** | BigInt | 255 | Unique fax request ID | 779029
**AccountID** | Int | 9 | Openfax Account ID | 123456789
FaxNumber | BigInt | 20 | Destination fax number | 15555551212
CurrentState | Int | 11 | Current status of processing | [Status Codes](#current-state)
CallerID | BigInt | 20 | Outbound Caller ID used with transacation | 17205721703
MaxAttempts | Int | 1 | Maximum number of redial attempts allowed | 3
RetryCount | Int | 1 | Sequence number of this call of MaxAttempts | 1
ToHeader | VarChar | 15 | Text provided for the fax To Header | Jones Smith
FromHeader | VarChar | 15 | Text provided for the fax Fr Header | Mike T.
FaxResolution | Int | 1 | Document resolution: <br/> 0 - Standard <br/> 1 - Fine  | 0
RenderStatus | Int | 1 | Render status:<br/<> 0 - Pending<br/> 1 - Complete <br/> 2 - Failed | 1
FaxDocURL | VarChar | 255 | Tiff image location | file.tif
PageCount | Int| 4 | Number of pages in the fax document | 2
PaperSize | Int | 1 | Specifies input PDF paper size: <br/> 0 - US Letter (8.5 x 11.0 in / 216 x 279 mm) </br>1 - US Legal (8.5 x 14.0 in /216 x 356 mm) <br/> 2 - A4 (8.5 x 11.0 in / 216 x 279 mm) | 0


Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeoswmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:
s
```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

