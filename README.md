# PeopleFinder sample application

[![Netlify Status](https://api.netlify.com/api/v1/badges/fe55f8a0-4595-43ec-8dc3-ecf653a62b28/deploy-status)](https://app.netlify.com/sites/peoplefinder/deploys)

[Aserto](https://aserto.com) is an authorization framework that provides fine-grained authorization for API's and applications. Aserto can be used in the service / API to make allow/deny decisions based on an authorization policy.

This sample demonstrates the integration of the [Aserto Express middleware](https://github.com/aserto-dev/aserto-node) with an API, and the [Aserto React SDK](https://github.com/aserto-dev/aserto-react) into a React application created using [create-react-app](https://reactjs.org/docs/create-a-new-react-app.html).

The sample shows how to secure an API (defined in `service/users-api.js`) using the [Aserto Express middleware](https://github.com/aserto-dev/aserto-node). Refer to the Express middleware documentation for a deeper overview of how to use it.

The Aserto React SDK helps solve a related problem: what UI elements should be rendered, and in what state, based on the logged-in user and the authorization policy they are subject to.

Aserto defines a <strong>Display State Map</strong> that defines three possible states for UI components:
1. Visible and enabled
2. Visible and disabled
3. Not visible

This sample also shows how the Display State Map returned by the Aserto custom hook can be used to dynamically render UI components based on those three states.

## Project setup

Use `yarn` to install the project dependencies:

```bash
yarn install
```

## Configuration

### Configure credentials

Copy `.env.example` into a new file in the same folder called `.env`, and replace the values with your Aserto developer keys:

```bash

POLICY_INSTANCE_NAME={Your Policy name}

# To use the Aserto hosted authorizer, provide an API key and Tenant ID
AUTHORIZER_API_KEY={Your Authorizer API Key - find in the Aserto console in the "Policy settings" section for this policy}
DIRECTORY_API_KEY={Your Directory read/write API Key - find in the Aserto console in the "Policy settings" section for this policy}
TENANT_ID={Your Tenant ID - find in the Aserto console in the "Policy settings" section for this policy}

# To use a local authorizer, instead of the two variables above, provide the service URL and cert file
AUTHORIZER_SERVICE_URL=localhost:8282
DIRECTORY_SERVICE_URL=localhost:9292

# For Topaz:
AUTHORIZER_CERT_CA_FILE=$HOME/.config/topaz/certs/grpc-ca.crt
DIRECTORY_CERT_CA_FILE==$HOME/.config/topaz/certs/grpc-ca.crt
# For the Aserto edge authorizer:
AUTHORIZER_CERT_CA_FILE=$HOME/.config/aserto/aserto-one/certs/grpc-ca.crt
DIRECTORY_CERT_CA_FILE==$HOME/.config/aserto/aserto-one/certs/grpc-ca.crt
```

Optionally, you can override these base URL's:

```bash
APP_ORIGIN={OPTIONAL: THE BASE URL OF YOUR APPLICATION (default: http://localhost:3000)}
REACT_APP_API_ORIGIN={OPTIONAL: THE BASE URL OF YOUR API (default: http://localhost:3001)}
```

## Run the sample

### Compile and hot-reload for development

This compiles and serves the React app at localhost:3000, and starts the backend API server on port 3001.

```bash
yarn run dev
```

To run the api-server on its own, run `yarn run api-server`.  To run the single-page application on its own, run `yarn run spa`.

## Deployment

### Compiles and minifies for production

```bash
yarn run build
```

### Deploy to Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/aserto-demo/peoplefinder-acmecorp#REACT_APP_NETLIFY=NETLIFY)

The project is ready to deploy to Netlify. Just click the "Deploy to Netlify" badge on the repo, or fork the project and set up a Netlify deployment for it.

Note that the API is deployed as a Netlify function.

Also, in order to run properly, the environment variables found in `.env.example` MUST be set up in the Deployment section in Netlify.

* POLICY_INSTANCE_NAME={Your Policy name}
* TENANT_ID={Your Tenant ID (find in the Aserto console in the "Policy settings" section)}
* AUTHORIZER_API_KEY={Your Authorizer API Key (find in the Aserto console in the "Policy settings" section)}
* DIRECTORY_API_KEY={Your Directory read/write API Key (find in the Aserto console in the "Connections" section under "Aserto Directory" connection)}
* REACT_APP_NETLIFY=NETLIFY


The following environment variables have the right defaults and don't need to be changed unless you know what you're doing!

* REACT_APP_POLICY_ROOT={policy root (the first component of the policy module name) - defaults to `peoplefinder`}
* REACT_APP_DEX_AUDIENCE=acmecorp-app
* REACT_APP_DEX_CLIENT_ID=acmecorp-app
* REACT_APP_DEX_DOMAIN=acmecorp.demo.aserto.com


### Building and running as a local docker image

Create a `.env.docker` (based on the `.env.example`) with the appropriate settings. If a local authorizer is to be used, the `Dockerfile` expects the `src/utils/gateway-ca.crt` to be the public key file for the gateway CA for that authorizer, and AUTHORIZER_CERT_FILE in `.env.docker` to be set to `src/utils/gateway-ca.crt`.

Alternatively, you can take the `COPY --from=build /app/src/utils/gateway-ca.crt ./src/utils/gateway-ca.crt` directive out of the `Dockerfile` and instead inject the `ca.crt` file location at runtime (e.g. as a kubernetes secret) via the `AUTHORIZER_CERT_FILE` environment variable.

`yarn run docker-build`: uses docker build to create a local container image
`yarn run docker-run`: runs the docker container built using the command above with the name `peoplefinder`

You can tweak the image tags using the four environment variables defined below.

### Building and running on Google Cloud Build / Run
`yarn run build-gcp`: uses the GCP container image build service to build on GCP
`yarn run deploy-gcp`: runs the container on Google Cloud Run

Note that these scripts rely on the following environment variables:
* `REGISTRY`: registry prefix (defaults to `gcr.io`)
* `PROJECT`: project name (the container image is named `$REGISTRY/$PROJECT/$IMAGE` - change to your GCP project name)
* `IMAGE`: image name (defaults to `peoplefinder`)
* `SERVICE`: GCR service name (defaults to `peoplefinder`)

## Run your tests

```bash
yarn run test
```

## Author

[Aserto](https://aserto.com)

## License

This project is licensed under the MIT license. See the [LICENSE](./LICENSE) file for more info.
