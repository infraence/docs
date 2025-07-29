# Getting started

> [!WARNING]
> Infraence is currently in closed beta. If you'd like early access, you can request an invitation on the [landing page](https://infraence.com).

In this guide, you'll set up and run an inference API using Infraence. This example focuses on receiving MP3 audio files, running inference on them, and returning responses synchronously. 

By the end of this guide, you will have an inference API with the following features:

- Authentication of your users through API Keys.
- Access management dashboard.
- Validate that the files provided are of the right type.
- Decode the data received from API users.
- Load balancing across multiple running instances of your service.
- Secure internet access without direct exposure of your inference servers to the internet.
- Automatic HTTPS setup and certificate management.

Prerequisites:
- A GitHub account.
- Python 3.x installed on your machine, along with pip.

Estimated time: 10 to 15 minutes.

## Setting up your service
1. Go to the [Infraence studio](https://studio.infraence.com/).

2. Create your new service (eg: audio_classification). A service encapsulates multiple endpoints, API keys and worker credentials.

3. After creating your service, go to the "Connect" section, where you can manage your worker credentials. These credentials will be used by your Python script to connect to Infraence and receive inference requests. Make sure to keep these credentials secure, since they provide access to modifying and creating new endpoints. You can keep the credentials modal open for now, since you will need the connection string in the next steps.

## Setting up your Python project
4. To connect to Infraence from your Python script, you must install the Infraence SDK. It can be installed with pip by using the following command:
```bash
pip install -i https://test.pypi.org/simple/ infraence==0.1.4a0
```
> [!NOTE]
> You can install the infraence SDK in a Python virtual environment, though this will not be covered in this guide.

5. The infraence SDK allows you to define your model's API by declaring what you want to receive. In this example, we'll create an endpoint for your inference service, which will look like this:

```nginx 
POST https://gateway.infraence.com/hello-world
```

> [!IMPORTANT]
> Here you will need the connection string created in step 3.

```python
from infraence import Infraence
from typing import Any

infraence = Infraence(
    connection_string="Paste the connection string from step 3 here"
)

@infraence.define(
    name="hello-world",
    request_body={
        "audio": {
            "data_type": "audio",
            "required": True,
            "constraints": {
                "allowed_format": "mp3",
                "max_size_mb": 1.5,
            },
        }
    },
)
def handle_request(payload: Any):
    # Here you can run your predictions. 
	# For audio fields, you will receive a local file path
	# at payload["audio"], pointing to the decoded MP3 file.    
	# Whatever you return from this function will be sent 
    # as the response body.
    # Keep in mind that this function will be called in every request,
    # so make sure to instantiate your models outside of it.
    return {
      "status": "success",
      "message": "audio received successfully"
    }

infraence.run()
```

In the `request_body` field, you can define all the fields you want the users to send to your inference API. In the example above, users must provide a body field named "audio", that contains a base64 encoded MP3 file with a maximum size of 1.5 megabytes.

> [!IMPORTANT]
> The audio must be sent by the user as a base64-encoded string,  
but your script will receive it as a decoded MP3 file on disk. Infraence will take care of validation and decoding.

6. Now you can run your script:
```bash
Python3 yourfilename.py
```
> [!NOTE]
> This command may vary depending on your python installation.

Then you should see something like this in your terminal:

```bash
✅ Endpoint registered successfully
✅ Successfully connected to Infraence broker

🚀 API is now available at POST https://gateway.infraence.com/hello-world
```

## Testing your API
7. To test your API, first you must create an API Key. API Keys provide access to your inference services. To issue an API Key, go to the "API Key manager" section of the Infraence studio, there you can create a new key. Just like connection strings, API Keys are sensitive information and you should keep them secure.

> [!CAUTION]
> For security reasons, Infraence does not store or display API keys after creation, so if you lose a key you will have to issue a new one.

8. Infraence requests are formed depending on the API definition you declared in your inference script. The only requirement is to provide an `API-Key` header containing a valid API key for the requested service, like the one created in step 7. Here is an example that sends an audio `audio.mp3` located in your current directory to your API:

> [!IMPORTANT]
> Remember to replace your API key where indicated

Powershell:
```powershell
$here = Get-Location
Set-Location -Path $here

$path = Join-Path $here "audio.mp3"
$b64 = [Convert]::ToBase64String([IO.File]::ReadAllBytes($path))

$payload = "{`"audio`":`"$b64`"}"
$payload | Set-Content "body.json"

curl.exe -X POST https://gateway.infraence.com/hello-world `
  -H "Content-Type: application/json" `
  -H "API-Key: ===REPLACE YOUR API KEY HERE===" `
  "--data" "@body.json"

Remove-Item "body.json"
```

macOS:
```bash
curl -X POST https://gateway.infraence.com/hello-world \
  -H "Content-Type: application/json" \
  -H "API-Key: Replace your API Key here" \
  -d "{\"audio\": \"$(base64 -i audio.mp3)\"}"
```
Output:
```json
{ "status": "success", "message": "audio received successfully" }
```

Your service is now accessible from anywhere on the internet.  

If you run another instance of your Python script and send additional requests,  Infraence will automatically balance them across all active instances.

## What's Next?
Your inference API is now production-ready. All the core features like authentication, validation, secure access, and load balancing are built-in and handled by Infraence. To start serving your users, you just need to deploy your inference script to your servers, a cloud environment, or both at the same time. Once running, it will seamlessly connect to Infraence and begin handling requests from anywhere in the world.
