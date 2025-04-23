# Fewsats Logger

A simple Python logging wrapper for Better Stack (Logtail) with automatic environment context.

## 5-Minute Setup

1. **Install package**
   ```bash
   pip install fewsats-logger
   ```

2. **Set environment variables** (in .env file or environment)
   ```
   BETTER_STACK_SOURCE_TOKEN=your_source_token
   BETTER_STACK_HOST=your_source_host
   ENV=dev
   ```

**IMPORTANT**: Use **dev** and **prod** for ENV values.

Get the host & token by creating a new source at https://telemetry.betterstack.com/

3. **Initialize once** in your main file
   ```python
   import os
   from dotenv import load_dotenv
   from fewsats_logger.core import setup_logging

   load_dotenv()   
   setup_logging(env=os.environ.get("ENV", "dev"),
                 host=os.environ.get("BETTER_STACK_HOST"),
                 token=os.environ.get("BETTER_STACK_SOURCE_TOKEN"))
   ```

4. **Use standard logging** anywhere in your code
   ```python
   import logging
   logger = logging.getLogger(__name__)
   logger.info("Message")
   logger.error("Error", extra={"order_id": "12345"})  # Add filterable fields
   ```

That's it. Logs will appear in Better Stack dashboard and console.

---

## Additional Details

### Log Levels

- `logger.debug()`: Detailed debugging information
- `logger.info()`: Confirmation that things are working
- `logger.warning()`: Something unexpected happened
- `logger.error()`: A more serious problem
- `logger.critical()`: A fatal error that prevents operation

### Adding Context for Filtering

```python
logger.info("Processing order", extra={
    "external_id": "12345",
    "customer_id": "67890"
})
```

you can add it to multiple statements like this:

```python

with logtail.context(order={"external_id": "12345" }):
    logger.info('Processing order')
    # More code here ...
    logger.info('Done.')

```

## Issues

⚠️ **WARNING**: When using FastAPI, you may encounter server initialization issues:
- The server may get blocked when started with `python server/main.py`
- But works fine with `uvicorn server.main:app --reload --host 0.0.0.0`
- This appears to be related to logging in the `__main__` scope
- Avoid using logging statements directly in the main module scope or in `if __name__ == "__main__":` blocks
