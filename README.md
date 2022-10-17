# okta-openid-mini-project

## Requirements
* Node v14.18.0

## Getting started
1. Create a new AWS account.
2. Create an Administrator user and Administrators group, as described 
   [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html).
3. Install the AWS CLI, as described
   [here](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
4. Create an IAM access key for the Administrator user, and configure the **default** profile with the AWS CLI, as
   described [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds).
5. Open the serverless.yml file in this directory and replace *<Okta_host>* by the Okta end user dashboard host.
6. In a shell session in this directory, run:
   ```shell
   $ npm ci
   $ npx sls deploy 
   ```
   Output of the last command should be:
   ```
   Deploying okta-openid-mini-project to stage dev (us-east-1)

   ✔ Service deployed to stack okta-openid-mini-project-dev (120s)

   endpoint: GET - https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/
   functions:
     hello: okta-openid-mini-project-dev-hello (58 kB)

   Monitor all your API routes with Serverless Console: run "serverless --console"
   ```
7. Copy the URL from the last command output.
8. Configure your REST API client to generate OpenID keys using (the *<Okta_host>* is the Okta end user dashboard host):
   * Grant Type: Authorization Code
   * Client ID: (Get it from the application in the Okta admin console)
   * Client Secret: (Get it from the application in the Okta admin console)
   * Authorization URL: https://*<Okta_host>*/oauth2/default/v1/authorize
   * Access Token URL: https://*<Okta_host>*/oauth2/default/v1/token
   * Redirect URL: http://localhost:3000/api/auth/callback/okta
   * Scope: openid
   * Token Prefix: Bearer
9. Add the following endpoint to your REST API client:
   * Method: GET
   * URL: (Paste the URL copied in step 6)
   * Headers:
     * Authorization: (Value generated as configured in step 7)

## Testing 
1. Call the endpoint configured above a few times.

### Current behavior
* Endpoint responds 401 randomly, even with a bearer token that was just generated.
* The CloudWatch log group /aws/http-api/okta-openid-mini-project-dev has entries with at least one of the following
  authorizerError values:
  * "invalid_token: OIDC discovery endpoint communication error"
  * "invalid_token: JWKS communication error"

### Expected behavior
* Endpoint should not respond 401, unless the token is invalid or expired.
* The CloudWatch log group /aws/http-api/okta-openid-mini-project-dev entries should not contain the authorizerError
  values mentioned above. 
