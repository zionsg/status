# Standard status values

Suggested standard list of 7-letter words that can be used in a `status` field,
whether in an API response or in a database table. Always store/return/compare
statuses in lowercase to reduce coding errors.

Why 7 letters? Cos it all started with `success`, `failure` and `pending` :)

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
- `ignored`: Not sent for processing as it is not applicable.
- `handled`: Processed, completed processing.
- `getting`: Downloading.
- `fetched`: Downloaded.
- `syncing`: Synchronizing.

## Uploading
- This warrants a section by itself. When a user uploads files via a webpage
  in a browser, whether via a `<input type="file">` filepicker element or
  dragging files/folders into a
  [drop zone](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API/File_drag_and_drop),
  there are typically 4 stages:
    + `reading`: Browser reads the files from the user's computer. Browser tab
      and session must not be closed at this stage. Note the differences between
      the various upload methods for an upload involving a huge no. of files,
      e.g. 100,000 files across multiple folders:
        * If the filepicker element is used, the webpage may freeze for a
          few minutes after selecting the folder/files, until all the files are
          read to populate `HTMLInputElement.files` (even if the property is
          not accessed).
        * If drag & drop is used, the page does not freeze noticeably when
          `DragEvent.dataTransfer.items` is accessed on the
          drop event. It is not known if the page will freeze if
          `DragEvent.dataTransfer.files` is accessed instead.
        * Note that DataTransfer items do not persist thru async calls.
          See https://stackoverflow.com/a/59244780 for more info.
    + `parsing`: Client-side code in the browser prepares the payload for
      the `multipart/form-data` POST request, typically a `FormData` object.
      Browser tab and session must not be closed at this stage. This involves
      running of the sample code below which would take longer if there is a
      huge no. of files:

      ```
      let formData = new FormData();
      [...files].forEach((file, fileIndex) => { // file is of File type
          formData.append('files[]', file);
          formData.append(
             'filepaths[]',
              file.relativePath || file.webkitRelativePath || file.name
          );
          formData.append(
              'file_modified_timestamps[]',
              file.lastModified
          );
      });
      ```

    + `sending`: Browser sends the files to a backend server, typically via a
      HTTP request to an API endpoint. Browser tab and session must not be
      closed at this stage. The XMLHttpRequest.upload progress event may be used
      to track the progress for this stage. The Fetch API currently does not
      support progress events.
        * Note that there may be multiple progress events fired for the same
          upload request where `event.loaded` is the same as `event.total`,
          hence it may be safer to compare with the total bytes uploaded if
          progress percentage is to be computed. See
          https://stackoverflow.com/q/66976081 for more info.
    + `storing`: Backend server stores the files on a storage system, typically
      the local filesystem or remote cloud storage. Browser tab and session may
      be closed at this stage. It should not be assumed that each file will be
      stored successfully hence it would be good to have progress updates for
      this stage as well, e.g. via progress callbacks from the storage system.
        * The backend may do additional processing after storing the files,
          e.g. indexing and inference, for which the progress would be reported
          under this stage as well. The processing is not split into additional
          stages as the backend server would usually only provide a single API
          endpoint for the browser to poll for progress all the way to the end,
          e.g. `http://localhost:10000/api/job/:id/progress`, instead of 1 for
          storing, 1 for indexing, 1 for inference, etc.

## Workflow
- `briefed`: Informed via message or email.
- `deputed`: Assigned user to work on document/ticket.
- `resting`: Worker waiting for task to be assigned. Not named `waiting` so as
  to differentiate starting letter from `working`.
- `working`: User has picked up the document and started work on it.
- `request`: Request for review of document/ticket.
- `vetting`: Reviewing document/ticket.
- `inquiry`: Asking owner of document/ticket for more information.
- `revised`: Corrected classification predicted using machine learning model.
- `checked`: Document/ticket reviewed.
- `refused`: Document/ticket rejected.
- `allowed`: Document/ticket approved.
- `expired`: Document expired.

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

## Audit Columns
- The statuses above can be used for naming audit columns in database tables
  as well, typically in the format `<status>_at` for a `TIMESTAMP` column to
  indicate when the record reached that status (this gives more information than
  a `is_<status>` column), and `<status>_by` for a `INT` column to
  indicate the ID of the user who made the record reach that status.
- If any of the `*_at` audit columns is used in a UNIQUE index,
  e.g. `UNIQUE(username, deleted_at)` in the `actor` table (for user records),
  it is recommended that the datatype be set to `BIGINT NOT NULL DEFAULT '0'`
  instead of `TIMESTAMP NULL`, with 0 as the default value and the values being
  the UNIX timestamp in seconds/milliseconds/microseconds (millisecond
  precision recommended as many things can happen within the same second). This
  is due to MySQL treating `NULL` values as distinct, i.e. `NULL != NULL`,
  which would result in duplicate records,
  e.g. `('cat', NULL), ('cat', NULL), ('cat', 0), ('cat', 1650852966)`
  would still be allowed with the `UNIQUE(username, deleted_at)` constraint.
    + In such a case, for consistency's sake especially during coding,
      it would be better for all the audit columns in the database to use
      `BIGINT NOT NULL DEFAULT '0'` for the datatype. `UNSIGNED` is not used as
      it is not a valid datatype in the ANSI SQL standard, which means
      PostgreSQL would not support it. `INT` datatype not recommended as it is
      prone to the
      [Year 2038 problem](https://en.wikipedia.org/wiki/Year_2038_problem)
      without use of `UNSIGNED`. See
      https://medium.com/@aleksandrasays/dealing-with-mysql-nulls-and-unique-constraint-d260f6b40e60
      for more info.
- The 4 most common audit columns are as listed below.
    + `created`: Record created. Columns: `created_at`, `created_by`.
    + `updated`: Record updated. Columns: `updated_at`, `updated_by`.
    + `deleted`: Record marked as deleted. Columns: `deleted_at`, `deleted_by`.
      A value of `NULL` or 0 for `deleted_at` would imply that the record is not
      marked as deleted. Soft-deleted records, i.e. those marked as deleted,
      are usually subject to hard-deletion via cron jobs, i.e. purging from the
      database such that the records do not exist or take up space anymore.
    + `revoked`: Record disabled/suspended/deactivated. Columns: `revoked_at`,
      `revoked_by`. A value of `NULL` or 0 for `revoked_at` would imply that
      the record is not disabled.

--------------------------------------------------------------------------------
P.S. If you are searching for 2 same-length words to mark the start and end
of a block of code which are alphabetically ordered, try `BEGIN` and `CLOSE` 
(^ v ^)
