# Audio data type - "audio"

Allows to receive a bytes object containing an audio provided by the API user.

## Supported constraints

| Name           | Description                                                               | Value     |
| -------------- | ------------------------------------------------------------------------- | --------- |
| allowed_format | audio format the user must provided                                       | "mp3"     |
| max_size_mb    | maximum size of the provided audio file in megabytes (decimal definition) | float > 0 |

## Supported sources

| Source | Description                      |
| ------ | -------------------------------- |
| URL    | An URL pointing to an audio file |
| Base64 | A base64 encoded audio file      |

## Example

```python
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
    """Simple handler that acknowledges receiving the audio file."""
    # Get the file bytes from the payload
    audio = payload.get("audio")

    # Just acknowledge receiving the file and return its size
    file_size_mb = len(audio) / 1e6

    return {
        "status": "success",
        "message": f"Received audio file (size: {file_size_mb:.2f}MB)",
    }
```
