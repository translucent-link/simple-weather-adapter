# Chainlink Simple Weather External Adapter 

See [Install Locally](#install-locally) for a quickstart

## Input Params

- `q`, `city`, or `town`: The location your fetching weather data for

## Output

```json
{
	"jobRunID": 0,
	"data": {
		"coord": {
			"lon": -71.0598,
			"lat": 42.3584
		},
		"weather": [{
			"id": 804,
			"main": "Clouds",
			"description": "overcast clouds",
			"icon": "04d"
		}],
		"base": "stations",
		"main": {
			"temp": 287.15,
			"feels_like": 287.06,
			"temp_min": 285.96,
			"temp_max": 288.36,
			"pressure": 1013,
			"humidity": 94
		},
		"visibility": 10000,
		"wind": {
			"speed": 8.23,
			"deg": 10
		},
		"clouds": {
			"all": 100
		},
		"dt": 1664995352,
		"sys": {
			"type": 2,
			"id": 2013408,
			"country": "US",
			"sunrise": 1664966731,
			"sunset": 1665008372
		},
		"timezone": -14400,
		"id": 4930956,
		"name": "Boston",
		"cod": 200,
		"result": 287.15
	},
	"result": 287.15,
	"statusCode": 200
}
```

## Install Locally

Install dependencies:

```bash
yarn
```

### Test

Run the local tests:

```bash
yarn test
```

Natively run the application (defaults to port 8080):

### Run

```bash
yarn start
```

## Call the external adapter/API server

```bash
curl -X POST -H "content-type:application/json" "http://localhost:8080/" --data '{ "id": 0, "data": { "q": "Boston" } }'
```

## Docker

If you wish to use Docker to run the adapter, you can build the image by running the following command:

```bash
docker build . -t weather-adapter
```

Then run it with:

```bash
docker run -e API_KEY=<APIKEY> --rm -p 8080:8080 -it weather-adapter:latest
```
You can get an API key [here](https://home.openweathermap.org/api_keys). A word of warning: it took about 20minutes for the API key to become active after creating it.

## Serverless hosts

After [installing locally](#install-locally):

### Create the zip

```bash
zip -r external-adapter.zip .
```

### Install to AWS Lambda

- In Lambda Functions, create function
- On the Create function page:
  - Give the function a name
  - Use Node.js 12.x for the runtime
  - Choose an existing role or create a new one
  - Click Create Function
- Under Function code, select "Upload a .zip file" from the Code entry type drop-down
- Click Upload and select the `external-adapter.zip` file
- Handler:
    - index.handler for REST API Gateways
    - index.handlerv2 for HTTP API Gateways
- Add the environment variable (repeat for all environment variables):
  - Key: API_KEY
  - Value: Your_API_key
- Save

#### To Set Up an API Gateway (HTTP API)

If using a HTTP API Gateway, Lambda's built-in Test will fail, but you will be able to externally call the function successfully.

- Click Add Trigger
- Select API Gateway in Trigger configuration
- Under API, click Create an API
- Choose HTTP API
- Select the security for the API
- Click Add

#### To Set Up an API Gateway (REST API)

If using a REST API Gateway, you will need to disable the Lambda proxy integration for Lambda-based adapter to function.

- Click Add Trigger
- Select API Gateway in Trigger configuration
- Under API, click Create an API
- Choose REST API
- Select the security for the API
- Click Add
- Click the API Gateway trigger
- Click the name of the trigger (this is a link, a new window opens)
- Click Integration Request
- Uncheck Use Lamba Proxy integration
- Click OK on the two dialogs
- Return to your function
- Remove the API Gateway and Save
- Click Add Trigger and use the same API Gateway
- Select the deployment stage and security
- Click Add

### Install to GCP

- In Functions, create a new function, choose to ZIP upload
- Click Browse and select the `external-adapter.zip` file
- Select a Storage Bucket to keep the zip in
- Function to execute: gcpservice
- Click More, Add variable (repeat for all environment variables)
  - NAME: API_KEY
  - VALUE: Your_API_key
