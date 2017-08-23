# Checklist before going live with a project

When a project is ready to be deployed and go live, there's a couple of things one needs to remember to have in place - this is good place to start, although it might not cover everything for your project.

## What to check before going live

- **Redis**: Check that the provider has been added and Redis is being used for sessions in `droplet.json`. Also make sure that the sessions middleware has been added.
- **Bugsnag**: Make sure that the project has been created (on Bugsnag) and the correct credentials has been added to all environments using environment variables. Make sure that the Bugsnag middleware has been added and consider doing a simple test to see if errors are being reported correctly.
- **Meta**: Check that the middleware has been added and it is being enforced when doing requests.
- **Crypto**: Check that the keys for hashers and ciphers has been generated correctly. Consider if it is needed to move the keys to environment variables to ensure that generated tokens cannot be used across different environments.
**SSO**: If your project includes an admin panel, then make sure to add Nodes SSO as well. Remember to create credentials for the project and make sure that they are added to all environments as environment variables.
- **Storage**: If file uploads are supported, make sure that the Storage credentials has been correctly added to all environments as environment variables.
- **Mail**: If your project supports sending mails, make sure all mail related credentials has been setup correctly as environment variables on all environments.
- **CORS**: If your API is being used in a web app, then strongly consider to add the CORS middleware.
