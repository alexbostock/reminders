# reminders

A web app for timed reminders, built on AWS

## Architecture

- User accounts managed by Cognito
- Reminders stored in DynamoDB
- Reminders run as Lambda functions, triggered by EventBridge (periodically query the database for reminders due)
- Frontend deployed with Amplify
- Backend built using Lambda / API Gateway

## Usage

Deploy using AWS SAM:

```
cd reminders-app
sam build
sam deploy --guided
```

## TODO list

- [ ] Build backend (user-facing) API
- [ ] Build frontend
- [ ] Deploy frontend
- [ ] Build schedule backend (triggered on reminders set)
- [ ] Integrate with useful services when reminders are triggered
- [ ] Add support for recurring reminders

## Backend API

RESTful API to fetch and modify "reminder" resources.

Users must be signed in to access all endpoints, and can only fetch and modify their own resources

### Reminder data structure

Reminder resources are represented by a JSON object data structure, eg.

```
{
    id: "abcde",
    title: "My reminder",
    reminderDate: "2021-01-01T09:00:00Z"
}
```

- `id` is a unique identifier
- `title` is a user-input string
- `reminderDate` is a timestamp when the reminder should be triggered, in an ISO 8601 format

This "standard reminder object" is used throughout API endpoints.

### `GET /reminders`

Returns a list of standard reminder objects, eg. `/reminders`:

```
[
    {
        id: "abcde",
        title: "My reminder",
        reminderDate: "2021-01-01T09:00:00Z"
    },
    {
        id: "abcdx",
        title: "My other reminder",
        reminderDate: "2021-06-21T01:00:00Z"
    }
]
```

### `GET /reminders/<id>`

Given a valid reminder's `id`, returns that reminder's standard reminder object.

If the requested ID cannot be found, returns status 404.

### `POST /reminders`

Creates a new reminder resource.

Expects the request body to be a standard reminder structure, but with no `id` field, eg.

```
{
    title: "My reminder",
    reminderDate: "2021-01-01T09:00:00Z"
}
```

On success, the response is the newly-created reminder's standard reminder structure (including its `id`).

### `PATCH /reminders/<id>`

Modifies an existing reminder resource.

In a valid request, the `id` in the URL must correspond to a valid reminder resource.

The request body should be a partial standard reminder structure, containing a sub-set of the standard structure's properties, but never including `id`.

All valid fields are updated in the corresponding resource (and any omitted fields are left unchanged).

On success, the response is the newly-updated version of the reminder.

eg.

If the current reminder stored is:

```
{
    id: "abcde",
    title: "My reminder",
    reminderDate: "2021-01-01T09:00:00Z"
}
```

and the request `PATCH /reminders/abcde` has the following body:

```
{
    title: "My updated reminder"
}
```

the new version of the resource (which is saved and returned in the response body) is:

```
{
    id: "abcde",
    title: "My updated reminder",
    reminderDate: "2021-01-01T09:00:00Z"
}
```

### `DELETE /reminders/<id>`

`id` in the URL should be a valid reminder `id`.

On success, the corresponding resource is deleted.
