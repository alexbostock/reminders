# reminders

A web app for timed reminders, built on AWS

## Architecture

- User accounts managed by Cognito
- Reminders stored in DynamoDB
- Reminders run as Lambda functions, triggered by EventBridge (periodically query the database for reminders due)
- Frontend deployed with Amplify
- Backend built using Lambda / API Gateway

## TODO list

[] Build backend (user-facing) API
[] Build frontend
[] Deploy frontend
[] Build schedule backend (triggered on reminders set)
[] Integrate with useful services when reminders are triggered
[] Add support for recurring reminders
