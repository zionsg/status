# Standard status values

Suggested standard list of 7-letter words that can be used in a `status` field,
whether in an API response or in a database table.

Why 7 letters? Cos it all started with "success", "failure" and "pending" :)

success
informed

## Beginning
- `started`: Job started.
- `ordered`: Item ordered.
- `shipped`: Item shipped.
- `prepped`: Ready to act.

## In Progress
- `pending`: In progress, waiting for update.
- `loading`: Loading something huge which may take a while.
- `tidying`: Processing.
- `sending`: Uploading.
- `getting`: Downloading.
- `syncing`: Synchronizing.

## Workflow
- `briefed`: Informed via message or email.
- `inspect`: Reviewing document.
- `checked`: Document reviewed.
- `vouched`: Document approved.

## Ending
- `killing`: Cancelling job.
- `stopped`: Job stopped or cancelled.
- `success`: Job completed with success.
- `failure`: Job failed to complete or ended with error.
- `settled`: Job completed without indication of success or failure.
- `unknown`: This can be used when the status of an external API response cannot
  be resolved to one of the standard values.
