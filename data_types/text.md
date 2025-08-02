# Text data type

---

## Description

The "text" data type allows receiving a string as the value for a specified field.

## Supported constraints

| Name       | Description                                | Value |
| ---------- | ------------------------------------------ | ----- |
| max_length | Maximum allowed length for the field value | int   |

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
		return payload.get("example-text")
```
