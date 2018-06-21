# How to upload files

Uploading files, either through an API or through our Admin Panel is common functionality in our projects. Files can be everything from CSVs to images and videos. Here is some general guidelines on how to support this functionality.

## File sizes

Our general rule of thumb on how big files we're allowing in a "traditional" file upload (a client submitting the file to the backend) using a JSON payload through the API or using a form in the Admin Panel are as follows:

- Mobile: 10 mb
- Web: 25 mb

## How to upload a file

### API

Our preferred way of uploading files using our APIs is to send the file in a base64 encoded format in a JSON payload. This allows us to have a consistent API and since the size of the files our apps should be sending using the API are relatively small, then we can accept the overhead in using this approach. Once received on the backend, the file should be uploaded to S3.

Instead of base64 json, Multipart can also be used for file upload to webserver.

### Web (e.g. Admin Panel)

Our preferred way of upload files on the web, e.g. in our Admin Panel or through a SPA, is to use a multipart form. Once received on the backend, the file should be uploaded to S3.

## Handling large files

Often we have to deal with files that are larger than the suggested file sizes for "traditional" file uploads, and therefore the following approach is our recommendation.

Get a project-specific s3 bucket setup.

### Mobile

If the mobile apps have to upload large files, then our preferred way of doing it is to have the apps upload directly to S3 and afterwards submitting the path to the file to the backend. The apps can use native SDKs for assisting in uploading the files.

### Web (e.g. Admin Panel)

Similar to handling large files in mobile apps, our preferred way of uploading large files on the web is to use JavaScript to upload the file directly to S3. We can use the AWS SDK for this and once the upload is done, we can save the path to the file.

## Serving files

### Public files

Every `public` file uploaded to S3 should be served using a CDN. The CDN will allow frontends to do cropping and resizing using query parameters.

Either the backend can move the files into the project S3 with CDN, or a new CDN can be set up for the bucket (eg: imgix).

### Private files

It's important `private` files are never public or cached on CDNs. We have few approaches to this:

1) NStack, if the files needs auth or password protection. This could be a very simple solution.
2) Private S3 bucket and files are served by backend. It is possible to stream files from S3 as a response.

## Processing files

Processing that takes longer time than a request to respond should be offloaded into a queue. If a user needs to be notified, an email can be sent out. Remember to think about the amount of memory that is being used in the queue and consider if chunking is possible.
