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

# Terminology / Vocabulary

-   **Form Template** - What Lobbie uses to create a Form for a Patient.

-   **Form Template Group** - A collection of Form Templates. A link is assigned to the collection and can be used to provide Patients an "anonymous" method of completing each Form that is generated from each Form Template in the Form Template Group.

-   **Form Template Section** - A page of a Form Template. If a Form Template has multiple Form Template Sections a button at the bottom of the page will be enabled when a Patient has completed all required fields in the current section.

-   **Form Element** - A single field in a Form Template Section.

-   **Lobbie Form Attribute** - A metadata tag added to a Form Element which can be used to give structure to unstructured data.

-   **Form** - What a Patient completes. Created from a Form Template.

-   **Form Group** - A collection of Forms assigned to a Patient to be completed together with a shared due date.

-   **Form Packet** - Same as a "Form Group". An alias.

-   **Form Answer** - A single answer to a single Form Element in a Form.

-   **Prefill** - Using either Lobbie Form Attributes with the Lifecycle value of "Patient" or data from the Patient record in Lobbie to complete fields in a Form BEFORE the Patient begins the Form. Uses Lobbie Form Attributes to map data to Form Answers in a Form. Runs when a Form is created.

    -   Developers can pass a map of key/value pairs to prefill data in Forms when a Form Group/Packet is created. Passed values take precedence over Lobbie Form Attribute "Patient" lifecycles and Patient data.

-   **Autofill** - Saving answers from one Form to other Forms in a Form Group. Happens when a Form is completed. Uses Lobbie Form Attributes to map data between Forms in a Form Group.

-   **Fillable PDF** - When printing a Form, Lobbie maps Form Answers to PDF file that has been submitted to Lobbie. Instead of Lobbie Form Attributes we use Mapped PDF Fields.

# Authentication

> Request an Access Token:

```shell
curl -X POST \
    https://auth.lobbie.com/oauth2/token \
    -H "authorization: Basic \"$(echo -n '<client_id>:<client_secret>' | base64)\"" \
    -H 'content-type: application/x-www-form-urlencoded' \
    -d 'grant_type=client_credentials'
```

> Make sure to replace `client_id` and `client_secret` with your Client ID and Client Secret.

Lobbie uses the OAuth2 client_credentials flow to allow access to the Developer API.

To get a Client ID and Client Secret, log into your Lobbie account as a user with the Developer role and navigate to the [Developer Settings Page](https://api.lobbie.com/home/settings/developer)

Lobbie expects an Access Token be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer $LOBBIE_ACCESS_TOKEN`

<aside class="notice">
You must replace <code>access_token</code> with the token returned from calling https://auth.lobbie.com/oauth2/token with your Client ID and Client Secret.
</aside>

# Locations

## Get All Locations

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/locations \
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

`GET https://api.lobbie.com/lobbie/api/developer/v1/locations`

### Query Parameters

None

## Get a Specific Location

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/locations/<location_id> \
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

`GET https://api.lobbie.com/lobbie/api/developer/v1/locations/<location_id>`

### URL Parameters

| Parameter | Description                        |
| --------- | ---------------------------------- |
| ID        | The ID of the Location to retrieve |

## Create a Location

```shell
curl "https://api.lobbie.com/lobbie/api/developer/v1/locations/create" \
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
        "id": 1,               # Retrieve Lobbie IDs for StateProvince objects by sending a GET to https://api.lobbie.com/lobbie/api/developer/v1/state_provinces
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

`POST https://api.lobbie.com/lobbie/api/developer/v1/locations/create`

## Update a Location

```shell
curl "https://api.lobbie.com/lobbie/api/developer/v1/locations/update" \
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
        "id": 1,               # Retrieve Lobbie IDs for StateProvince objects by sending a GET to https://api.lobbie.com/lobbie/api/developer/v1/state_provinces
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

`POST https://api.lobbie.com/lobbie/api/developer/v1/locations/update`

# Patients

## Get All Patients

```shell
curl -X GET \
    https://localhost:8443/lobbie/api/developer/v1/patients \
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

`GET https://api.lobbie.com/lobbie/api/developer/v1/patients`

### Query Parameters

None

## Get a Specific Patient

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/patients/<patient_id> \
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
    "patientPrimaryAddress": {
      "address1": "1 E Baltimore St.",
      "city": "Baltimore",
      "postalCode": "21202",
      "latitude": 38.86293745172355,
      "longitude": -77.05874058689015,
      "addressNote": "Tacos are delicious here.",
      "id": 4,
      "createdOn": "2023-07-26T16:48:49.000Z",
      "lastUpdatedOn": "2023-07-26T16:48:49.000Z",
      "stateProvince": {
        "shortName": "MD",
        "name": "Maryland",
        "id": 33,
        "createdOn": "2023-07-26T16:48:37.000Z",
        "lastUpdatedOn": "2023-07-26T16:48:37.000Z"
      },
      "fullAddress": "1 E Baltimore St., Baltimore, MD 21202",
      "active": true,
      "isPrimary": true
    },
    "parents": [],      // A list of Patient objects that are parents of this Patient
    "children": [],     // A list of Patient objects that are children of this Patient
    "practitioners": [] // A list of Practitioner objects that are assigned to this Patient
  }
}
```

This endpoint retrieves a specific patient.

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/patients/<patient_id>`

### URL Parameters

| Parameter | Description                       |
| --------- | --------------------------------- |
| ID        | The ID of the Patient to retrieve |

## Create a Patient

## Update a Patient

# Form Templates

Form Templates are what Lobbie uses to create Forms for

## Get All Form Templates

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

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
            "sortOrder": 6
        },
        {
            "id": 1,
            "name": "Lobbie Local Patient Information",
            "isActive": true,
            "isStaffOnly": false,
            "sortOrder": 0
        }
    ]
}
```

This endpoint retrieves all form templates associated with your organization's Lobbie account.

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/templates`

### Query Parameters

None

## Get a Specific Form Template

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/<form_template_id> \
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

This endpoint retrieves a specific Form Template.

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/<form_template_id>`

### URL Parameters

| Parameter | Description                             |
| --------- | --------------------------------------- |
| ID        | The ID of the Form Template to retrieve |

# Form Template Groups

## Get All Form Template Groups

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
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

This endpoint retrieves all Form Template Groups associated with your organization's Lobbie account.

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

### Query Parameters

None

## Get a Specific Form Template Group

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/<form_template_group_id> \
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

This endpoint retrieves a specific Form Template Group.

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/<form_template_group_id>`

### URL Parameters

| Parameter | Description                                   |
| --------- | --------------------------------------------- |
| ID        | The ID of the Form Template Group to retrieve |

## Create a Form Template Group

```shell
curl -X POST \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      locationId: 1, \
      formTemplateIds: [1, 2, 3], \
      name: \"Taco Templates\", \
      practitionerId: null \
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

`POST https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

## Update a Form Template Group

```shell
curl -X PUT \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      id: 1, \
      formTemplateIds: [1, 2], \
      name: \"Taco Templates Remix\", \
      practitionerId: 1 \
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

`PUT https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

## Delete a Form Template Group

```shell
curl -X DELETE \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups \
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

`PUT https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups`

## Get All Form Template Groups Assigned to a Practitioner

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/practitioner/<practitioner_id> \
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

This endpoint retrieves all Form Template Groups associated with a Practitioner in your organization.

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/templates/groups/practitioner/<practitioner_id>`

### Query Parameters

None

# Form Groups / Form Packets

## Get All Form Groups/Packets

When retrieving a FormGroup/Packet you can choose to call either the /groups or /packets endpoint. Both return the same response.

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/packets \
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

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/groups`

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/packets`

### Query Parameters

-   `locationId` - number - Get FormGroups/Packets in this Location. Optional. Default all Locations.

-   `patientId` - number - Get FormGroups/Packets assigned to this Patient. Optional.

-   `practitionerId` - number - Get all FormGroups/Packets for Patients assigned to this Practitioner. Optional.

-   `formTemplateIds` - string, comma-separated - Get all FormGroups/Packets which include Forms with these FormTemplates. Optional.

-   `formTemplateNames` - string, comma-separated - Get all FormGroups/Packets which include Forms with these FormTemplates. Optional.

-   `startDateTime` - number - Get all FormGroups/Packets with a dueDate of this unix epoch time or greater. Optional. Default `now - 3 years`.

-   `endDateTime` - number - Get all FormGroups/Packets with a dueDate less than this unix epoch time. Optional. Default `now + 1 year`.

-   `limit` - number - Return at most this number of FormGroups/Packets. Optional. Default 10.

-   `page` - number - Offset the returned list of FormGroups/Packets by this number \* `limit`. Optional. Default 0.

-   `isArchived` - boolean - If true, returns only archived FormGroups/Packets. Optional. Default false.

-   `isActive` - boolean - If true, returns only active FormGroups/Packets. Optional. Default true.

-   `status` - number / FormStatusEnum - Return only FormGroups/Packets with this status. Optional. Default FormStatusEnum.STATUS_COMPLETE_NOT_COMPLETE.

## Get a Specific Form Group/Packet

```shell
curl -X GET \
    https://localhost:8443/lobbie/api/developer/v1/forms/groups/8 \
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
        "signedURL": "https://s3.us-east-2.amazonaws.com/lobbie-dev/fb924039-ed0f-4214-9196-a7fc33cd3619/49730717-2c3f-4e31-8686-02a08b08b853?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230728T170056Z&X-Amz-SignedHeaders=host&X-Amz-Expires=86399&X-Amz-Credential=AKIA4VI4T6RK7JOTETBA%2F20230728%2Fus-east-2%2Fs3%2Faws4_request&X-Amz-Signature=41e820e8404ac796a758eccca15cf660251310d281dd546bce2d35d4a7fa8a09",
        "forms": [
            {
                "locked": false,
                "id": 8,
                "isActive": true,
                "createdOnUnix": 1689349105000,
                "completedOnUnix": 1690563654000,
                "answers": {
                    "fe:8:1": "Taco",
                    "fe:8:2": "",
                    "fe:8:3": "Nacho",
                    "fe:8:4": "07/28/2023",
                    "fe:8:5": "taco@lobbie.com",
                    "fe:8:6": "3108675309",
                    "fe:8:7": "Yes",
                    "fe:8:8": "Lobbie",
                    "fe:8:9": "1234567890",
                    "fe:8:10": "123",
                    "fe:8:11": "4108675309",
                    "fe:8:12": "true",
                    "fe:8:14": "false",
                    "fe:8:19": "false",
                    "fe:8:16": "true",
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

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/groups/<form_group_id>`

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/packets/<form_packet_id>`

## Create a Form Group/Packet

> This creates a new Patient in Lobbie since there is no "id" passed in the "patient" object.

> When prefilling Forms, Lobbie will give precdence to key/value pairs in the "prefill" object passed with the request in lieu of data from the Patient record. For example here, the Form(s) created in this Form Group/Packet will be prefilled with "Taco" and "Nacho" for any Form Elements that hold the "first_name" and "last_name" Lobbie Form Attributes.

```shell
curl -X POST \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
        formTemplateGroupId: 1,
        locationId: 1,
        dueDateUnix: 1690811324454,
        patient: {
          first_name: \"Neato\",
          last_name: \"Burrito\",
          email: \"burrito@lobbie.com\",
          dateOfBirth: \"01/01/2000\"
        },
        prefill: {
          first_name: \"Taco\",
          last_name: \"Nacho\"
        }
    }"
```

> This request will assign the created Form Group/Packet to the Patient with an `id` of 1.

> Because no `prefill` object is passed, Lobbie will first use any past Forms that hold Lobbie Form Attributes of the "Patient" lifecycle and then data from the Patient record to prefill the new Forms that will be created.

```shell
curl -X POST \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/groups \
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

There are many ways to structure a request to create a new Form Group/Packet. However, they all rely on the presence of several things:

1. A `formTemplateGroupId` or a list of `formTemplateIds` so that Lobbie knows which Form Templates to use.

2. A `locationId` so that Lobbie can assign the Form Group/Packet to the correct location.

3. A `dueDateUnix`, which Lobbie uses to set the due date of the Form Group/Packet.

    a. Lobbie uses the time zone associated with the `location` to adjust this unix-epoch time to the correct due date.

    b. Due dates are adjusted to end-of-day, meaning that the times for all due dates will be 11:59:59 PM on the day the Form Group/Packet is due.

    c. Patients will not be able to access Form Groups/Packets once the due date is passed.

4. A `patient` object which must contain an `id` key to assign the Form Group/Packet to an existing Patient. Otherwise, Lobbie will try to create a new Patient before proceeding.

5. Finally, optionally, you may pass a `prefill` object which is an object of LobbieFormAttribute/FormAnswer key/value pairs.

    a. Lobbie uses the key attributes to find Form Elements in the Form Templates you pass to prefill Form Answers with the values in the object.

    b. For example if you were to pass, `{ prefill: { first_name: "Taco" } }`, Lobbie would find all Form Elements with the Lobbie Form Attribute of "first_name" and set the answer to that Form Element as "Taco".

    c. Lobbie prefills Forms from two other sources, past Forms that have Lobbie Form Attributes with the "Patient" lifecycle and data from the Patient records directly. However, any key/values you pass here take precedence over the other two methods.

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

`POST https://api.lobbie.com/lobbie/api/developer/v1/forms/groups`

## Update a Form Group/Packet

Add or remove Forms from a Form Group/Packet by passing Form Templates, and/or change the due date.

> This command will update the Form Group/Packet with `id` of 1 to have Forms created from Form Templates 1 and 4.

```shell
curl -X PUT \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 1,
      formTemplateIds: [1, 4],
      dueDateUnix: 1690815877454
    }"
```

> The Form Group/Packet with `id` of 1 is updated to include Forms from Form Templates 1 and 4.

```json
{
  "success": true,
  "message": "Form group updated.",
  "data": {
    "id": 1,
    "locationId": 1,
    "patientId": 3,
    "dueDateUnix": 1690873199000,
    "completedOnUnix": null,
    "createdOnUnixUtc": 1690558706000,
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

> This command will update the Form Group/Packet with `id` of 1 to have only have Forms from Form Template 1.

```shell
curl -X PUT \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/groups \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{
      id: 1,
      formTemplateIds: [1],
      dueDateUnix: 1690815877454
    }"
```

> The Form with Form Template 4 is removed:

```json
{
  "success": true,
  "message": "Form group updated.",
  "data": {
    "id": 1,
    "locationId": 1,
    "patientId": 3,
    "dueDateUnix": 1690873199000,
    "completedOnUnix": null,
    "createdOnUnixUtc": 1690558706000,
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

`PUT https://api.lobbie.com/lobbie/api/developer/v1/forms/groups`

# Forms

The structure of a Form returned from Lobbie includes Form Answers.

Form Answers can be found under the key `data.answers`. Answers that are mapped to structured fields using Lobbie Form Attributes can be found at the `data.answers.mapped` key.

The structure of a key for a Form Answer is as follows: `fe:{form_id}:{form_element_id}`. The key tells us that the Form Answer belongs on the Form associated with `{form_id}`, which itself is associated with a Patient, and that the "question" the Patient answered is represented by the Form Element retrived from `{form_element_id}`.

For example, looking at the response on the right side, the key `fe:8:1` means that the Patient has answered "Taco" on their Form with an ID of 8 to the Form Element of ID 1.

## Get a Specific Form

```shell
curl -X GET \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/<form_id> \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "locked": false,
        "id": 8,
        "isActive": true,
        "createdOnUnix": 1689349105000,
        "completedOnUnix": 1690563654000,
        "answers": {
            "fe:8:1": "Taco",
            "fe:8:2": "",
            "fe:8:3": "Nacho",
            "fe:8:4": "07/28/2023",
            "fe:8:5": "taco@lobbie.com",
            "fe:8:6": "3108675309",
            "fe:8:7": "Yes",
            "fe:8:8": "Lobbie",
            "fe:8:9": "1234567890",
            "fe:8:10": "123",
            "fe:8:11": "4108675309",
            "fe:8:12": "true",
            "fe:8:14": "false",
            "fe:8:19": "false",
            "fe:8:16": "true",
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
}
```

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/<form_id>`

# Printing / PDF Generation

## Creating a PDF

When creating a PDF of a Form, Form Group, Form Template or Form Template Group you can choose to call either the /print or /pdf endpoint. Both return the same response.

One of these endpoints must be called before a PDF can be retrieved.

> Settings "isPatient" in the request body to "false" will generate a PDF with Staff-only fields displayed. Defaults to "true".

```shell
curl -X POST \
    https://localhost:8443/lobbie/api/developer/v1/forms/print/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formTemplateId: 1, \
      locationId: 1, \
      isPatient: true
    }"
```

```shell
curl -X POST \
    https://localhost:8443/lobbie/api/developer/v1/forms/pdf/create \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data "{ \
      formTemplateId: 1, \
      locationId: 1, \
      isPatient: true
    }"
```

> The above command returns JSON structured like this:

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

`POST https://api.lobbie.com/lobbie/api/developer/v1/forms/print/create`

`POST https://api.lobbie.com/lobbie/api/developer/v1/forms/pdf/create`

## Retrieving a PDF

Call this endpoint after sending a POST request to either the /print or /pdf endpoints, passing the `s3ObjectPath` returned from the respective API call.

```shell
curl -X POST \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/print/retrieve \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '{ s3ObjectPath: <s3_object_path_from_POST_above> }'
```

```shell
curl -X POST \
    https://api.lobbie.com/lobbie/api/developer/v1/forms/pdf/retrieve \
    -H "Authorization: Bearer $LOBBIE_ACCESS_TOKEN" \
    --data '{ s3ObjectPath: <s3_object_path_from_POST_above> }'
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "",
    "data": {
        "s3ObjectPath": "<s3_object_path_equal_to_s3_object_path_in_request>",
        "signedURL": "https://signed_url_from_aws"
    }
}
```

### HTTP Request

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/print/retrieve`

`GET https://api.lobbie.com/lobbie/api/developer/v1/forms/pdf/retrieve`

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
