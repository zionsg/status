# Standard status values

Suggested standard list of 7-letter words that can be used in a `status` field,
whether in an API response or in a database table.

Why 7 letters? Cos it all started with "success", "failure" and "pending" :)

## Beginning
- `started`: Job started.
- `ordered`: Item ordered.
- `shipped`: Item shipped.
- `readied`: Ready to act.

## In Progress
- `pending`: In progress, waiting for update.
- `cloning`: Cloning a resource.
- `loading`: Loading something huge which may take a while.
- `girding`: Preparing.
- `prepped`: Prepared.
- `parsing`: Processing.
- `handled`: Processed, completed processing.
- `getting`: Downloading.
- `fetched`: Downloaded.
- `syncing`: Synchronizing.

## Uploading
- This warrants a section by itself. When a user uploads files via a webpage
  in a browser, whether via a `<input type="file">` filepicker element or
  dragging files/folders into a drop zone, there are 3 stages:
    + `reading`: Browser reads the files from the user's computer. Browser tab
      and session must not be closed at this stage. Note the differences between
      the various upload methods for an upload involving a huge no. of files,
      e.g. 100,000 files in multiple folders:
        * If the filepicker element is used, the webpage may freeze for a
          few minutes until all the files are read to return
          `HTMLInputElement.files`.
        * If drag & drop is used, the page does not freeze when
          `DragEvent.dataTransfer.items` is accessed on the
          drop event. It is not known if the page will freeze if
          `DragEvent.dataTransfer.files` is accessed instead.
    + `sending`: Browser sends the files to a backend server, typically via a
      HTTP request to an API endpoint. Browser tab and session must not be
      closed at this stage. The XMLHttpRequest progress event may be used to
      track the progress for this stage. The Fetch API currently does not
      support progress events.
    + `storing`: Backend server stores the files on a storage system, typically
      the local filesystem or remote cloud storage. Browser tab and session may
      be closed at this stage. It should not be assumed that each file will be
      stored successfully hence it would be good to have progress updates for
      this stage as well, e.g. via progress callbacks from the storage system.

## Workflow
- `briefed`: Informed via message or email.
- `deputed`: Assigned user to work on document.
- `vetting`: Reviewing document.
- `checked`: Document reviewed.
- `vouched`: Document approved.

## Ending
- `killing`: Cancelling job.
- `stopped`: Job stopped but interim results may still be saved or returned.
- `aborted`: Job cancelled without saving anything.
- `success`: Job completed with success.
- `failure`: Job failed to complete or ended with error.
- `settled`: Job completed without indication of success or failure,
  e.g. case closed.
- `unknown`: This can be used when the status of an external API response cannot
  be resolved to one of the standard values.

--------------------------------------------------------------------------------
P.S. If you are searching for 2 same-length words to mark the start and end
of a block of code, try `begin` and `close` (^ v ^)
