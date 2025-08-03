# Text data type - "text"

Allows receiving a string as the value for a specified field.

## Supported constraints

| Name       | Description                                | Value   |
| ---------- | ------------------------------------------ | ------- |
| max_length | Maximum allowed length for the field value | int > 0 |

## Supported sources

| Source | Description                                  |
| ------ | -------------------------------------------- |
| Inline | Content received as-is from the request body |

## Example

```python
@infraence.define(
    name="text-example",
    request_body={
        "example-text": {
            "data_type": "text",
            "required": False,
            "constraints": {"max_length": 15},
        },
    },
)
def handle_request(payload: Any):
	return payload.get("example-text") # Returns a string or None since the field is optional
```
