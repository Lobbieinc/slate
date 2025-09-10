---
title: Lobbie API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
    - shell

toc_footers:
    - <a href='https://lobbie.com'>Join Lobbie</a>
    - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

# includes:
#   - errors

search: true

code_clipboard: true

meta:
    - name: description
      content: Documentation for the Lobbie Developer API
---

# Introduction

Welcome to the Lobbie Developer API! You can use our API to access Lobbie Developer API endpoints, which can be used to create, read, update and delete information about Locations, Patients and Forms within Lobbie.

You can view code examples in the dark area to the right to see how to send requests to the Lobbie Developer API.

# Endpoints

Lobbie provides API access for both the Sandbox and Production/My environments. Each environment has its own domain as follows:

Sandbox:
auth-sandbox.lobbie.com - for retrieving access tokens using a client id and client secret.
api-sandbox.lobbie.com - for create/read/update/delete operations within Lobbie.

Prod:
auth.lobbie.com - for retrieving access tokens using a client id and client secret.
api.lobbie.com - for create/read/update/delete operations within Lobbie.

All the following examples will use the `sandbox` environment to make sure that if you copy + paste an example your production environment is not affected.

# Response Structure

Every response from Lobbie should result in a 200 with a JSON structure as shown on the right:

<pre>
<code>
{
  "success": boolean,
  "message: string,
  data: {
    ...
  }
}
</code>
</pre>

If `success` is `false`, the `message` key will provide an explanation and the `data` key will be empty.

If `success` is `true`, the `data` key will be filled with information relevant to the request that was sent.

. However, if the request was **_not_** successful,

# Terminology / Vocabulary

### Form Template

What Lobbie uses to create a Form for a Patient.

When Lobbie updates a Form Template for your organization, the `version` number is increased by 1.

Lobbie uses the latest `version` of a Form Template to create Forms for Patients.

### Form Template Group

A collection of Form Templates. A link is assigned to the collection and can be used to provide Patients an "anonymous" method of completing each Form that is generated from each Form Template in the Form Template Group.

### Form Template Section

A page of a Form Template. If a Form Template has multiple Form Template Sections a button at the bottom of the page will be enabled when a Patient has completed all required fields in the current section.

### Form Element

A single field in a Form Template Section.

### Lobbie Form Attribute

A metadata tag added to a Form Element which can be used to give structure to unstructured data.

Lobbie Form Attributes have "lifecycles" of which there are two: either the "Form Group" or "Patient" lifecycle.

A Lobbie Form Attribute with a lifecycle of "Form Group" (default) will only [autofill](#autofill) fields in other Forms when the Form that owns the Lobbie Form Attribute is completed.

A Lobbie Form Attribute with a lifecycle of "Patient" will [prefill](#prefill) Forms in future Form Group/Packets assigned to the same Patient when that Form Group/Packet is created.

### Form

What a Patient completes. Created from a Form Template.

### Form Group

A collection of Forms assigned to a Patient to be completed together with a shared due date.

### Form Packet

Same as a "Form Group". An alias.

### Form Answer

A single answer to a single Form Element in a Form.

### Prefill

Using either Lobbie Form Attributes with the Lifecycle value of "Patient" or data from the Patient record in Lobbie to complete fields in a Form BEFORE the Patient begins the Form. Uses Lobbie Form Attributes to map data to Form Answers in a Form. Runs when a Form is created.

-   Developers can pass a map of key/value pairs to prefill data in Forms when a Form Group/Packet is created. Passed values take precedence over Lobbie Form Attribute "Patient" lifecycles and Patient data.

### Autofill

Saving answers from one Form to other Forms in a Form Group. Happens when a Form is completed. Uses Lobbie Form Attributes to map data between Forms in a Form Group.

### Fillable PDF

When printing a Form, Lobbie maps Form Answers to PDF file that has been submitted to Lobbie. Instead of Lobbie Form Attributes we use Mapped PDF Fields.

# Authentication

> Request an Access Token:

```shell
curl -X POST \
    https://auth-sandbox.lobbie.com/oauth2/token \
    -H "authorization: Basic \"$(echo -n '<client_id>:<client_secret>' | base64)\"" \
    -H 'content-type: application/x-www-form-urlencoded' \
    -d 'grant_type=client_credentials'
```

> Make sure to replace `client_id` and `client_secret` with your Client ID and Client Secret.

Lobbie uses the OAuth2 client_credentials flow to allow access to the Developer API.

To get a Client ID and Client Secret, log into your Lobbie account as a user with the Developer role and navigate to the [Developer Settings Page](https://sandbox.lobbie.com/home/settings/developer)

Lobbie expects an Access Token be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer $LOBBIE_ACCESS_TOKEN`

<aside class="notice">
You must replace <code>access_token</code> with the token returned from calling https://auth-sandbox.lobbie.com/oauth2/token with your Client ID and Client Secret.
</aside>

# Locations

## Get All Locations

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "All locations for <account_name>.",
    "data": [
        {
            "timeZone": "US/Pacific",
            "active": true,
            "name": "California",
            "id": 1,
            "createdOn": "2023-07-26T16:48:37.000Z",
            "lastUpdatedOn": "2023-07-26T16:48:37.000Z"
        }
    ]
}
```

This endpoint retrieves all locations.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations`

### Query Parameters

None

## Get a Specific Location

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations/<location_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "timeZone": "US/Pacific",
        "active": true,
        "name": "California",
        "id": 1,
        "createdOn": "2023-07-26T16:48:37.000Z",
        "lastUpdatedOn": "2023-07-26T16:48:37.000Z",
        "address": {
            "address1": "6833 Caminito Sueno",
            "city": "Carlsbad",
            "postalCode": "92009",
            "fullAddress": "6833 Caminito Sueno, Carlsbad, CA 92009",
            "latitude": 39.28962937068381,
            "longitude": -76.6148371308074,
            "id": 1,
            "createdOn": "2023-07-26T16:48:37.000Z",
            "lastUpdatedOn": "2023-07-26T16:48:37.000Z",
            "stateProvince": {
                "shortName": "CA",
                "name": "California",
                "id": 5,
                "createdOn": "2023-07-26T16:48:37.000Z",
                "lastUpdatedOn": "2023-07-26T16:48:37.000Z"
            }
        },
        "branding": {
            "logoName": "Lobbie Pro Paperless (LOCAL)",
            "logoLetterColor": "#ffffff",
            "logoCircleColor": "#2b46ff",
            "isUsingCustomLogoImage": false,
            "fromEmailAddress": "noreply@lobbie.com",
            "fromEmailName": "Lobbie",
            "replyToEmailAddress": "noreply@lobbie.com",
            "id": 1,
            "createdOn": "2023-07-26T16:49:26.000Z",
            "lastUpdatedOn": "2023-07-26T16:49:26.000Z",
            "isLocationBrandingActive": false,
            "logoS3Url": null,
            "locationId": null
        }
    }
}
```

This endpoint retrieves a specific location.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations/<location_id>`

### URL Parameters

| Parameter | Description                        |
| --------- | ---------------------------------- |
| ID        | The ID of the Location to retrieve |

## Create a Location

```shell
curl "https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations/create" \
  -X POST \
  -H "Authorization: $LOBBIE_ACCESS_TOKEN"
  -H "Content-Type: application/json"
  --data '{
    "address": {
      "address1": "1 East Baltimore St",
      "address2": "Suite 100", # Optional
      "city": "Baltimore",
      "postalCode: "21202",
      "stateProvince": {
        "id": 1,               # Retrieve Lobbie IDs for StateProvince objects by sending a GET to https://api-sandbox.lobbie.com/lobbie/api/developer/v1/state_provinces
        "name": "Maryland",
        "shortName": "MD"
      }
    },
    "name": "The Greatest City in America",
    "timeZone": "America/New_York", # You can also use the deprecated US/Eastern style.
    "active": true,                 # Inactive Locations are hidden from responses.
    "phoneNumber": "4108675309",    # Optional. Not used for SMS.
    "staffIds": [1, 2, 3]             # IDs of Staff members created in Lobbie who are assigned to this Location. Staff members only have UI access to Locations to which they are assigned.
  }'
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "timeZone": "US/Eastern",
        "active": true,
        "name": "Maryland",
        "id": 2,
        "staffIds": [1, 2, 3],
        "createdOn": "2023-07-26T16:48:37.000Z",
        "lastUpdatedOn": "2023-07-26T16:48:37.000Z",
        "address": {
            "address1": "1 East Baltimore St",
            "city": "Baltimore",
            "postalCode": "21202",
            "fullAddress": "1 East Baltimore St, Baltimore, MD 21202",
            "latitude": 33.10724999521315,
            "longitude": -117.22945198859631,
            "id": 2,
            "createdOn": "2023-07-26T16:48:37.000Z",
            "lastUpdatedOn": "2023-07-26T16:48:37.000Z",
            "stateProvince": {
                "shortName": "MD",
                "name": "Maryland",
                "id": 33,
                "createdOn": "2023-07-26T16:48:37.000Z",
                "lastUpdatedOn": "2023-07-26T16:48:37.000Z"
            }
        },
        "branding": {
            "logoName": "Lobbie Pro Paperless (LOCAL)",
            "logoLetterColor": "#ffffff",
            "logoCircleColor": "#2b46ff",
            "isUsingCustomLogoImage": false,
            "fromEmailAddress": "noreply@lobbie.com",
            "fromEmailName": "Lobbie",
            "replyToEmailAddress": "noreply@lobbie.com",
            "id": 1,
            "createdOn": "2023-07-26T16:49:26.000Z",
            "lastUpdatedOn": "2023-07-26T16:49:26.000Z",
            "isLocationBrandingActive": false,
            "logoS3Url": null,
            "locationId": null
        }
    }
}
```

This endpoint creates a Location in Lobbie.

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations/create`

## Update a Location

```shell
curl "https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations/update" \
  -X POST \
  -H "Authorization: $LOBBIE_ACCESS_TOKEN"
  -H "Content-Type: application/json"
  --data '{
    "id": 2,                   # The ID of the Location to update.
    "address": {
      "address1": "2 East Baltimore St",
      "address2": "Suite 200", # Optional
      "city": "Baltimore",
      "postalCode: "21202",
      "stateProvince": {
        "id": 1,               # Retrieve Lobbie IDs for StateProvince objects by sending a GET to https://api-sandbox.lobbie.com/lobbie/api/developer/v1/state_provinces
        "name": "Maryland",
        "shortName": "MD"
      }
    },
    "name": "The Greatest City in America",
    "timeZone": "America/New_York", # You can also use the deprecated US/Eastern style.
    "active": true,                 # Inactive Locations are hidden from responses.
    "phoneNumber": "4108675309",    # Optional. Not used for SMS.
    "staffIds": [4, 5, 6]             # IDs of Staff members created in Lobbie who are assigned to this Location. Staff members only have UI access to Locations to which they are assigned.
  }'
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "timeZone": "US/Eastern",
        "active": true,
        "name": "Maryland",
        "id": 2,
        "staffIds": [4, 5, 6],
        "createdOn": "2023-07-26T16:48:37.000Z",
        "lastUpdatedOn": "2023-07-26T16:48:37.000Z",
        "address": {
            "address1": "1 East Baltimore St",
            "city": "Baltimore",
            "postalCode": "21202",
            "fullAddress": "2 East Baltimore St, Baltimore, MD 21202",
            "latitude": 33.10724999521315,
            "longitude": -117.22945198859631,
            "id": 2,
            "createdOn": "2023-07-26T16:48:37.000Z",
            "lastUpdatedOn": "2023-07-26T16:48:37.000Z",
            "stateProvince": {
                "shortName": "MD",
                "name": "Maryland",
                "id": 33,
                "createdOn": "2023-07-26T16:48:37.000Z",
                "lastUpdatedOn": "2023-07-26T16:48:37.000Z"
            }
        },
        "branding": {
            "logoName": "Lobbie Pro Paperless (LOCAL)",
            "logoLetterColor": "#ffffff",
            "logoCircleColor": "#2b46ff",
            "isUsingCustomLogoImage": false,
            "fromEmailAddress": "noreply@lobbie.com",
            "fromEmailName": "Lobbie",
            "replyToEmailAddress": "noreply@lobbie.com",
            "id": 1,
            "createdOn": "2023-07-26T16:49:26.000Z",
            "lastUpdatedOn": "2023-07-26T16:49:26.000Z",
            "isLocationBrandingActive": false,
            "logoS3Url": null,
            "locationId": null
        }
    }
}
```

This endpoint updates a Location in Lobbie.

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/locations/update`

# Patients

## Get All Patients

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```javascript
{
  "success": true,
  "message": "",
  "data": [
    {
      "active": true,
      "email": "patient@lobbie.com",
      "firstName": "Donny",
      "lastName": "DemoPatient",
      "mobilePhone": "4108675309",
      "dateOfBirth": "2/2/2006",
      "id": 1,
      "createdOn": "2023-07-26T16:48:49.000Z",
      "lastUpdatedOn": "2023-07-26T16:48:49.000Z",
      "isEmailConfirmed": false,
      "isPhoneConfirmed": false,
      "receiveEmail": true,
      "receiveSms": true,
      "name": "Donny DemoPatient",
      "isMinimumAgeConfirmedAccount": true,
      "sexAssignedAtBirth": 2, // See Enums.SexAssignedAtBirthEnum for values.
      "legalSex": 0,           // See Enums.LegalSexEnum for values.
      "intakeMethod": "UNKNOWN",
      "gender": "male",
    }
  ]
}
```

This endpoint retrieves all patients.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients`

### Query Parameters

None

## Get a Specific Patient

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients/<patient_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```javascript
{
  "success": true,
  "message": "",
  "data": {
    "active": true,
    "email": "patient@lobbie.com",
    "firstName": "Donny",
    "lastName": "DemoPatient",
    "mobilePhone": "4108675309",
    "dateOfBirth": "2/2/2006",
    "id": 1,
    "createdOn": "2023-07-26T16:48:49.000Z",
    "lastUpdatedOn": "2023-07-26T16:48:49.000Z",
    "isEmailConfirmed": false,
    "isPhoneConfirmed": false,
    "receiveEmail": true,
    "receiveSms": true,
    "name": "Donny DemoPatient",
    "isMinimumAgeConfirmedAccount": true, // This may be false if "Minimum Age Required in Years" in your Account settings is not null and the Patient has yet to start their first set of forms.
    "sexAssignedAtBirth": 2,  // See Enums.SexAssignedAtBirthEnum for values.
    "legalSex": 0,            // See Enums.LegalSexEnum for values.
    "intakeMethod": "STAFF_SENT_FORMS",
    "gender": "male",
    "homeAddress": {
      "address1": "1 E Baltimore St.",
      "city": "Baltimore",
      "postalCode": "21202",
      "stateProvince": "MD",
      "fullAddress": "1 E Baltimore St., Baltimore, MD 21202"
    },
    "mailingAddress": {
      "address1": "123 W Something St.",
      "city": "Baltimore",
      "postalCode": "21202",
      "stateProvince": "MD",
      "fullAddress": "123 W Something St., Baltimore, MD 21202"
    },
    "parents": [],      // A list of Patient objects that are parents of this Patient
    "children": [],     // A list of Patient objects that are children of this Patient
    "practitioners": [] // A list of Practitioner objects that are assigned to this Patient
  }
}
```

This endpoint retrieves a specific patient.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients/<patient_id>`

### URL Parameters

| Parameter | Description                       |
| --------- | --------------------------------- |
| ID        | The ID of the Patient to retrieve |

## Search for Patients

Find patient(s) using different query parameters.

<aside class="notice">
    This endpoint can return more than 1 patient.
</aside>

```shell
curl -G GET \
    "https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients/search" \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data-urlencode "firstName=Donny" \
    --data-urlencode "lastName=DemoPatient" \
    --data-urlencode "email=patient@lobbie.com" \
    --data-urlencode "dateOfBirth=08/10/2003"
```

> The above command returns JSON structured like this:

```javascript
{
  "success": true,
  "message": "Patient search results",
  "data": [
    {
      "active": true,
      "email": "patient@lobbie.com",
      "firstName": "Donny",
      "lastName": "DemoPatient",
      "mobilePhone": "4108675309",
      "dateOfBirth": "8/10/2003",
      "id": 1,
      "createdOn": "2023-07-28T15:38:26.000Z",
      "lastUpdatedOn": "2023-07-28T15:38:26.000Z",
      "isEmailConfirmed": false,
      "isPhoneConfirmed": false,
      "receiveEmail": true,
      "receiveSms": true,
      "name": "Donny DemoPatient",
      "isMinimumAgeConfirmedAccount": true,
      "sexAssignedAtBirth": 2,
      "legalSex": 0,
      "intakeMethod": "UNKNOWN",
      "gender": "non-binary"
    }
  ]
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients/search`

### Query Parameters

-   `firstName` - The first/given name of the Patient.

-   `lastName` - The last/family name of the Patient.

-   `name` - The first/give and last/family name of the Patient. If this is not empty it will be used instead of the first/last name query parameters.

-   `email` - The email of the Patient to search for.

-   `mobilePhone` - The phone number of the Patient to search for.

-   `dateOfBirth` - The birth date of the Patient in MM/DD/YYYY format.

# Patient Tags

## Get All Patient Tags

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/patients/tags?patientId=? \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "All tags for <patient>.",
    "data": [
	  {
	    "id": 5,
	    "note": "Here is a new tag",
	    "color": "#FFF5C4",
	    "active": true
	  },
	  {
	    "id": 6,
	    "note": "Here is another new tag",
	    "color": "#F7D8D8",
	    "active": true
	  }
	]
}
```

This endpoint retrieves all patient tags associated with the patient.

# Form Templates

[Form Templates](#form-template) are what Lobbie uses to create Forms for

## Get All Form Templates

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

<asside class="notice">
When Lobbie updates a Form Template for your organization, the `version` number is increased by 1.

Lobbie uses the latest `version` of a Form Template to create Forms for Patients
</aside>

```json
{
    "success": true,
    "message": "All form templates for <account_name>.",
    "data": [
        {
            "id": 7,
            "name": "Lobbie Local All Elements",
            "isActive": true,
            "isStaffOnly": false,
            "sortOrder": 6,
            version: 1,
        },
        {
            "id": 1,
            "name": "Lobbie Local Patient Information",
            "isActive": true,
            "isStaffOnly": false,
            "sortOrder": 0,
            version: 1,
        }
    ]
}
```

This endpoint retrieves all form templates associated with your organization's Lobbie account.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates`

### Query Parameters

None

## Get a Specific Form Template

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/<form_template_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "id": 1,
        "name": "Lobbie Local Patient Information",
        "isActive": true,
        "isStaffOnly": false,
        "sortOrder": 0,
        "isFillablePDF": false,
        "publicLink": "https://localhost:3000/public/form/fb924039-ed0f-4214-9196-a7fc33cd3619/3/d4d97da4-af28-4ef4-80b2-1bcc2b1e5129",
        "formTemplateSections": [
            {
                "sortOrder": 0,
                "name": "Patient Section",
                "id": 1,
                "formElements": [
                    {
                        "label": "First Name",
                        "required": true,
                        "sortOrder": 0,
                        "elementType": 3,
                        "colClass": 3,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 1,
                        "children": [],
                        "attribute": "first_name"
                    },
                    {
                        "label": "Middle Name",
                        "required": false,
                        "sortOrder": 1,
                        "elementType": 3,
                        "colClass": 3,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 2,
                        "children": [],
                        "attribute": "middle_name"
                    },
                    {
                        "label": "Last Name",
                        "required": true,
                        "sortOrder": 2,
                        "elementType": 3,
                        "colClass": 3,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 3,
                        "children": [],
                        "attribute": "last_name"
                    },
                    {
                        "label": "Birth Date",
                        "required": true,
                        "sortOrder": 3,
                        "elementType": 6,
                        "colClass": 3,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 4,
                        "children": [],
                        "attribute": "date_of_birth"
                    },
                    {
                        "label": "Email",
                        "required": true,
                        "sortOrder": 4,
                        "elementType": 9,
                        "colClass": 4,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 5,
                        "children": [],
                        "attribute": "email"
                    },
                    {
                        "label": "Mobile Phone",
                        "required": true,
                        "sortOrder": 5,
                        "elementType": 7,
                        "colClass": 4,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 6,
                        "children": [],
                        "attribute": "mobile_phone"
                    }
                ]
            },
            {
                "sortOrder": 1,
                "name": "Insurance and other things a patient has to do (long section name test) Section",
                "id": 2,
                "formElements": [
                    {
                        "label": "Insurance Company Name",
                        "required": true,
                        "sortOrder": 7,
                        "elementType": 3,
                        "colClass": 9,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 8,
                        "children": []
                    },
                    {
                        "label": "ID Number",
                        "required": true,
                        "sortOrder": 8,
                        "elementType": 3,
                        "colClass": 4,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 9,
                        "children": []
                    },
                    {
                        "label": "Group/Program #",
                        "required": true,
                        "sortOrder": 9,
                        "elementType": 3,
                        "colClass": 4,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": false,
                        "id": 10,
                        "children": []
                    }
                ]
            },
            {
                "sortOrder": 2,
                "name": "Medical History Section",
                "id": 3,
                "formElements": [
                    {
                        "label": "Do you have a thing?",
                        "required": false,
                        "sortOrder": 11,
                        "elementType": 2,
                        "colClass": 12,
                        "displayLabel": true,
                        "editGroup": 0,
                        "patientViewable": true,
                        "parent": true,
                        "id": 12,
                        "children": [
                            {
                                "label": "Anemia",
                                "required": false,
                                "sortOrder": 12,
                                "elementType": 2,
                                "colClass": 3,
                                "displayLabel": true,
                                "editGroup": 0,
                                "patientViewable": true,
                                "parent": false,
                                "id": 13
                            },
                            {
                                "label": "Artificial heart valves",
                                "required": false,
                                "sortOrder": 14,
                                "elementType": 2,
                                "colClass": 3,
                                "displayLabel": true,
                                "editGroup": 0,
                                "patientViewable": true,
                                "parent": false,
                                "id": 15
                            },
                            {
                                "label": "Asthma",
                                "required": false,
                                "sortOrder": 15,
                                "elementType": 2,
                                "colClass": 3,
                                "displayLabel": true,
                                "editGroup": 0,
                                "patientViewable": true,
                                "parent": false,
                                "id": 16
                            }
                        ]
                    }
                ]
            }
        ]
    }
}
```

This endpoint retrieves a specific [Form Template](#form-template).

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/<form_template_id>`

### URL Parameters

| Parameter | Description                                               |
| --------- | --------------------------------------------------------- |
| ID        | The ID of the [Form Template](#form-template) to retrieve |

# Form Template Groups

## Get All Form Template Groups

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "All form template groups for <account_name>.",
    "data": [
        {
            "name": "Short Form Template Group",
            "active": true,
            "publicUuid": "50877aa3-2691-49d1-bb53-ac5c06d61d56",
            "id": 1,
            "createdOn": "2023-07-28T15:42:47.000Z",
            "lastUpdatedOn": "2023-07-28T15:42:47.000Z",
            "staticLink": "https://short-dev.lobbie.com/30Cf7UBSS-8L",
            "locationId": 1,
            "practitionerId": 11
        }
    ]
}
```

This endpoint retrieves all [Form Template Groups](#form-template-group) associated with your organization's Lobbie account.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

### Query Parameters

None

## Get a Specific Form Template Group

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/<form_template_group_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "name": "Short Form Template Group",
        "active": true,
        "publicUuid": "50877aa3-2691-49d1-bb53-ac5c06d61d56",
        "id": 1,
        "createdOn": "2023-07-28T15:42:47.000Z",
        "lastUpdatedOn": "2023-07-28T15:42:47.000Z",
        "staticLink": "https://short-dev.lobbie.com/30Cf7UBSS-8L",
        "locationId": 1,
        "practitionerId": 11,
        "formTemplates": [
            {
                "id": 3,
                "name": "Lobbie Local Short Form",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 2
            }
        ]
    }
}
```

This endpoint retrieves a specific [Form Template Group](#form-template-group).

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/<form_template_group_id>`

### URL Parameters

| Parameter | Description                                                           |
| --------- | --------------------------------------------------------------------- |
| ID        | The ID of the [Form Template Group](#form-template-group) to retrieve |

## Create a Form Template Group

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      locationId: 1,
      formTemplateIds: [1, 2, 3],
      name: Taco Templates,
      practitionerId: null
    }"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "Form Template Group - Taco Templates - created.",
    "data": {
        "name": "Taco Templates",
        "active": true,
        "staticLink": "https://short-dev.lobbie.com/abc123",
        "locationId": 1,
        "practitionerId": null,
        "formTemplates": [
            {
                "id": 1,
                "name": "Taco 1",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 1
            },
            {
                "id": 2,
                "name": "Taco 2",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 2
            },
            {
                "id": 3,
                "name": "Taco 3",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 3
            }
        ]
    }
}
```

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

## Update a Form Template Group

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 1,
      formTemplateIds: [1, 2],
      name: \"Taco Templates Remix\",
      practitionerId: 1
    }"
```

<aside class="warning">
  You MUST pass the existing `formTemplateIds` on the Form Template Group to retain existing Form Templates.
  <div style="margin-top: 10px; margin-bottom: 10px;">
    Otherwise those Form Templates will be removed from the Form Template Group.
  </div>
  <div>
    For example, if you were to pass an `formTemplateIds` as an empty list, Lobbie would remove all Form Templates from the Form Template Group.
  </div>
</aside>

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "Form Template Group - Taco Templates Remix - updated.",
    "data": {
        "name": "Taco Templates Remix",
        "active": true,
        "staticLink": "https://short-dev.lobbie.com/abc123",
        "locationId": 1,
        "practitionerId": 1,
        "formTemplates": [
            {
                "id": 1,
                "name": "Taco 1",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 1
            },
            {
                "id": 2,
                "name": "Taco 2",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 2
            }
        ]
    }
}
```

### HTTP Request

`PUT https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

## Delete a Form Template Group

```shell
curl -X DELETE \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '{ "id": 1 }'
```

<aside class="notice">
    This operation flips the `active` field to `false` from `true` and the Form Template Group will be:
    <div>
        1. Hidden from the Lobbie UI.
    </div>
    <div>
        2. Inaccessible at its `staticLink`.
    </div>
</aside>

```json
{
    "success": true,
    "message": "Form Template Group - Taco Templates Remix - updated.",
    "data": {
        "name": "Taco Templates Remix",
        "active": false,
        "staticLink": "https://short-dev.lobbie.com/abc123",
        "locationId": 1,
        "practitionerId": 1,
        "formTemplates": [
            {
                "id": 1,
                "name": "Taco 1",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 1
            },
            {
                "id": 2,
                "name": "Taco 2",
                "isActive": true,
                "isStaffOnly": false,
                "sortOrder": 2
            }
        ]
    }
}
```

### HTTP Request

`PUT https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

## Get All Form Template Groups Assigned to a Practitioner

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/practitioner/<practitioner_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "All form template groups for practitioner <practitioner_name>.",
    "data": [
        {
            "name": "Short Form Template Group",
            "active": true,
            "staticLink": "https://short-dev.lobbie.com/30Cf7UBSS-8L",
            "locationId": 1,
            "practitionerId": 11
        }
    ]
}
```

This endpoint retrieves all [Form Template Groups](#form-template-group) associated with a Practitioner in your organization.

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/practitioner/<practitioner_id>`

### Query Parameters

None

# Form Attributes

## Get all Lobbie Form Attributes for a Form Template or Form Template Group

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/attributes?formTemplateId=1 \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "Lobbie Form Attributes for Form Template - 7 / Lobbie Local All Elements",
    "data": [
        {
            "lifecycle": "FORM_GROUP",
            "name": "first_name",
            "active": true,
            "type": "LOBBIE",
            "id": 1,
            "createdOn": "2023-08-02T20:09:48.000Z",
            "lastUpdatedOn": "2023-08-02T20:09:48.000Z"
        },
        {
            "lifecycle": "FORM_GROUP",
            "name": "last_name",
            "active": true,
            "type": "LOBBIE",
            "id": 3,
            "createdOn": "2023-08-02T20:09:48.000Z",
            "lastUpdatedOn": "2023-08-02T20:09:48.000Z"
        },
        {
            "lifecycle": "FORM_GROUP",
            "name": "date_of_birth",
            "active": true,
            "type": "LOBBIE",
            "id": 4,
            "createdOn": "2023-08-02T20:09:48.000Z",
            "lastUpdatedOn": "2023-08-02T20:09:48.000Z"
        }
    ]
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/attributes`

### Query Parameters

**One of:**

-   `formTemplateId` - The ID of the [Form Template](#form-template) to retrieve [Lobbie Form Attributes](#lobbie-form-attribute) from.

-   `formTemplateGroupId` - The ID of the [Form Template Group](#form-template-group) to retrieve Lobbie Form Attributes from. Retrieves Lobbie Form Attributes from all Form Templates in the Form Template Group.

## Get only the names of all Lobbie Form Attributes for a Form Template or Form Template Group

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/attributes/names?formTemplateGroupId=1 \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "Lobbie Form Attribute Names for Form Template - 7 / Lobbie Local All Elements",
    "data": ["first_name", "last_name", "date_of_birth"]
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/attributes/names`

### Query Parameters

**One of:**

-   `formTemplateId` - The ID of the [Form Template](#form-template) to retrieve the names of [Lobbie Form Attributes](#lobbie-form-attribute) from.

-   `formTemplateGroupId` - The ID of the [Form Template Group](#form-template-group) to retrieve the names of Lobbie Form Attributes from. Retrieves Lobbie Form Attributes from all Form Templates in the Form Template Group.

# Form Elements

## Get Form Elements for a Form Template or Form Template Group

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/elements?formTemplateGroupId=1 \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "Form Elements for Form Template - 3 / Lobbie Local Short Form",
    "data": [
        {
            "label": "First Name",
            "required": true,
            "sortOrder": 0,
            "elementType": 3,
            "colClass": 3,
            "displayLabel": true,
            "editGroup": 0,
            "patientViewable": true,
            "parent": false,
            "id": 1,
            "children": [],
            "attribute": "first_name"
        },
        {
            "label": "Last Name",
            "required": false,
            "sortOrder": 1,
            "elementType": 3,
            "colClass": 3,
            "displayLabel": true,
            "editGroup": 0,
            "patientViewable": true,
            "parent": false,
            "id": 2,
            "children": [],
            "attribute": "last_name"
        },
        {
            "label": "Is this a short form?",
            "required": true,
            "sortOrder": 1,
            "elementType": 5,
            "colClass": 12,
            "displayLabel": true,
            "editGroup": 0,
            "patientViewable": true,
            "parent": false,
            "id": 58,
            "children": [],
            "formSelectOptions": [
                {
                    "value": 1,
                    "label": "Very short."
                },
                {
                    "value": 2,
                    "label": "Not short enough."
                }
            ]
        },
        {
            "label": "Conditional",
            "required": true,
            "sortOrder": 29,
            "elementType": 5,
            "colClass": 8,
            "displayLabel": true,
            "editGroup": 0,
            "patientViewable": true,
            "parent": true,
            "id": 116,
            "children": [
                {
                    "label": "Child of Conditional",
                    "required": true,
                    "sortOrder": 30,
                    "elementType": 3,
                    "colClass": 4,
                    "displayLabel": true,
                    "editGroup": 0,
                    "patientViewable": true,
                    "parent": false,
                    "id": 117
                }
            ],
            "formSelectOptions": [
                {
                    "value": 34,
                    "label": "Don't Show Child"
                },
                {
                    "value": 35,
                    "label": "Show Child"
                }
            ]
        }
    ]
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/elements`

### Query Parameters

**One of:**

-   `formTemplateId` - The ID of the [Form Template](#form-template) to retrieve the names of [Lobbie Form Attributes](#lobbie-form-attribute) from.

-   `formTemplateGroupId` - The ID of the [Form Template Group](#form-template-group) to retrieve the names of Lobbie Form Attributes from. Retrieves Lobbie Form Attributes from all Form Templates in the Form Template Group.

## Update Many Form Elements

Similar to the [Update Single Form Element](#update-single-form-element) endpoint below, however, with this endpoint you pass a list of Form Element objects to update.

Not all attributes of a Form Element can be updated as changing some attributes would require a new version of the [Form Template](#form-template) to be issued.

For example, changing a Form Element from not-required to required is not permitted as this would impact Forms that have already been completed by Patients. 

> These commands will update the Form Element with an id of `736` and of type `Select` to first and and then remove the options with labels `Developer Option` and `Taco Option`.

> Additionally, the first request will change the `label` of the Form Element to `Taco Select` and the second request will change the label to `Developer Select`.

```shell
curl -X PUT \
    https://localhost:8443/lobbie/api/developer/v1/forms/templates/elements/update \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '[{ "id": 736, "label": "Taco Select", "options": { "add": ["Developer Option", "Taco Option"] }}]'
```

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/elements/update \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '[{ "id": 736, "label": "Developer Select", "options": { "remove": ["Developer Option", "Taco Option" ] }}]'
```

### HTTP Request

`PUT https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/elements/update`

### Request Body Parameters

-   `label` - String - The label of the Form Element that describes what the Form Element is.

-   `displayLabel` - Boolean - Whether or not the label should be displayed on the Form Element.

-   `sortOrder` - Number - Where the Form Element appears on the [Form](#form), lower numbers appear before higher numbers.

-   `colClass` - Number from 1 to 12, inclusive - How much width the Form Element should take up on the Form.

    - Lobbie uses the <a target="_blank" href="https://getbootstrap.com/docs/5.3/layout/grid/">Bootstrap 5 Grid System</a> so this number must be between 1 and 12, with 1 being less width and 12 being the full width of the page.
    
    - Some Form Elements have fixed widths and changing this number will not affect them.

-   `attribute` - String - A [Lobbie Form Attribute](#lobbie-form-attribute) to assign to this Form Element in order to structure its data for [prefilling](#prefill).

    - If you send an Attribute that does not exist Lobbie will create a new one tied to your Lobbie account.

    - Only 1 of the same Attribute can be present on a Form Template. If you try to add an Attribute to a 2nd Form Element, Lobbie will return an error.

    - To remove an Attribute from a Form Element, send the magic string `<delete>`, including the `<>` and Lobbie will irrecoverably remove the Attribute from the Form Element (this will *not* delete the Attribute itself).


-   `options` - Object - An object containing either or both of `add` and/or `remove` keys where:

    - `add` - List<String> - A list of strings that correspond to the labels for options that Lobbie will add to the Select/Radio Buttons Form Element. The `value` will be the `id` of the option that Lobbie creates.

    - `remove` - List<String> - A list of strings that correspond to the labels for options that Lobbie will remove from the Select/Radio Buttons Form Element. If there exists any [Form Answer](#form-answer) equal to the option that will be deleted, Lobbie will *not* remove the option.

## Update Single Form Element

Similar to the [Update Many Form Elements](#update-many-form-elements) endpoint above, however, with this endpoint you include the `id` of the Form Element to update in the endpoint route.

Not all attributes of a Form Element can be updated as changing some attributes would require a new version of the [Form Template](#form-template) to be issued.

For example, changing a Form Element from not-required to required is not permitted as this would impact Forms that have already been completed by Patients. 

> These commands will update the Form Element with an id of `736` and of type `Select` to first and and then remove the options with labels `Developer Option` and `Taco Option`.

> Additionally, the first request will change the `label` of the Form Element to `Taco Select` and the second request will change the label to `Developer Select`.

```shell
curl -X PUT \
    https://localhost:8443/lobbie/api/developer/v1/forms/templates/elements/update/736 \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '[{ "label": "Taco Select", "options": { "add": ["Developer Option", "Taco Option"] }}]'
```

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/elements/update/736 \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '[{ "label": "Developer Select", "options": { "remove": ["Developer Option", "Taco Option" ] }}]'
```

### HTTP Request

`PUT https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/templates/elements/update/<form_element_id>`

### Request Body Parameters

-   `label` - String - The label of the Form Element that describes what the Form Element is.

-   `displayLabel` - Boolean - Whether or not the label should be displayed on the Form Element.

-   `sortOrder` - Number - Where the Form Element appears on the [Form](#form), lower numbers appear before higher numbers.

-   `colClass` - Number from 1 to 12, inclusive - How much width the Form Element should take up on the Form.

    - Lobbie uses the <a target="_blank" href="https://getbootstrap.com/docs/5.3/layout/grid/">Bootstrap 5 Grid System</a> so this number must be between 1 and 12, with 1 being less width and 12 being the full width of the page.
    
    - Some Form Elements have fixed widths and changing this number will not affect them.

-   `attribute` - String - A [Lobbie Form Attribute](#lobbie-form-attribute) to assign to this Form Element in order to structure its data for [prefilling](#prefill).

    - If you send an Attribute that does not exist Lobbie will create a new one tied to your Lobbie account.

    - Only 1 of the same Attribute can be present on a Form Template. If you try to add an Attribute to a 2nd Form Element, Lobbie will return an error.

    - To remove an Attribute from a Form Element, send the magic string `<delete>`, including the `<>` and Lobbie will irrecoverably remove the Attribute from the Form Element (this will *not* delete the Attribute itself).


-   `options` - Object - An object containing either or both of `add` and/or `remove` keys where:

    - `add` - List<String> - A list of strings that correspond to the labels for options that Lobbie will add to the Select/Radio Buttons Form Element. The `value` will be the `id` of the option that Lobbie creates.

    - `remove` - List<String> - A list of strings that correspond to the labels for options that Lobbie will remove from the Select/Radio Buttons Form Element. If there exists any [Form Answer](#form-answer) equal to the option that will be deleted, Lobbie will *not* remove the option.


# Form Groups / Form Packets

## Get All Form Groups/Packets

When retrieving a [Form Group/Packet](#form-group) you can choose to call either the /groups or /packets endpoint. Both return the same response.

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/packets \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": [
        {
            "id": 8,
            "locationId": 1,
            "patientId": 10,
            "status": 0
        },
        {
            "id": 42,
            "locationId": 1,
            "patientId": 44,
            "status": 1
        }
    ]
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups`

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/packets`

### Query Parameters

-   `locationId` - number - Get [Form Groups/Packets](#form-group) in this Location. Optional. Default all Locations.

-   `patientId` - number - Get [Form Groups/Packets](#form-group) assigned to this Patient. Optional.

-   `practitionerId` - number - Get all [Form Groups/Packets](#form-group) for Patients assigned to this Practitioner. Optional.

-   `formTemplateIds` - string, comma-separated - Get all [Form Groups/Packets](#form-group) which include Forms with these [Form Templates](#form-template). Optional.

-   `formTemplateNames` - string, comma-separated - Get all [Form Groups/Packets](#form-group) which include Forms with these [Form Templates](#form-template). Optional.

-   `startDateTime` - number - Get all [Form Groups/Packets](#form-group) with a dueDate of this unix epoch time or greater. Optional. Default `now - 3 years`.

-   `endDateTime` - number - Get all [Form Groups/Packets](#form-group) with a dueDate less than this unix epoch time. Optional. Default `now + 1 year`.

-   `limit` - number - Return at most this number of [Form TemplatesGroups/Packetsform-template)group Optional. Default 10.

-   `page` - number - Offset the returned list of [Form Groups/Packets](#form-group) by this number \* `limit`. Optional. Default 0.

-   `isArchived` - boolean - If true, returns only archived [Form TemplatesGroups/Packetsform-template)group Optional. Default false.

-   `isActive` - boolean - If true, returns only active [Form TemplatesGroups/Packetsform-template)group Optional. Default true.

-   `status` - number / FormStatusEnum - Return only [Form Groups/Packets](#form-group) with this status. Optional. Default FormStatusEnum.STATUS_COMPLETE_NOT_COMPLETE.

## Get a Specific Form Group/Packet

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups/8 \
    -H "Authorization: Bearer $ACCESS_TOKEN" | jq .
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "id": 8,
        "locationId": 1,
        "patientId": 10,
        "dueDateUnix": 1691823599000,
        "completedOnUnix": 1690563654000,
        "createdOnUnixUtc": 1690558709000,
        "isComplete": true,
        "isArchived": false,
        "signedURL": "https://s3.us-east-2.amazonaws.com/lobbie-dev/fb924039-ed0f-4214-9196-a7fc33cd3619/49730717-2c3f-4e31-8686-02a08b08b853?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230728T170056Z&X-Amz-SignedHeaders=host&X-Amz-Expires=86399&X-Amz-Credential=<key>%2F20230728%2Fus-east-2%2Fs3%2Faws4_request&X-Amz-Signature=41e820e8404ac796a758eccca15cf660251310d281dd546bce2d35d4a7fa8a09",
        "forms": [
            {
                "locked": false,
                "id": 8,
                "isActive": true,
                "createdOnUnix": 1689349105000,
                "completedOnUnix": 1690563654000,
                "answers": {
                    1: "Taco",
                    2: "",
                    3: "Nacho",
                    4: "07/28/2023",
                    5: "taco@lobbie.com",
                    6: "3108675309",
                    7: "Yes",
                    8: "Lobbie",
                    9: "1234567890",
                    10: "123",
                    11: "4108675309",
                    12: "true",
                    14: "false",
                    16: "true",
                    19: "false",
                    20: {
                        "label": "Option 1",
                        "value": 20
                    },
                    "mapped": {
                        "first_name": "Taco",
                        "middle_name": "",
                        "last_name": "Nacho",
                        "date_of_birth": "07/28/2023",
                        "email": "taco@lobbie.com",
                        "mobile_phone": "3108675309"
                    }
                }
            }
        ]
    }
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups/<form_group_id>?isIncludeForms=true&isIncludeFormTemplates=true&isIncludePatient=true&isIncludeFormAnswers=true&isIncludeMappedFormAnswers=true&isIncludePDF=true&isIncludeLabeledFormAnswers=false`

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/packets/<form_packet_id>?isIncludeForms=true&isIncludeFormTemplates=true&isIncludePatient=true&isIncludeFormAnswers=true&isIncludeMappedFormAnswers=true&isIncludePDF=true&isIncludeLabeledFormAnswers=false`

### Query Parameters

- `isIncludeForms` - boolean - Default true. Include details about the Forms in this Form Group/Packet.

- `isIncludeFormTemplates` - boolean - Default true. Include details about the Form Templates associated with Forms in this Form Group/Packet. If this is true then `isIncludeForms` MUST be true or null.

- `isIncludeFormAnswers` - boolean - Default true. Include details about mapped and un-mapped Form Answers associated with Forms in this Form Group/Packet. If this is true then `isIncludeForms` MUST be true or null.

- `isIncludePatient` - boolean - Default true. Include details about the [patient](#get-a-specific-patient) to whom this Form Group/Packet is assigned.

- `isIncludePDF` - boolean - Default true. Include a url to a PDF version of this Form Group/Packet. See [Printing / PDF Generation](#printing-pdf-generation) for more details.

-   `isIncludeMappedFormAnswers` - If true, the response data will include a key, `mapped` located at `data.answers.mapped` which is map of key/value pairs where they keys are the [Lobbie Form Attributes](#lobbie-form-attribute) mapped to values of [Form Answers](#form-answer). Defaults to `true`.

-   `isIncludeLabeledFormAnswers` - If true, the response data will include a key, `labeled` located at `data.answers.labeled` which is map of key/value pairs where they keys are the labels of [Form Elements](#form-element) mapped to values of [Form Answers](#form-answer). Defaults to `false`.

<aside class="warning">
Be careful writing code that relies on the keys/labels returned from setting <code>isIncludeLabeledFormAnswers=true</code>. These labels may change as a Form Template is updated to fit the changing requirements of your clients.
</aside>


## Create a Form Group/Packet

> This creates a new Patient in Lobbie since there is no "id" passed in the "patient" object.

> When [prefilling](#prefill) Forms, Lobbie will give precdence to key/value pairs in the "prefill" object passed with the request in lieu of data from the Patient record. For example here, the [Form(s)](#form) created in this [Form Group/Packet](#form-group) will be prefilled with "Taco" and "Nacho" for any [Form Elements](#form-element) that hold the "first_name" and "last_name" Lobbie Form Attributes.

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        formTemplateGroupId: 1,
        locationId: 1,
        dueDateUnix: 1690811324454,
        patient: {
          firstName: \"Neato\",
          lastName: \"Burrito\",
          email: \"burrito@lobbie.com\",
          dateOfBirth: \"01/01/2000\"
        },
        prefill: {
          first_name: \"Taco\",
          last_name: \"Nacho\"
        }
    }"
```

> This request will assign the created [Form Group/Packet](#form-group) to the Patient with an `id` of 1.

> Because no `prefill` object is passed, Lobbie will first use any [Form Answers](#form-answer) from past [Forms](#form) that hold [Lobbie Form Attributes](#lobbie-form-attribute) of the "Patient" lifecycle and then data from the Patient record to prefill the new Forms that will be created.

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      formTemplateIds: [1],
      locationId: 1,
      dueDateUnix: 1690811324454,
      patient: {
        id: 1
      }
    }"
```

<aside class="warning">
Sending the same request again will result in a duplicate Form Group/Packet being created. Lobbie does not check if a Form Group/Packet already exists for the given Patient with a given due date.
</aside>

There are many ways to structure a request to create a new [Form Group/Packet](#form-group). However, they all rely on the presence of several things:

1. A `formTemplateGroupId` and/or a list of `formTemplateIds` so that Lobbie knows which [Form Templates](#form-template) to use.

2. A `locationId` so that Lobbie can assign the [Form Group/Packet](#form-group) to the correct location.

3. A `dueDateUnix`, which Lobbie uses to set the due date of the [Form Group/Packet](#form-group).

    a. Lobbie uses the time zone associated with the `location` to adjust this unix-epoch time to the correct due date.

    b. Due dates are adjusted to end-of-day, meaning that the times for all due dates will be 11:59:59 PM on the day the [Form Group/Packet](#form-group) is due.

    c. Patients will not be able to access Form Groups/Packets once the due date is passed.

4. A `patient` object which must contain an `id` key to assign the [Form Group/Packet](#form-group) to an existing Patient. Otherwise, Lobbie will try to create a new Patient before proceeding.

5. Finally, optionally, you may pass a `prefill` object which is an object of [LobbieFormAttribute](#lobbie-form-attribute)/[FormAnswer](#form-answer) key/value pairs.

    a. Lobbie uses the key attributes to find [Form Elements](#form-element) in the [Form Templates](#form-template) you pass to prefill Form Answers with the values in the object.

    b. For example if you were to pass, `{ prefill: { first_name: "Taco" } }`, Lobbie would find all Form Elements with the [Lobbie Form Attribute](#lobbie-form-attribute) of "first_name" and set the answer to that Form Element as "Taco".

    c. Lobbie [prefills](#prefill) [Forms](#form) from two other sources, past Forms that have [Lobbie Form Attributes](#lobbie-form-attribute) with the "Patient" lifecycle and data from the Patient records directly. However, any key/values you pass here take precedence over the other two methods.

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "Forms created.",
    "data": {
        "id": 52,
        "locationId": 1,
        "patientId": 1,
        "dueDateUnix": 1690873199000,
        "urls": {
            "patient": "https://localhost:3000/forms/patient?token=oso9NmuIqa2lqhTVNaaaBkadErMrzsXG",
            "staff": "https://localhost:3000/home/patients/fb924039-ed0f-4214-9196-a7fc33cd3619/1"
        }
    }
}
```

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups`

## Update a Form Group/Packet

Add or remove Forms from a [Form Group/Packet](#form-group) by passing [Form Template](#form-template) ids and/or a [Form Template Group](#form-template-group) id, change the due date and/or change the complete/incomplete status of the Form Group/Packet.

> This command will update the Form Group/Packet with `id` of 1 to have Forms created from Form Templates 1 and 4, to have a new due date and to be marked as complete.

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 1,
      formTemplateIds: [1, 4],
      dueDateUnix: 1690815877454,
      status: 0 # 0 => FormStatusEnum.COMPLETE
    }"
```

<aside class="warning">
  If you include a value of <b>status: 0</b> in the request body, Lobbie will <i>forcefully</i> complete the Form Group/Packet and all forms within it.
  <div style="margin-top: 10px; margin-bottom: 10px;">
  This will trigger a *staff-complete* webhook sent to the endpoint you define in the Lobbie Developer Settings.
  </div>
</aside>

> The Form Group/Packet with `id` of 1 is updated to include Forms from Form Templates 1 and 4, to have a new due date and to be marked as complete.

```json
{
    "success": true,
    "message": "Form group updated.",
    "data": {
        "id": 1,
        "locationId": 1,
        "patientId": 3,
        "dueDateUnix": 1690815877454,
        "completedOnUnix": 1692028292189,
        "createdOnUnixUtc": 1690558706000,
        "isComplete": false,
        "isArchived": false,
        "forms": [
            {
                "id": 53,
                "isActive": true,
                "createdOnUnix": 1690816734000
            },
            {
                "id": 54,
                "isActive": true,
                "createdOnUnix": 1690816734000
            }
        ]
    }
}
```

> This command will update the Form Group/Packet with `id` of 1 to have only have Forms from Form Template 1, to have a new due date and to be marked as incomplete.

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 1,
      formTemplateIds: [1],
      dueDateUnix: 1690558706000,
      status: 1 # 1 => FormStatusEnum.INCOMPLETE
    }"
```

> The Form with Form Template 4 is removed and the due date is changed:

```json
{
    "success": true,
    "message": "Form group updated.",
    "data": {
        "id": 1,
        "locationId": 1,
        "patientId": 3,
        "isComplete": false,
        "dueDateUnix": 1690558706000,
        "completedOnUnix": null,
        "createdOnUnixUtc": 1690558706000,
        "isArchived": false,
        "forms": [
            {
                "id": 53,
                "isActive": true,
                "createdOnUnix": 1690816734000
            }
        ]
    }
}
```

<aside class="warning">
  You MUST pass the existing <code>formTemplateIds</code> on the Form Template Group to retain existing Form Templates.
  <div style="margin-top: 10px; margin-bottom: 10px;">
    Otherwise those Form Templates will be removed from the Form Template Group.
  </div>
  <div>
    For example, if you were to pass an <code>formTemplateIds</code> as an empty list, Lobbie would remove all Form Templates from the Form Template Group.
  </div>
</aside>

### HTTP Request

`PUT https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups`

## Archive or Un-archive a Form Group/Packet

Archive a [Form Group/Packet](#form-group) by passing one or more ids of a Form Group/Packet. Archived Form Group/Packets are moved to a separate view on the Forms Dashboard on the Lobbie UI.

> This command will archive the Form Group/Packet with `id` of 1.

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups/archive \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 1,
    }"
```

```json
{
    "success": true,
    "message": "Archived: 1 - Un-archived: none",
    "data": [{
        "id": 1,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": true
    }]
}
```

> This command will archive the Form Groups/Packets with `ids` of 2, 3 and 4.

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/packets/archive \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      ids: [2, 3, 4],
    }"
```

```json
{
    "success": true,
    "message": "Archived: 2, 3 - Un-archived: 4",
    "data": [{
        "id": 2,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": true
    }, 
    {
        "id": 3,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": true
    },
    {
        "id": 4,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": false
    }]
}
```

> You can combine both the `id` and `ids` keys, Lobbie will only perform one archive/un-archive action per passed id.

```shell
curl -X PUT \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/packets/archive \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 2,
      ids: [2, 3, 4],
    }"
```

```json
{
    "success": true,
    "message": "Archived: 2, 3 - Un-archived: 4",
    "data": [{
        "id": 2,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": true
    }, 
    {
        "id": 3,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": true
    },
    {
        "id": 4,
        "locationId": 1,
        "patientId": 3,
        "createdOnUnixUtc": 1690558706000,
        "dueDateUnix": 1690558706000,
        "isComplete": false,
        "completedOnUnix": null,
        "isArchived": false
    }]
}
```

### HTTP Request

`PUT https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/groups/archive`

# Forms

The structure of a [Form](#form) returned from Lobbie includes [Form Answers](#form-answer).

Form Answers can be found under the key `data.answers` where each key/value pair is equal to `<form_answer_id>` / `<form_answer_value>`.

Answers that are mapped to structured fields using [Lobbie Form Attribute](#lobbie-form-attribute) can be found at the `data.answers.mapped` key.
    
* If the [Form Element](#form-element) behind a [Form Answer](#form-answer) has no **attribute**, it will not be included in these results.
    
* If more than one Form Element exists with the same **attribute** mapping in the Form, then the attribute/key will be suffixed with an index.

Answers that are mapped by the label of the [Form Element](#form-element) to the answer value can be found at the `data.answers.labeled` key.

* If the [Form Element](#form-element) behind a [Form Answer](#form-answer) has no **label**, it will not be included in these results.
    
* If more than one Form Element exists with the same **label** in the Form, then the label/key will be suffixed with an index.

<aside class="notice">
Check the query params section below for more information about retrieving mapped and labeled form answers for a form.
</aside>

## Get a Specific Form

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/<form_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "message": "Form with mapped and unmapped answers.",
  "data": {
    "id": 328,
    "isActive": true,
    "createdOnUnix": 1702656769000,
    "formTemplateId": 15,
    "version": 10,
    "answers": {
      "1765": "vlad",
      "1766": "tester",
      "1767": "taco@lobbie.com",
      "1769": "235123513",
      "1770": "1324641234",
      "1771": "1235612345",
      "1772": "3215132532",
      "1773": {
        "label": "Latino/a",
        "value": "7514",
        "weightedValue": 10
      },
      "1774": {
        "label": "Black/African American",
        "value": "7520",
        "weightedValue": 10
      },
      "1775": "sdgadsg",
      "1776": {
        "label": "French",
        "value": "7537",
        "weightedValue": 20
      },
      "1777": {
        "label": "Spanish",
        "value": "7762",
        "weightedValue": 10
      },
      "1778": {
        "label": "Homeless",
        "value": "7982",
        "weightedValue": 10
      },
      "1779": {
        "label": "Seasonal",
        "value": "7992",
        "weightedValue": 10
      },
      "1780": {
        "label": "Female",
        "value": "7994",
        "weightedValue": 0
      },
      "1781": {
        "label": "Female",
        "value": "8003",
        "weightedValue": 0
      },
      "1782": {
        "label": "Male",
        "value": "8008",
        "weightedValue": 10
      },
      "1783": {
        "label": "Bisexual",
        "value": "8015",
        "weightedValue": 10
      },
      "1784": {
        "label": "Burn",
        "value": "8024",
        "weightedValue": 0
      },
      "1785": "dgasd",
      "1786": "adsgads",
      "1787": {
        "label": "Grandparent",
        "value": "8051",
        "weightedValue": 20
      },
      "1788": "3426234634",
      "1789": "234623462",
      "1790": "3165234623",
      "1791": "gadsgadsg",
      "1792": "dbbafds",
      "1793": {
        "label": "Grandparent",
        "value": "8098",
        "weightedValue": 20
      },
      "1794": "25534",
      "1795": "3462432346",
      "1796": "2346234623",
      "1797": "1 E Baltimore St",
      "1799": "33",
      "1800": "Baltimore",
      "1801": "21202",
      "1802": "no",
      "1803": "dsgasdg",
      "1804": "",
      "1818": {
        "label": "2",
        "value": "8150",
        "weightedValue": 2
      },
      "1819": "11/12/1999",
      "1820": "12/15/2023",
      "mapped": {
        "first_name": "vlad",
        "last_name": "tester",
        "email": "taco@lobbie.com",
        "social_security_number": "235123513",
        "home_phone": "1324641234",
        "mobile_phone": "1235612345",
        "work_phone": "3215132532",
        "ethnic_group": {
          "label": "Latino/a",
          "value": "7514",
          "weightedValue": 10
        },
        "race": {
          "label": "Black/African American",
          "value": "7520",
          "weightedValue": 10
        },
        "race_2": "sdgadsg",
        "primary_language": {
          "label": "French",
          "value": "7537",
          "weightedValue": 20
        },
        "secondary_language": {
          "label": "Spanish",
          "value": "7762",
          "weightedValue": 10
        },
        "housing_status": {
          "label": "Homeless",
          "value": "7982",
          "weightedValue": 10
        },
        "migrant_status": {
          "label": "Seasonal",
          "value": "7992",
          "weightedValue": 10
        },
        "gender": {
          "label": "Female",
          "value": "7994",
          "weightedValue": 0
        },
        "legal_sex": {
          "label": "Female",
          "value": "8003",
          "weightedValue": 0
        },
        "sex_assigned_at_birth": {
          "label": "Male",
          "value": "8008",
          "weightedValue": 10
        },
        "sexual_orientation": {
          "label": "Bisexual",
          "value": "8015",
          "weightedValue": 10
        },
        "service_name": {
          "label": "Burn",
          "value": "8024",
          "weightedValue": 0
        },
        "parent_legal_guardian_first_name": "dgasd",
        "parent_legal_guardian_last_name": "adsgads",
        "parent_legal_guardian_relationship": {
          "label": "Grandparent",
          "value": "8051",
          "weightedValue": 20
        },
        "parent_legal_guardian_home_phone": "234623462",
        "parent_legal_guardian_work_phone": "3165234623",
        "parent_legal_guardian_first_name_2": "gadsgadsg",
        "parent_legal_guardian_last_name_2": "dbbafds",
        "parent_legal_guardian_relationship_2": {
          "label": "Grandparent",
          "value": "8098",
          "weightedValue": 20
        },
        "parent_legal_guardian_home_phone_2": "3462432346",
        "parent_legal_guardian_work_phone_2": "2346234623",
        "address1": "1 E Baltimore St",
        "state": "MD",
        "city": "Baltimore",
        "postal_code": "21202",
        "insurance_name": "dsgasdg",
        "insurance_id": ""
      },
      "labeled": {
        "First Name": "vlad",
        "Last Name": "tester",
        "Email": "taco@lobbie.com",
        "Social Security Number": "235123513",
        "Home Phone": "1324641234",
        "Mobile Phone": "1235612345",
        "Work Phone": "3215132532",
        "Ethnic Group": {
          "label": "Latino/a",
          "value": "7514",
          "weightedValue": 10
        },
        "Race": {
          "label": "Black/African American",
          "value": "7520",
          "weightedValue": 10
        },
        "Other Race": "sdgadsg",
        "Primary Language": {
          "label": "French",
          "value": "7537",
          "weightedValue": 20
        },
        "Secondary Language": {
          "label": "Spanish",
          "value": "7762",
          "weightedValue": 10
        },
        "Housing Status": {
          "label": "Homeless",
          "value": "7982",
          "weightedValue": 10
        },
        "Migrant Status": {
          "label": "Seasonal",
          "value": "7992",
          "weightedValue": 10
        },
        "Gender Identity": {
          "label": "Female",
          "value": "7994",
          "weightedValue": 0
        },
        "Legal Sex": {
          "label": "Female",
          "value": "8003",
          "weightedValue": 0
        },
        "Sex Assigned at Birth": {
          "label": "Male",
          "value": "8008",
          "weightedValue": 10
        },
        "Sexual Orientation": {
          "label": "Bisexual",
          "value": "8015",
          "weightedValue": 10
        },
        "Hospital Service": {
          "label": "Burn",
          "value": "8024",
          "weightedValue": 0
        },
        "Parent 1 Parent/Legal Guardian First Name": "dgasd",
        "Parent 1 Parent/Legal Guardian Last Name": "adsgads",
        "Parent 1 Relationship to Patient": {
          "label": "Grandparent",
          "value": "8051",
          "weightedValue": 20
        },
        "Parent 1 Home Phone Number": "3426234634",
        "Parent 1 Mobile Phone Number": "234623462",
        "Parent 1 Work Phone Number": "3165234623",
        "Parent 2 Parent/Legal Guardian First Name 2": "gadsgadsg",
        "Parent 2 Parent/Legal Guardian Last Name 2": "dbbafds",
        "Parent 2 Relationship to Patient 2": {
          "label": "Grandparent",
          "value": "8098",
          "weightedValue": 20
        },
        "Parent 2 Home Phone Number": "25534",
        "Parent 2 Mobile Phone Number": "3462432346",
        "Parent 2 Work Phone Number": "2346234623",
        "Address 1": "1 E Baltimore St",
        "State": "MD",
        "City": "Baltimore",
        "Zip Code": "21202",
        "Do you have insurance?": "no",
        "Insurance 1 Insurance Name 1": "dsgasdg",
        "Insurance 1 ID #": "",
        "Hours": {
          "label": "2",
          "value": "8150",
          "weightedValue": 2
        },
        "Dates Worked": "11/12/1999",
        "Test test": "12/15/2023"
      }
    }
  }
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/<form_id>`

### Request Query String Parameters

-   `isIncludeMappedFormAnswers` - If true, the response data will include a key, `mapped` located at `data.answers.mapped` which is map of key/value pairs where they keys are the [Lobbie Form Attributes](#lobbie-form-attribute) mapped to values of [Form Answers](#form-answer). Defaults to `true`.

-   `isIncludeLabeledFormAnswers` - If true, the response data will include a key, `labeled` located at `data.answers.labeled` which is map of key/value pairs where they keys are the labels of [Form Elements](#form-element) mapped to values of [Form Answers](#form-answer). Defaults to `false`.

<aside class="warning">
Be careful writing code that relies on the keys/labels returned from setting <code>isIncludeLabeledFormAnswers=true</code>. These labels may change as a Form Template is updated to fit the changing requirements of your clients.
</aside>

# Staff

## Get All Staff

When retrieving a [Staff](#staff) you can use the /staff endpoint.

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": [
        {
            "id": 8,
            "firstName":"John",
            "lastName":"Doe",
            "email":"johndoe@lobbie.com",
            "locationIds": "1,2",
            "roleGroupIds":"1,2",
            "active": true
        },
        {
            "id": 8,
            "firstName":"Jane",
            "lastName":"Doe",
            "email":"janedoe@lobbie.com",
            "locationIds": "1,2",
            "roleGroupIds":"1,2",
            "active": true
        },
    ]
}
```

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff`

## Get a Specific Staff

```shell
curl -X GET \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff/<staff_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
            "id": 8,
            "firstName":"John",
            "lastName":"Doe",
            "email":"johndoe@lobbie.com",
            "locationIds": "1,2",
            "roleGroupIds":"1,2",
            "active": true
        }
}
```

This endpoint retrieves a specific [Staff](#staff).

### HTTP Request

`GET https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff/<staff_id>`

### URL Parameters

| Parameter | Description                                                           |
| --------- | --------------------------------------------------------------------- |
| ID        | The ID of the [Staff](#staff) to retrieve |


## Create a Staff

```shell
curl "https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff" \
  -X POST \
  -H "Authorization: $LOBBIE_ACCESS_TOKEN"
  -H "Content-Type: application/json"
  --data '{
    "firstName":"John",
            "lastName":"Doe",
            "email":"johndoe@lobbie.com",
            "locationIds": "1,2", # IDs of Locations created in Lobbie who are assigned to this Staff Member. Staff members only have UI access to Locations to which they are assigned.
            "roleGroupIds":"1,2",
            "active": true
  }'
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
            "id": 8,
            "firstName":"John",
            "lastName":"Doe",
            "email":"johndoe@lobbie.com",
            "locationIds": "1,2",
            "roleGroupIds":"1,2",
            "active": true
        }
}
```

This endpoint creates a Staff in Lobbie.

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff`

## Update a Staff

```shell
curl "https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff" \
  -X POST \
  -H "Authorization: $LOBBIE_ACCESS_TOKEN"
  -H "Content-Type: application/json"
  --data '{
    "id": 8,                   # The ID of the Staff to update.
    ""firstName":"John",
    "lastName":"Doe",
    "email":"johndoe@lobbie.com",
    "locationIds": "1,2", # IDs of Locations created in Lobbie who are assigned to this Staff Member. Staff members only have UI access to Locations to which they are assigned.
    "roleIds":"1,2",
    "active": 1
  }'
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
            "id": 8,
            "firstName":"John",
            "lastName":"Doe",
            "email":"johndoe@lobbie.com",
            "locationIds": "1,2",
            "roleIds":"1,2",
            "active": 1
        }
}
```

This endpoint updates a Staff in Lobbie.

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/staff`

# Printing / PDF Generation

## Creating a PDF

When creating a PDF of a [Form](#form), [Form Group/Packet](#form-group), [Form Template](#form-template) or [Form Template Group](#form-template-group) you can choose to call either the /print or /pdf endpoint. Both return the same response.

One of these endpoints must be called before a PDF can be retrieved.

NOTE: Forms and Form Groups/Packets do NOT need to be completed in order to be printed. This endpoint can be called at any time.

<aside class="notice">
    Setting <code>isPatient</code> in the request body to <code>false</code> will generate a PDF with Staff-only fields displayed. Defaults to <code>true</code>.
</aside>

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/print/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formTemplateId: 1, \
      isPatient: true
    }"
```
> Same request as above, but the endpoint has changed.
```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formTemplateId: 1, \
      isPatient: true
    }"
```

> The request body MUST include only one of formTemplateId, formTemplateGroupId, formId, formGroupId or formGroupIds. For example:

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formId: 1, \
      isPatient: true
    }"

curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formTemplateGroupId: 1, \
      isPatient: true
    }"

curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formGroupId: 1, \
      isPatient: true
    }"

curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formGroupIds: [1, 2], \
      isPatient: true
    }"

curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formGroupId: [1], \
      formTemplateId: 1 \
      isPatient: true
    }"
```

> The above commands returns JSON structured like this:

```json
{
    "success": true,
    "message": "Preparing PDF",
    "data": {
        "s3ObjectPath": "fb924039-ed0f-4214-9196-a7fc33cd3619/0b313bb1-c18c-4e6a-a084-432b6f14ee5a"
    }
}
```

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/print/create`

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/create`

### Request Body Parameters

-   `isPatient` - If true, the PDF will not include Staff-only fields. Defaults to `true`.

**One of:**

- `formId` 

- `formTemplateId` 

- `formTemplateGroupId` 

- `formGroupId` 

- `formGroupIds`

- `formGroupId` AND `formTemplateId` -> This prints a [Form](#form)

## Retrieving a PDF

Call this endpoint after sending a POST request to either the /print or /pdf endpoints, passing the `s3ObjectPath` returned from the respective API call.

> Both API calls here use the `s3ObjectPath` returned from the above [/create](#creating-a-pdf) call.

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/print/retrieve \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        s3ObjectPath: \"fb924039-ed0f-4214-9196-a7fc33cd3619/0b313bb1-c18c-4e6a-a084-432b6f14ee5a\"
    }"
```

```shell
curl -X POST \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/retrieve \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        s3ObjectPath: \"fb924039-ed0f-4214-9196-a7fc33cd3619/0b313bb1-c18c-4e6a-a084-432b6f14ee5a\"
    }"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "s3ObjectPath": "fb924039-ed0f-4214-9196-a7fc33cd3619/0b313bb1-c18c-4e6a-a084-432b6f14ee5a",
        "signedURL": "https://signed_url_from_aws"
    }
}
```

### HTTP Request

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/print/retrieve`

`POST https://api-sandbox.lobbie.com/lobbie/api/developer/v1/forms/pdf/retrieve`

# Deleting/Destroying Data in Lobbie 

<aside class="warning">
  The following endpoints are DESTRUCTIVE. Lobbie will ***NOT*** be able to recover data for you if you choose to invoke them.
</aside>

## Deleting/Destroying a Patient

This endpoint will delete [Patient](#patients) for the given `id`. An optional `force` argument can be provided to force Lobbie to destroy all records associated with the Patient.

If you choose to not pass the `force` argument, or set `force` as `false`, Lobbie will return an error if any associations are detected. Default false.

You may also pass a boolean `isDestroyRelationships` argument which, if true, will instruct Lobbie to destroy any parent and child patients. Default false.

The response data will include a json structure of the just-deleted record, calculated before the data was destroyed.

```shell
curl -X DELETE \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/patient \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        id: 1, force: true
    }"
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "message": "Patient and associated appointments, forms, addresses, nps reviews and notifications deleted.",
  "data": {
    "active": true,
    "email": "seed-614559@lobbie.com",
    "firstName": "Taco",
    "lastName": "Lobbie",
    "mobilePhone": "1238675309",
    "dateOfBirth": "2/6/1987",
    "id": 42,
    "createdOn": "2024-02-06T18:01:45.000Z",
    "lastUpdatedOn": "2024-02-07T19:29:38.000Z",
    "isEmailConfirmed": true,
    "isPhoneConfirmed": false,
    "receiveEmail": true,
    "receiveSms": true,
    "name": "Taco Lobbie",
    "isMinimumAgeConfirmedAccount": true,
    "sexAssignedAtBirth": 1,
    "legalSex": 0,
    "intakeMethod": "UNKNOWN",
    "gender": "non-binary",
    "patientPrimaryAddress": {
      "address1": "1 E Baltimore St.",
      "city": "Baltimore",
      "postalCode": "21202",
      "latitude": 38.86293745172355,
      "longitude": -77.05874058689015,
      "addressNote": "Tacos are delicious.",
      "id": 45,
      "createdOn": "2024-02-06T18:01:45.000Z",
      "lastUpdatedOn": "2024-02-06T18:01:45.000Z",
      "stateProvince": {
        "shortName": "MD",
        "name": "Maryland",
        "id": 33,
        "createdOn": "2024-02-06T18:01:23.000Z",
        "lastUpdatedOn": "2024-02-06T18:01:23.000Z"
      },
      "fullAddress": "1 E Baltimore St., Baltimore, MD 21202",
      "active": true,
      "isPrimary": true
    },
    "parents": [
      {
        "active": true,
        "lobbieIntegrationUuid": "98cbc0c8-76ce-48b8-8556-e3d0ec47d44a",
        "email": "seed-614559@lobbie.com",
        "firstName": "Test",
        "lastName": "Lobbie",
        "mobilePhone": "1238675309",
        "dateOfBirth": "10/8/1977",
        "isPatient": true,
        "id": 43,
        "createdOn": "2024-02-06T18:01:45.000Z",
        "lastUpdatedOn": "2024-02-06T18:01:45.000Z",
        "patientId": null,
        "patient": true,
        "accountPatientId": 43,
        "isEmailConfirmed": true,
        "isPhoneConfirmed": false,
        "receiveEmail": true,
        "receiveSms": true,
        "name": "Test Lobbie",
        "isMinimumAgeConfirmedAccount": true,
        "sexAssignedAtBirth": 2,
        "legalSex": 0,
        "intakeMethod": "UNKNOWN",
        "gender": "non-binary",
        "patientPrimaryAddress": {
          "address1": "1 E Baltimore St.",
          "city": "Baltimore",
          "postalCode": "21202",
          "latitude": 38.86293745172355,
          "longitude": -77.05874058689015,
          "addressNote": "Tacos are delicious.",
          "updateGeo": false,
          "id": 46,
          "createdOn": "2024-02-06T18:01:45.000Z",
          "lastUpdatedOn": "2024-02-06T18:01:45.000Z",
          "stateProvince": {
            "shortName": "MD",
            "name": "Maryland",
            "id": 33,
            "createdOn": "2024-02-06T18:01:23.000Z",
            "lastUpdatedOn": "2024-02-06T18:01:23.000Z"
          },
          "fullAddress": "1 E Baltimore St., Baltimore, MD 21202",
          "active": true,
          "isPrimary": true
        },
        "parents": [],
        "children": [
          {
            "id": 42
          }
        ],
        "childRelationshipName": null,
        "parentRelationshipName": "Parent",
        "isPrimary": true
      }
    ],
    "children": [],
    "practitioners": [
      {
        "canSelfSchedule": true,
        "receiveEmail": true,
        "receiveSms": true,
        "isNotifyOnFormsComplete": true,
        "isCanUnlockForms": false,
        "lobbieIntegrationUuid": "54377536-d6ed-48d7-b582-067480517f37",
        "activated": true,
        "lastLogin": "2/7/2024 3:17PM UTC",
        "lastLoginIpAddress": "192.168.65.1",
        "active": true,
        "email": "admin_user_practitioner_developer@lobbie.com",
        "firstName": "Devon",
        "lastName": "DeveloperAdminUserPractitioner",
        "mobilePhone": "1238675309",
        "isPatient": false,
        "id": 11,
        "createdOn": "2024-02-06T18:01:28.000Z",
        "lastUpdatedOn": "2024-02-07T15:17:22.000Z",
        "isTOTP2FAVerified": false,
        "name": "Adm. Devon DeveloperAdminUserPractitioner",
        "title": "Adm.",
        "position": "Front Desk Associate",
        "locationIds": "1,2,3",
        "roleGroupIds": "3,1,2,5",
        "telehealthUrl": "https://short-dev.lobbie.com/MEf7dFmWE_YjzQQQ",
        "accountPatientIds": [
          42
        ]
      }
    ]
  }
}
```

### HTTP Request

`DELETE https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/patient`

Body:

* `id` - Required. The id of the [Patient](#patients) to destroy.

* `force` - Optional. Default false. If true will destroy all other entities related to the Patient, with the exception of child and parent Patients.

* `isDestroyRelationships` - Optional. Default false. If true, when paired with `force=true`, will destroy all child and parent Patients related to the Patient being destroyed.


## Deleting/Destroying a Form Group/Packet

Like the patient-delete endpoint, you can also pass an optional `force` argument with the request to. When true, `force` will instruct Lobbie to destroy all Forms associated with this [Form Group/Packet](#form-group).

If false (default), Lobbie will return an error and prevent destruction of the Form Group/Packet if any Forms are associated with the Form Group/Packet.

The response data will include a json structure of the just-deleted record, calculated before the data was destroyed.

You may use either of the below endpoints, they are aliases of each other.

```shell
curl -X DELETE \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/form/group \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        id: 1, force: true
    }"
```

OR

```shell
curl -X DELETE \
    https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/form/packet \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        id: 1, force: true
    }"
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "message": "Form group/packet deleted.",
  "data": {
    "id": 4,
    "locationId": 1,
    "patientId": 6,
    "createdOnUnixUtc": 1707242492000,
    "dueDateUnix": 1709711999000,
    "isComplete": false,
    "completedOnUnix": null,
    "isArchived": false,
    "patient": {
      "active": true,
      "email": "seed-96262@lobbie.com",
      "firstName": "Lobbie",
      "lastName": "Test",
      "mobilePhone": "1238675309",
      "dateOfBirth": "5/7/2008",
      "id": 6,
      "createdOn": "2024-02-06T18:01:32.000Z",
      "lastUpdatedOn": "2024-02-06T18:01:33.000Z",
      "isEmailConfirmed": true,
      "isPhoneConfirmed": false,
      "receiveEmail": true,
      "receiveSms": true,
      "name": "Lobbie Test",
      "isMinimumAgeConfirmedAccount": true,
      "sexAssignedAtBirth": 1,
      "legalSex": 0,
      "intakeMethod": "UNKNOWN",
      "gender": "female",
      "patientPrimaryAddress": {
        "address1": "1 E Baltimore St.",
        "city": "Baltimore",
        "postalCode": "21202",
        "latitude": 38.86293745172355,
        "longitude": -77.05874058689015,
        "addressNote": "Tacos are delicious.",
        "id": 9,
        "createdOn": "2024-02-06T18:01:32.000Z",
        "lastUpdatedOn": "2024-02-06T18:01:33.000Z",
        "stateProvince": {
          "shortName": "MD",
          "name": "Maryland",
          "id": 33,
          "createdOn": "2024-02-06T18:01:23.000Z",
          "lastUpdatedOn": "2024-02-06T18:01:23.000Z"
        },
        "fullAddress": "1 E Baltimore St., Baltimore, MD 21202",
        "active": true,
        "isPrimary": true
      },
      "parents": [],
      "children": [],
      "practitioners": []
    },
    "forms": [
      {
        "id": 4,
        "isActive": true,
        "createdOnUnix": 1706637690000
      }
    ]
  }
}
```

### HTTP Request

`DELETE https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/form/group`

`DELETE https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/form/packet`

Body:

* `id` - Required. The id of the [Form Group/Packet](#form-group) to destroy.

* `force` - Optional. Default false. If true will destroy all Forms related to the Form Group/Packet.


## Deleting/Destroying a Form

There is no `force` argument available for this endpoint.

```shell
curl -X DELETE \
    https://localhost:8443/lobbie/api/developer/v1/destroy/form \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '{ "id": 20, "force": true }' | jq .
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "message": "Form deleted.",
  "data": {
    "id": 20,
    "isActive": true,
    "createdOnUnix": 1705168890000,
    "formTemplateId": 4,
    "version": 1,
    "answers": {
      "mapped": {}
    }
  }
}
```

### HTTP Request

`DELETE https://api-sandbox.lobbie.com/lobbie/api/developer/v1/destroy/form`

Body:

* `id` - Required. The id of the [Form](#form) to destroy.

# Enums

## FormStatusEnum

Used in:

-   [Get all Form Groups / Packets](#get-all-form-groups-packets)

```shell
STATUS_COMPLETE =              0
STATUS_NOT_COMPLETE =          1
STATUS_COMPLETE_NOT_COMPLETE = 2
STATUS_EXPIRED =               3
```

## LegalSexEnum

Used in:

-   [Get all Patients](#get-all-patients)

-   [Get a Specific Patient](#get-a-specific-patient)

```shell
UNKNOWN = 0
MALE    = 1
FEMALE  = 2
```

## RoleGroupEnum

Used in:

-   [Get All Staff](#staff)
-   [Get a Specific Staff](#staff)
-   [Create staff](#staff)
-   [Update staff](#staff)

```shell
USER_ROLE =          1;
PRACTITIONER_ROLE =  2;
ADMINISTRATOR_ROLE = 3;
DEVELOPER_ROLE =     5;
REPORTING_ROLE =     6;
SUPERVISING_ROLE =   7;
```

## SexAssignedAtBirthEnum

Used in:

-   [Get all Patients](#get-all-patients)

-   [Get a Specific Patient](#get-a-specific-patient)

```shell
UNKNOWN =       0
MALE =          1
FEMALE =        2
OTHER =         3
NOT_SPECIFIED = 4
```

# Webhooks

When you set up your integration with Lobbie you have the option of also setting up Lobbie webhooks. Webhooks are HTTP requests that Lobbie will send to your application when something happens in Lobbie.

Webhooks are fully configurable, meaning you choose the request method (GET, POST, PUT, PATCH) that Lobbie sends, how Lobbie authenticates with your application (OAuth2 or API Key), and where in the request Lobbie includes those authentication credentials (request body, request headers, query string, etc.).

The settings for your webhooks are maintained on an Account level, meaning that the settings will be global to your organization's Lobbie Account, **not** your individual Lobbie account.

If you choose to send a response to the webhook, Lobbie keeps basic logs of each webhook request and will store the response code + reason/message, in addition to the request method, url and body to help you with debugging.

Lobbie currently supports 2 webhooks, both of which are optional:

1. When a [Patient](#patients) completes a [Form Group/Packet](#form-group)

2. When a Staff member completes a Form Group/Packet

## A Patient completes Form Group/Packet

If the request method you select for this endpoint is GET, Lobbie will include `formGroupId`, `patientId` and, if applicable, `appointmentId` as query parameters in the request.

If the request method you select for this endpoint is **not** a GET, Lobbie will include the following in the request body to right:

<aside class="notice">
Form Answers that belong to Staff-only Form Elements are <b>not</b> included in this request.
</aside>

```json
{
        "id": 8,
        "locationId": 1,
        "patientId": 10,
        "dueDateUnix": 1691823599000,
        "completedOnUnix": 1690563654000,
        "createdOnUnixUtc": 1690558709000,
        "isComplete": true,
        "isArchived": false,
        "signedURL": "https://s3.us-east-2.amazonaws.com/lobbie-dev/fb924039-ed0f-4214-9196-a7fc33cd3619/49730717-2c3f-4e31-8686-02a08b08b853?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230728T170056Z&X-Amz-SignedHeaders=host&X-Amz-Expires=86399&X-Amz-Credential=<key>%2F20230728%2Fus-east-2%2Fs3%2Faws4_request&X-Amz-Signature=41e820e8404ac796a758eccca15cf660251310d281dd546bce2d35d4a7fa8a09",
        "forms": [
            {
                "locked": false,
                "id": 8,
                "formTemplateId": 1,
                "version": 3,
                "isActive": true,
                "createdOnUnix": 1689349105000,
                "completedOnUnix": 1690563654000,
                "answers": {
                    1: "Taco",
                    2: "",
                    3: "Nacho",
                    4: "07/28/2023",
                    5: "taco@lobbie.com",
                    6: "3108675309",
                    7: "Yes",
                    8: "Lobbie",
                    9: "1234567890",
                    10: "123",
                    11: "4108675309",
                    12: "true",
                    14: "false",
                    16: "true",
                    19: "false",
                    20: {
                        label: "Option 1",
                        value: 20
                    },
                    "mapped": {
                        "first_name": "Taco",
                        "middle_name": "",
                        "last_name": "Nacho",
                        "date_of_birth": "07/28/2023",
                        "email": "taco@lobbie.com",
                        "mobile_phone": "3108675309"
                    }
                }
            }
        ]
    }
```

## A Staff member completes Form Group/Packet

If the request method you select for this endpoint is GET, Lobbie will include `formGroupId`, `patientId` and, if applicable, `appointmentId` as query parameters in the request.

If the request method you select for this endpoint is **not** a GET, Lobbie will include the following in the request body to right:

<aside class="notice">
Form Answers that belong to Staff-only Form Elements will be included in this request.
</aside>

```json
{
        "id": 8,
        "locationId": 1,
        "patientId": 10,
        "dueDateUnix": 1691823599000,
        "completedOnUnix": 1690563654000,
        "createdOnUnixUtc": 1690558709000,
        "isComplete": true,
        "isArchived": false,
        "signedURL": "https://s3.us-east-2.amazonaws.com/lobbie-dev/fb924039-ed0f-4214-9196-a7fc33cd3619/49730717-2c3f-4e31-8686-02a08b08b853?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230728T170056Z&X-Amz-SignedHeaders=host&X-Amz-Expires=86399&X-Amz-Credential=<key>%2F20230728%2Fus-east-2%2Fs3%2Faws4_request&X-Amz-Signature=41e820e8404ac796a758eccca15cf660251310d281dd546bce2d35d4a7fa8a09",
        "forms": [
            {
                "locked": false,
                "id": 8,
                "isActive": true,
                "createdOnUnix": 1689349105000,
                "completedOnUnix": 1690563654000,
                "answers": {
                    1: "Taco",
                    2: "",
                    3: "Nacho",
                    4: "07/28/2023",
                    5: "taco@lobbie.com",
                    6: "3108675309",
                    7: "Yes",
                    8: "Lobbie",
                    9: "1234567890",
                    10: "123",
                    11: "4108675309",
                    12: "true",
                    14: "false",
                    16: "true",
                    19: "false",
                    20: {
                        label: "Option 1",
                        value: 20
                    },
                    "mapped": {
                        "first_name": "Taco",
                        "middle_name": "",
                        "last_name": "Nacho",
                        "date_of_birth": "07/28/2023",
                        "email": "taco@lobbie.com",
                        "mobile_phone": "3108675309"
                    }
                }
            }
        ]
    }
```