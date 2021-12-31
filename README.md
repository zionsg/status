# Status

Suggested standard list of 7-letter words that can be used in a `status` field,
whether in an API response or in a database table.

Why 7 letters? Cos it all started with "success", "failure" and "pending" :)

## Values
- `started`: Job started.
- `stopped`: Job stopped.
- `pending`: In progress.
- `success`: Job completed with success.
- `failure`: Job failed to complete.
- `inspect`: Reviewing document.
- `checked`: Reviewed document.
- `vouched`: Approved document.
- `settled`: Job completed without indication of success or failure.
- `unknown`: This can be used when the status of an external API response cannot
  be resolved to one of the standard values.
