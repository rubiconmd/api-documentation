---
title: RubiconMD API

language_tabs:
  - shell

toc_footers:
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

<aside class="warning">
The requests presented on this API documentation are for our live **production environment**. For development and testing, it is recommended to use our **sandbox environment**.
</aside>

Server |  URL
--------- | -----------
**Sandbox:** | `https://demo.rubiconmd.com`
**Production:** | `https://rubiconmd.com`

# Authentication

> The RubiconMD server authenticates via the use of access tokens. <br>

```shell
# Just a standard HTTP Request
  Note: Be sure to curl using https: when connecting.

## User authentication not required (app has standing permissions from user)
curl -i https://rubiconmd.com/api/v1/oauth/token
 -F grant_type="standing"
 -F email="USER_EMAIL_ADDRESS"
 -F client_id="YOUR_CLIENT_ID"
 -F client_secret="YOUR_CLIENT_SECRET"

 ## User authentication required
 curl -i https://rubiconmd.com/api/v1/oauth/token
 -F grant_type="client_credentials"
 -F client_id="YOUR_CLIENT_ID"
 -F client_secret="YOUR_CLIENT_SECRET"
```

> A successful request will return:

```json
[
  {
    "access_token": "AAAAAA",
    "token_type": "bearer",
    "expires_in": 7200
  }
]
```



RubiconMD uses an OAuth2 workflow for obtaining the access tokens needed for API access.

To make an access token request, valid API keys are required. To request or cycle your API keys, please contact [Michael](mailto: michael@rubiconmd.com?Subject=Rubicon%20API%20Keys).

RubiconMD API requires a valid access token to be included within the parameters for all API requests to the server:

`?access_token="AAAAAA"`

<aside class="notice">
For security, all access_tokens expire in two hours.
</aside>

### Grant Types

`standing`: If you are making a request on behalf of a user that has granted standing access to your application (if you created the user, such permissions are granted by default), then this grant type will return an access token that will be sufficient to make requests that user to authenticate on the RubiconMD site.

`client_credentials`: This will return a token that can be used for making queries that do not require a user to be logged in. This includes OAuth2-type queries where the user authenticates on the RubiconMD site, then is redirected back to your site with a valid_access token for making API calls on the user's behalf.

# Basics

Here are some basic tools you can use with all API calls.

## Limit

<aside class="warning">
  The limit parameter has been deprecated -- it was just too limit-ed. Please use `per_page`, instead.
</aside>

## Paginators

```shell
curl -i https://rubiconmd.com/api/v1/provider_cases?access_token="AAAAAA"&per_page=15&page=2
```

You can use the `per_page` and `page` parameters to paginate results. If neither parameter is provided, all records will be returned by default.

<aside class="success">
Using pagination greatly increases response times and is highly recommended.
</aside>

### per_page

Returns the specified number of records per page.

<aside class="notice">
  For all endpoints, the default number of records `per_page` is 25. If a `per_page` parameter is passed without a `page` parameter, the server will return just the first page by default.
</aside>

### page

Returns the specified page of records. If, for example, you only want to retrieve records 16-30, passing `?per_page=15&page=2` as parameters would give you just those records.

### Header fields

Using one or both of the pagination parameters will add some useful header fields to the server response, including:

* `Total` - total number of records available
* `Per-Page` - number of records being returned per page
* `Link` - the API endpoints (as full URLs) to call for retrieving the next and previous pages of results

# Provider Cases

Provider Cases are created by a user that is seeking a specialist response to the case.

## Get All Provider Cases

```shell
curl -i https://rubiconmd.com/api/v1/provider_cases?access_token="AAAAAA"
```

> A successful request returns the following:

```json
[
  {
    "id": 10071849,
    "state": "opened",
    "question": "Victim bleeding from pores... possibly Red Death?"
  },
  {
    "id": 888,
    "state": "assigned",
    "question": "Is it possible to have a case of Rocky Mountain Spotted Fever occur in the Appalachians and with stripes?"
  }
]
```

This endpoint retrieves all the provider case for the authenticated user.

### HTTP Request

`GET https://rubiconmd.com/api/v1/provider_cases?access_token="AAAAAA"`

### Query Optional Parameters

Parameter |  Description
--------- | -----------
state | Only returns cases of the specified state. Possible options: assigned, accepted, declined, awaiting_review, need_specialist_reply, need_pcp_reply, closed
per_page | Number of records to display per_page. [Read more.](#paginators)
page | Page to display. [Read more.](#paginators)

## Get a Specific Provider Case

```shell
curl -i https://rubiconmd.com/api/v1/provider_cases/10071849?access_token="AAAAAA"
```

> A successful request returns the following:

```json
{
  "id": 10071849,
  "state": "accepted",
  "question": "Victim bleeding from pores... possibly Red Death?",
  "created_at": "1842-05-15T15:33:44.220-04:00",
  "updated_at": "1842-05-15T15:45:56.440-04:00",
  "assigned_on": "1842-05-15T15:33:46.774-04:00",
  "accepted_on": "1842-05-15T15:45:56.430-04:00",
  "patient_age": 33,
  "patient_gender": "Male",
  "differential_diagnosis": "If not Red Death, maybe Telltale Heart Disease?",
  "medical_history": "Lots of partying and excessive drinking.",
  "medications": "Amontillado",
  "symptoms": "convulsions, bloody sweat, seeing masked illusions.",
  "labs": null
}
```

This endpoint will return the JSON data about a specific case.

<aside class="notice">
 Make sure to replace `CASE_ID` with the actual ID of the case you want to look up.
</aside>


### HTTP Request

`GET https://rubiconmd.com/api/v1/provider_cases/CASE_ID?access_token="AAAAAA"`

### URL Parameters

Parameter | Description
--------- | -----------
CASE_ID | The ID of the case to retrieve

## Create a New Provider Case

```shell
curl -X POST
  -H "Content-Type: application/json"
  -d '{"provider_case":{"question":"Victim bleeding from pores... possibly Red Death?","patient_birthdate": "2009-01-19","patient_gender": "Male","patient_first_name":"P.","patient_middle_name":"N/A","patient_last_name":"Prospero","differential_diagnosis":"If not Red Death, maybe Telltale Heart Disease?","medical_history":"Lots of partying and excessive drinking.","medications":"Amontillado","symptoms":"convulsions, bloody sweat, seeing masked illusions.","labs":"N/A"}}' "https://rubiconmd.com/api/v1/provider_cases?access_token=AAAAAA"
```

>A successful POST request returns a JSON response:

```json
{
  "id": 10071849,
  "state": "accepted",
  "question": "Victim bleeding from pores... possibly Red Death?",
  "created_at": "1842-05-15T15:33:44.220-04:00",
  "updated_at": "1842-05-15T15:45:56.440-04:00",
  "assigned_on": "1842-05-15T15:33:46.774-04:00",
  "accepted_on": "1842-05-15T15:45:56.430-04:00",
  "patient_age": 33,
  "patient_gender": "Male",
  "differential_diagnosis": "If not Red Death, maybe Telltale Heart Disease?",
  "medical_history": "Lots of partying and excessive drinking.",
  "medications": "Amontillado",
  "symptoms": "convulsions, bloody sweat, seeing masked illusions.",
  "labs": null
}
```

### HTTP Request

`POST https://rubiconmd.com/api/v1/provider_cases/ {"provider_case": {"question": TEXT, "patient_birthdate": TEXT, "patient_gender": TEXT, "patient_first_name": TEXT, "patient_middle_name": TEXT, "patient_last_name": TEXT, "differential_diagnosis": TEXT, "medical_history": TEXT, "medications": TEXT, "symptoms": TEXT, "labs": TEXT} }`

### URL Parameters

Parameter |  Description
--------- | -----------
question | The specific question for the specialist to answer.
patient_birthdate | Patient birthdate. Must be formatted as text: "YYYY-MM-DD"
patient_gender | Patient gender. Only acceptable values are "Female",  "Male", and "Other".
patient_first_name | Patient first name.
patient_middle_name | Patient middle name.
patient_last_name | Patient last name.
differential_diagnosis | Physician thoughts/plans.
medical_history | Patient relevant prior medical history.
medications | Patient medications.
symptoms | Patient current presentation.
labs | Lab results.

# Case Referrals

Case Referrals are the de-identified cases received by a specialist. Only users that are specialists can access Case Referrals.

## Get All Case Referrals

```shell
curl -i https://rubiconmd.com/api/v1/referrals?access_token="AAAAAA"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 37,
    "state": "accepted",
    "question": "Does this look deadly?"
  },
  {
    "id": 22,
    "state": "need_pcp_reply",
    "question": "Is this Lupus?"
  }
]
```

This endpoint returns all the `Consults` tied to `Assignments` a specialist has participated on as individual or as a `Specialist Panel` member, given that the assignments are not in `revoked`, `time_out` or `declined` state. Will include also consults in `timed_out` and `declined` states if the `Specialist` had written a `Response` for them.

### HTTP Request

`GET https://rubiconmd.com/api/v1/referrals?access_token="AAAAAA"`

### Query Optional Parameters

Parameter |  Description
--------- | -----------
state | Gets pending cases with a particular state.
per_page | Number of records to display per_page. [Read more.](#paginators)
page | Page to display. [Read more.](#paginators)

**states:** assigned, accepted, declined, awaiting_review, need_specialist_reply, need_pcp_reply, closed

## Get a Specific Case Referral

```shell
curl -i https://rubiconmd.com/api/v1/referrals/57?access_token="AAAAAA"
```

> The above command returns JSON structured like this:

```json
{
  "id": 57,
  "state": "accepted",
  "question": "Is this normal?",
  "created_at": "2014-07-15T15:33:44.220-04:00",
  "updated_at": "2014-07-15T15:45:56.440-04:00",
  "assigned_on": "2014-07-15T15:33:46.774-04:00",
  "accepted_on": "2014-07-15T15:45:56.430-04:00",
  "patient_age": 18,
  "patient_gender": "Female",
  "differential_diagnosis": "This is my Differential Diagnosis.",
  "medical_history": "A long one.",
  "medications": "None that you should know.",
  "symptoms": "Feels bad.",
  "labs": "Cholesterol levels are fine."
}
```

This endpoint will return the JSON data about a specific case.

<aside class="notice">
 Make sure to replace `CASE_ID` with the actual ID of the case you want to look up.
</aside>


### HTTP Request

`GET https://rubiconmd.com/api/v1/referrals/CASE_ID?access_token="AAAAAA"`

### URL Parameters

Parameter | Description
--------- | -----------
CASE_ID | The ID of the case to retrieve


## Accept / Reject a Specific Case

```shell
curl -i https://rubiconmd.com/api/v1/referrals/57/reject?access_token="AAAAAA"
```

> The above command returns JSON structured like this:

```json
{
  "id": 57,
  "state": "declined",
  "question": "Is this normal?",
  "created_at": "2014-07-15T15:33:44.220-04:00",
  "updated_at": "2014-07-15T15:45:56.440-04:00",
  "assigned_on": "2014-07-15T15:33:46.774-04:00",
  "accepted_on": "2014-07-15T15:45:56.430-04:00",
  "patient_age": 18,
  "patient_gender": "Female",
  "differential_diagnosis": "This is my Differential Diagnosis.",
  "medical_history": "A long one.",
  "medications": "None that you should know.",
  "symptoms": "Feels bad.",
  "labs": "Cholesterol levels are fine."
}
```

This endpoint allows you to accept or turn down a specific case.

<aside class="success">
When a case is accepted, a successful call will return the JSON data about the case. A successful rejected case call will return: {message: "The case was successfully rejected."}
</aside>
<aside class="warning">
An unsuccessful call will return a **402** and an error message:
{"error":"This case could not be accepted at this time."}
</aside>

### HTTP Request

`GET https://rubiconmd.com/api/v1/rubiconmdeferrals/CASE_ID/ACTION?access_token="AAAAAA"`

### URL Parameters

Parameter |  Description
--------- | -----------
CASE_ID | The ID of the case to retrieve
ACTION | Accepts or rejects a case.

**Actions:** accept, reject

# Case Responses

## Get All Responses

```shell
curl -i https://rubiconmd.com/api/v1/referrals/63/responses?access_token="AAAAAA"

curl -i https://rubiconmd.com/api/v1/provider_cases/63/responses?access_token="AAAAAA"
```

> A successful request returns an array of JSON responses:

```json
[
  {
    "id": 17,
    "case_id": 63,
    "specialist_id": 12,
    "body": "This is where you put the specialist text response.",
    "created_at": "2014-04-12T14:11:38.554-04:00",
    "purpose": "specialist_opinion",
    "need_reply": true
  },
  {
  "id": 18,
  "case_id": 63,
  "specialist_id": null,
  "body": "This is a pcp response.",
  "created_at": "2014-04-12T14:19:33.554-04:00",
  "purpose": "pcp_reply",
  "need_reply": false
  }
]
```

Retrieves all the responses associated with a specific case.

### HTTP Request

`GET https://rubiconmd.com/api/v1/referrals/CASE_ID/responses?access_token="AAAAAA"`

or

`GET https://rubiconmd.com/api/v1/provider_cases/CASE_ID/responses?access_token="AAAAAA"`

### URL Parameters

Parameter |  Description
--------- | -----------
CASE_ID | The ID of the case to retrieve

### Response Fields

Parameter |  Description
--------- | -----------
id | ID # of response
case_id | ID # of associated case
specialist_id | ID # of specialist that made the response (if user was a provider, this value will be nil)
body | The text of the response
created_at | Time response was created
purpose | Either 'pcp_comment' or 'specialist_reply'
need_reply | Boolean value dictating whether or not creator needed a reply to the response

## Get a Specific Response

```shell
curl -i https://rubiconmd.com/api/v1/referrals/63/responses/17?access_token="AAAAAA"

curl -i https://rubiconmd.com/api/v1/provider_cases/63/responses/17?access_token="AAAAAA"
```

>The above command returns JSON like this:

```json
{
  "id":17,
  "case_id":63,
  "specialist_id":12,
  "body":"This is where you put the specialist text response.",
  "created_at":"2014-04-12T14:11:38.554-04:00",
  "purpose":"specialist_opinion",
  "need_reply":true
}
```

Lookup a specific response by the response's ID.

### HTTP Request

`GET https://rubiconmd.com/api/v1/referrals/CASE_ID/responses/RESPONSE_ID?access_token="AAAAAA"`

or

`GET https://rubiconmd.com/api/v1/provider_cases/CASE_ID/responses/RESPONSE_ID?access_token="AAAAAA"`

### URL Parameters

Parameter |  Description
--------- | -----------
CASE_ID | The ID of the case to retrieve
RESPONSE_ID | The ID of the specific response you want to get

## Post a Response

```shell
curl -X POST
  -H "Content-Type: application/json"
  -d '{"response":{"body":"This is where you put the text of the response.","purpose":"specialist_opinion","need_reply":"true"}}' "https://rubiconmd.com/api/v1/referrals/63/responses?access_token=AAAAAA"

curl -X POST
  -H "Content-Type: application/json"
  -d '{"response":{"body":"This is where you put the text of the response.","purpose":"pcp_comment","need_reply":"true"}}' "https://rubiconmd.com/api/v1/provider_cases/63/responses?access_token=AAAAAA"
```

>A successful POST request returns a JSON response:

```json
{
  "id": 17,
  "case_id": 63,
  "specialist_id": 12,
  "body": "This is where you put the text of the response.",
  "created_at": "2014-04-12T14:11:38.554-04:00",
  "purpose": "pcp_comment",
  "need_reply": true
}
```

This allows you post a response in a specific case (as a specialist). It also returns your response.

### HTTP Request

`POST https://rubiconmd.com/api/v1/referrals/CASE_ID/responses/ {"response": {"body": TEXT, "purpose": "specialist_opinion", "need_reply": BOOLEAN} }`

or

`POST https://rubiconmd.com/api/v1/provider_cases/CASE_ID/responses/ {"response": {"body": TEXT, "purpose": "pcp_comment", "need_reply": BOOLEAN} }`

### URL Parameters

Parameter |  Description
--------- | -----------
CASE_ID | The ID of the case you want to respond to
body | TEXT field with your response
purpose | "specialist_opinion" or "pcp_comment"
need_reply | BOOLEAN field, set to true if a reply is required from the provider/specialist

<aside class="notice">
The server will return an "unprocessable entity" HTTP response if the purpose does not match the user type (e.g., the user is a provider but the purpose is "specialist_reply").
</aside>

# Images

## Get All Images of a case

```shell
curl -i https://rubiconmd.com/api/v1/provider_cases/CASE_ID/attachments?access_token="AAAAAA"
```

> A successful request returns an array of JSON responses:

```json
{
  "id": "Zhy7",
  "image_file_name": "ee83c9baf45b73138bc6c771a.jpg",
  "image_file_size": 73260,
  "image_content_type": "image/jpg",
  "created_at": "2014-04-12T14:11:38.554-04:00",
  "updated_at": "2014-07-15T15:45:56.440-04:00",
  "removable": false,
  "original": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/original/ee83c9baf45b73138bc6c771a.jpg?",
  "large": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/large/ee83c9baf45b73138bc6c771a.jpg?",
  "thumb_340": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/thumb_340/ee83c9baf45b73138bc6c771a.jpg?",
  "thumb_180": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/thumb_180/ee83c9baf45b73138bc6c771a.jpg?",
  "response_id": null
}
```

Retrieves all the images associated with a specific case.

### HTTP Request

`GET https://rubiconmd.com/api/v1/provider_cases/CASE_ID/attachments?access_token=AAAAAA`

### URL Parameters

Parameter |  Description
--------- | -----------
CASE_ID | The ID of the case to retrieve

### Response Fields

Parameter |  Description
--------- | -----------
id | ID # of image
image_file_name | Encrypted name of the file
image_file_size | Size of the file
image_content_type | The type of file
created_at | Time image was created
updated_at | Time image was updated
removable | Whether or not the image can be deleted
original | URL of the original image
large | URL of the large size image
thumb_340 | URL of the thumb sized(340px) image
thumb_180 | URL of the thumb sized(180px) image
response_id | Response ID of response attached to the image

## Post an Image

```shell
curl -X POST
  -H "Origin: https://rubiconmd.com"
  -F "attachment[image]=@/path/filename.ext" "https://rubiconmd.com/api/v1/provider_cases/A-9AGj/attachments?access_token=AAAAAA"
```

>A successful POST request returns a JSON response:

```json
{
  "id": "Zhy7",
  "image_file_name": "ee83c9baf45b73138bc6c771a.jpg",
  "image_file_size": 73260,
  "image_content_type": "image/jpg",
  "created_at": "2014-04-12T14:11:38.554-04:00",
  "updated_at": "2014-07-15T15:45:56.440-04:00",
  "removable": false,
  "original": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/original/ee83c9baf45b73138bc6c771a.jpg?",
  "large": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/large/ee83c9baf45b73138bc6c771a.jpg?",
  "thumb_340": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/thumb_340/ee83c9baf45b73138bc6c771a.jpg?",
  "thumb_180": "https://rubicon.s3.amazonaws.com/attachments/images/000/000/000/thumb_180/ee83c9baf45b73138bc6c771a.jpg?",
  "response_id": null
}
```

This allows you post an image in a specific case (as a pcp).

### HTTP Request

`POST https://rubiconmd.com/api/v1/provider_cases/CASE_ID/attachments?access_token=AAAAAA`

### URL Parameters

Parameter |  Description
--------- | -----------
CASE_ID | The ID of the case you want to upload to

# Members

Operate over the members of a Payer

## Batch Upload

```shell
curl -X POST
  -H "Content-Type: application/json"
  -d '{"members":[{"uid": "JIYGN-357-5309","first_name": "Charles","last_name": "Bovary","gender": "Male","birthdate": "12/15/1856","email": "devoted.husband@gmail.fr","race": "Norman","phone_number": "555-555-5555","insurance": "self","location": "Tostes","valid_until": "01/15/2018"}]}' "https://rubiconmd.com/api/v1/members/upload?access_token=AAAAAA"
```

> The above command ingests JSON structured like this:

```json
[
  {
    "uid": "JIYGN-867-5309",
    "first_name": "Emma",
    "last_name": "Bovary",
    "gender": "Female",
    "birthdate": "12/15/1856",
    "email": "rouen.girl@gmail.fr",
    "race": "Norman",
    "phone_number": "555-555-5555",
    "insurance": "officier de santé",
    "location": "Tostes",
    "valid_until": "01/15/2018"
  },
  {
    "uid": "JIYGN-637-5309",
    "first_name": "Rodolphe",
    "last_name": "Boulanger",
    "gender": "Male",
    "birthdate": "12/15/1856",
    "email": "livin.easy@gmail.fr",
    "race": "Aristocrat",
    "phone_number": "555-555-5555",
    "insurance": "independent wealth",
    "location": "Tostes",
    "valid_until": "01/15/2018"
  },
  {
    "uid": "JIYGN-638-6309",
    "first_name": "Léon",
    "last_name": "Dupuis",
    "gender": "Male",
    "birthdate": "12/15/1856",
    "email": "poetry.lover@gmail.fr",
    "race": "Norman",
    "phone_number": "555-555-5555",
    "insurance": "grace of God?",
    "location": "Rouen",
    "valid_until": "01/15/2018"
  }
]
```

This endpoint retrieves all the members associated with your API client's organization.

### Important Notes

* All payloads must be formatted in JSON and wrapped with {"members": [...]}, with the JSON payloads for individual members listed within the array.

* UID is required for each member and must be unique. If two payloads are submitted with the same UID, the payload received later will overwrite the earlier one.

* An access token created using the client_credentials authentication strategy is sufficient for this completing this request (most other tokens should be valid, as well).

### HTTP Request

`POST https://rubiconmd.com/api/v1/members?access_token="AAAAAA" {"members":[{"uid": "JIYGN-357-5309","first_name": "Charles","last_name": "Bovary","gender": "Male","birthdate": "12/15/1856","email": "devoted.husband@gmail.fr","race": "Norman","phone_number": "555-555-5555","insurance": "self","location": "Tostes","valid_until": "01/15/2018"}]}`

### Query Optional Parameters

Parameter |  Description
--------- | -----------
uid | A unique identifier for the member, such as plan UID or MRN. (required)
first_name | Member first name. (recommended)
last_name | Member last name. (recommended)
gender | Member gender. Preferred values are 'Male', 'Female', or 'Transgender'
birthdate | Member birthdate. Should be formated as MM/DD/YYYY. (required)
email | Member e-mail. (optional)
race | Member race. Can be combined with ethnicity.
phone_number | Member contact phone number.
insurance | Member insurance plan (freetext).
location | Member current location.
valid_until | Date that member record is no longer valid, or must be renewed/updated by. Should be formated as MM/DD/YYYY.

<!--
 ##
 ##
 ## USER INFO
 ##
 ##
-->

# Users

##User Information

```shell
curl -i https://rubiconmd.com/api/v1/users/me?access_token="AAAAAA"
```
> The above command returns JSON structured like this:

```json
{
   "id":12,
   "is_specialist":true,
   "first_name":"Franklin",
   "last_name":"McAwesome",
   "role":"medical_doctor",
   "email":"awesome_specialist@rubiconmd.com",
   "organization":"Health Medical Clinic",
   "tos_accepted_at": "2017-08-01T23:55:10.981-05:00",
   "legal_documents":[
     {
       "id": "kwey",
       "purpose": "terms_of_service",
       "name": "Terms Of Service",
       "signed_at": "2017-08-01T23:55:10.981-05:00"
     }, {
       "id": "DlGy",
       "purpose": "specialist_baa",
       "name": "Specialist Business Associate Agreement",
       "signed_at": "null"
     }, {
       "id": "hxBn1",
       "purpose": "specialist_agreement",
       "name": "Specialist Agreement",
       "signed_at": "null"
     }
   ],
   "country":"United States",
   "specialties":[
      {
         "specialty":{
            "id":17,
            "name":"Electrophysiology",
            "category":"Cardiology"
         }
      },
      {
         "specialty":{
            "id":24,
            "name":"HIV",
            "category":"Infectious Diseases"
         }
      },
      {
         "specialty":{
            "id":31,
            "name":"Hand surgery",
            "category":"Orthopedic Surgery"
         }
      }
   ]
}
```

Want to get information about the user you just logged in? This is your endpoint.

The `legal_documents` array returns the documents that correspond to that user to sign, and their signature date if signed. This is, a null on signed_at means the latest version of the given document is not signed by the user, and the client should handle the signing process.

The `privacy_policy` document needs no signature from any user, so it won't be presented in this object. However, it will be accessible via the corresponding endpoints (see _Legal Documents_ section).

### HTTP Request

`GET https://rubiconmd.com/api/v1/users/me?access_token="AAAAAA"`

##Creating Users

```shell
curl -X POST
  -H "Content-Type: application/json"
  -d '{"user":{"email": "awesome_specialist@rubiconmd.com", "first_name": "Franklin", "last_name": "McAwesome", "role": "medical_doctor"}}' "https://rubiconmd.com/api/v1/users?access_token=AAAAAA"
```
> The above command returns JSON structured like this:

```json
{
   "first_name":"Franklin",
   "last_name":"McAwesome",
   "email":"awesome_specialist@rubiconmd.com",
   "role":"medical_doctor"
}
```

This allows you to create a new provider for your organization.

### HTTP Request

`POST https://rubiconmd.com/api/v1/users/ {"user": {"email": TEXT, "first_name": TEXT, "last_name": TEXT, "role": ROLE} }`

### URL Parameters

Parameter |  Description
--------- | -----------
email | The e-mail from the user you are creating.
first_name | First name of the user.
last_name | Last name of the user.
role | User's role. Roles are limited to the following values: 'medical_doctor' , 'physician_assistant' , 'registered_nurse' , 'nurse_practitioner'
per_page | Number of records to display per_page. [Read more.](#paginators)
page | Page to display. [Read more.](#paginators)

## Reviewers

```shell
curl -i https://rubiconmd.com/api/v1/users/reviewers?access_token="AAAAAA"
```
> The above command returns JSON structured like this:

```json
[
  {
    "id": "zaBgO",
    "first_name": "Charles",
    "last_name": "Bovary",
    "email": "devoted.husband@gmail.fr"
  },
  {
    "id": "9RXXW",
    "first_name": "Jason",
    "last_name": "Awesome",
    "email": "jason.awesome@gmail.com"
  },
  {
    "id": "YdgL8",
    "first_name": "Megan",
    "last_name": "Chair",
    "email": "megachair@ucsf.edu"
  }
]
```

Retrieve all the possible users that can review an eConsult.

### HTTP Request

`GET https://rubiconmd.com/api/v1/users/reviewers?access_token="AAAAAA"`

# Legal Documents

These API endpoints allow clients to have knowledge of which legal documents have been signed (accepted) by the user. For example, the 'terms of service' or the 'specialist agreement'.

##index

This endpoint returns the basic information of the documents available to the user making the API call.

### HTTP Request

```shell
curl -X GET
  -H "Content-Type: application/json"
  "https://rubiconmd.com/api/v1/users/:user_id/legal_documents?access_token=AAAAAA"
```

> The above command would return a json like this:

```json
[ { "name": "Terms of Service", "purpose": "terms_of_service", "id": "DlGy" },
  { "name": "Privacy Policy", "purpose": "privacy_policy", "id": "kwey" } ]
```

`GET https://rubiconmd.com/api/v1/users/:user_id/legal_documents?access_token=AAAAAA"`

<aside class="notice">
  The user id is needed in the call, in the `:user_id` segment.
</aside>

The call will return a JSON array with the `name` of the documents, their `id` and their `purpose`. The former two pieces are used to access each individual document.

Differences in the response will reside in:

* A Specialist will get the latest versions of privacy policy, terms of service, specialist agreement and specialis business associate agreement documents.
* An Organization Admin will get the latest versions the privacy policy, terms of service, business associate agreement and the different versions available for organization contracts. Even if several contract versions are listed, the user will only be able to access and sign the contract that suits the configuration of the Organization they belong to.
* The rest of users will get the latest versions of the privacy policy and the terms of service.


##show

This endpoint returns the information for the document related to the `id` passed in the call.

### HTTP Request

```shell
curl -X GET
  -H "Content-Type: application/json"
  "https://rubiconmd.com/api/v1/users/:user_id/legal_documents/kwey?access_token=AAAAAA"
```

> The above command would return a json like this:

```json
{ "name": "Privacy Policy",
  "content": "text",
  "created_at": "2017-12-11T09:17:10.753-05:00",
  "updated_at": "2017-12-11T09:17:10.753-05:00",
  "id": "kwey" }
```

```shell
curl -X GET
  -H "Content-Type: application/json"
  "https://rubiconmd.com/api/v1/users/:user_id/legal_documents/DlGy?access_token=AAAAAA"
```

> The above command, for a signed document, would return a json like this:

```json
{ "name": "Terms of Service",
  "content": "text",
  "created_at": "2017-12-11T09:17:10.753-05:00",
  "updated_at": "2017-12-11T09:17:10.753-05:00",
  "id": "kwey",
  "signed_at": "2017-08-01T23:55:10.981-05:00" }
```

`GET https://rubiconmd.com/api/v1/users/:user_id/legal_documents/:id?access_token=AAAAAA"`

<aside class="notice">
  The user id is needed in the call, in the `:user_id` segment.
</aside>

The call will return the name of the document, the text of the document in html format so it is easily usable in the views, and other info regarding the time of creation of the document.

If the user has signed the document, it will return the date of when this action was performed.

If the document requested is not accessible by the user (according to the user type) or can not be found, the response will have the `422` status code and the body will be `{error: 'Document not available' }`.

##sign

This endpoint allows a user to sign a document.

### HTTP Request

```shell
curl -X POST
  -H "Content-Type: application/json"
  "https://rubiconmd.com/api/v1/users/:user_id/legal_documents/kwey/sign?access_token=AAAAAA"
```

> The above command, for a signed document, would return a json like this:

```json
{ "name": "Terms of Service",
  "content": "text",
  "created_at": "2017-12-11T09:17:10.753-05:00",
  "updated_at": "2017-12-11T09:17:10.753-05:00",
  "id": "kwey",
  "signed_at": "2017-08-01T23:55:10.981-05:00" }
```

`POST https://rubiconmd.com/api/v1/users/:user_id/legal_documents/:id/sign?access_token=AAAAAA"`

<aside class="notice">
  The user id is needed in the call, in the `:user_id` segment. The document id is needed as well in the `:id` segment
</aside>

The call will peform an attempt to generate a signature for the document backend side. If the call succeeds, the returned JSON object will be equivalent to the one in the `show` method, in the case that the user has signed the document.

If the call fails, the response will be a `422` status code and the body will include the error that prevented the signature from being created. One possible reason for failure is that the user has already signed that document. In this case the json response would be: `["Legal document signer should sign a document only once"]`

If the document requested is not accessible by the user (according to the user type) or can not be found, the response will have the `422` status code and the body will be `{ error: 'Document not available' }`.

##Accessing documents by type

To ease work for clients, we've enabled the possiblity of accessing and signing the documents directly by their `purpose`, so the initial call for the index method is not needed.

The available purposes are:

General (**all** users):

* `privacy_policy`
* `terms_of_service`

Right now, all users should have a signature for the `terms_of_service` document in order to use the platform, and be prompted by the client to sign it if otherwise.

Directed to **Organization admins**:

* `contract`
* `contract_dsa`
* `baa`

Client Organizations need to have their `baa` and proper `contract` signed in order for their users to be able to access the platform. A client can know beforehand which contract should the organization admin sign with the information provided in the `users#me` endpoint.

Directed to **Specialists**:

* `specialist_agreement`
* `specialist_baa`

Specialists need to have both documents above signed in order to operate in the platform.

### HTTP Request for accessing a document

`GET https://rubiconmd.com/api/v1/users/:user_id/legal_documents/type/:purpose?access_token=AAAAAA"`

This call will be equivalent to calling the `show` method described above in terms of returned objects and error messages.

If the purpose is mistyped on the call or it is not accessible by the user trying to access it, the system will prevent access to that information

### HTTP Request for signing a document

```shell
curl -X POST
  -H "Content-Type: application/json"
  "https://rubiconmd.com/api/v1/users/:user_id/legal_documents/type/terms_of_service/sign?access_token=AAAAAA"
```

> The above command would return a json like this:

```json
{ "name": "Terms of Service",
  "content": "text",
  "created_at": "2017-12-11T09:17:10.753-05:00",
  "updated_at": "2017-12-11T09:17:10.753-05:00",
  "id": "kwey",
  "signed_at": "2017-08-01T23:55:10.981-05:00" }
```

`POST https://rubiconmd.com/api/v1/users/:user_id/legal_documents/type/:purpose/sign?access_token=AAAAAA"`

This call will be equivalent to calling the `sign` method described above in terms of returned objects and error messages.

If the purpose is mistyped on the call or it is not accessible by the user trying to access it, the system will prevent access to that information.

# iFrame

##iFrame URL calls

In order to call certain pages of our site through an iFrame, you must first obtain a user token.  It's a two-step process.

1. Get a user token by curling the token endpoint with the access token.
2. Append the user token along with user info in the url.

<aside class="notice">
User tokens are one-time/single use only.
</aside>

##Obtaining a user token

To get a user token, curl the endpoint with the access token.

```shell
curl -i https://rubiconmd.com/api/v1/users/token?access_token="AAAAAA"
```

> A successful request will return:

```json
[
  {
    "user_token": "AAAAAA",
    "created_at": -1302048000,
    "expires_in": 300
  }
]
```

### HTTP Request

`GET https://rubiconmd.com/api/v1/users/token?access_token="AAAAAA"`

##Auto-login & Display case

Pass users' username, user token, and case id parameters into the URL.
<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET https://rubiconmd.com/pcp_iframe?user_username=EMAIL&user_token=TOKEN&case_id=CASE_ID`

### URL Parameters

Parameter |  Description
--------- | -----------
email | The e-mail of the user you are logging in.
user_token | Single-use user token.
case_id | The ID of the case you want to see.

<aside class="notice">
If you send the user to the iframe with an invalid token the user will be asked to login.  No errors will be raised.
</aside>
