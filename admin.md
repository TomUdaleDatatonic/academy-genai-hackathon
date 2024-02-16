# Instructions for Client Admin for Gen AI Hackathon

## Create a project for the hackathon

## Create a service account

You should give the service account the following roles: 

- `Vertex AI User`
- `Big Query Admin`

You should distribute the `credentials.json` file to each participant so they can impersonate the service account for the hackathon.

## Create a Vertex AI workbench instance for each user

This should be of the new **instances** type, not a user-managed or managed notebook.


## Upload the BQ data

In `analytics-assistant/datasets` you should run `BQ_upload.ipynb` to create and upload sample data for the hackathon. A good chance to test that your credentials and service account are working correctly too.

## When participants begin:

They should complete the following:

1. Clone xxx repository to get all notebooks and hands-on materials
2. Copy the credentials.json file to the root of the repository

