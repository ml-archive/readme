# How to report to Bugsnag

We use [Bugsnag](https://www.bugsnag.com/) as our tool of choice for reporting and monitoring errors. It can be a really powerful tool to get some insights on how our projects are performing. However, poorly made reports can also cause confusion and frustration since it can be hard to decipher what went wrong and why.

To make sure our reports are aligned and contains the neccessary information, please have a look below.

## When to report

There's a lot of different scenarios where it can make sense to report errors:

- When a request is not as expected
- When something went wrong while handling the request
- When the response could not be created correctly
- When commands didn't run as expected

As a rule of thumb, one can report when the logic is not following the happy path of the project. However, consider marking errors that are a trivial (e.g. a request with validation errors) to not be reported to Bugsnag.

## How to report

Using our [Bugsnag package](https://github.com/nodes-vapor/bugsnag), there's basically two ways of reporting errors to Bugsnag:

- Using the Bugsnag middleware which reports all thrown `Abort` errors unless they hold `metadata` that says that they shouldn't be reported.
- Using the reporter directly. This is convenient when you are not working with a request (e.g. in a command) or when you want to report an error but without throwing the error as a response to the request.

## What to report

What you decide to include in the report is crucial to be able to debug later if errors starts to get reported. Here's some central things to consider when constructing your `Abort` error:

- `status`: Have a look at [our API guide](https://github.com/nodes-vapor/readme/blob/master/Documentation/how-to-write-apis.md#response-codes) to see a list of our most commonly used status code.
- `reson`: Make sure to have a describing error message. Consider making the message general for the type of error and then use the `metadata` part for any dynamic values.
- `metadata`: Any non-sensitive information should be added to this dictionary (do not include any passwords etc.). A common approach is to include id's for each model item that is relevant for the error that occurred. If an error occurred bassed on a request, that request will automatically be included in the report. Remember to [configure Bugsnag](https://github.com/nodes-vapor/bugsnag#metadata) to filter out any sensitive information from the request.