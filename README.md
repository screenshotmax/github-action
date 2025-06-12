## ScreenshotMAX GitHub Action

This GitHub Action allows developers to securely call the [ScreenshotMAX API](https://screenshotmax.com) to render websites as screenshots.
It automatically generates an HMAC SHA256 signature, sends the request with your querystring, and saves the response as an artifact.

✅ Useful for visual testing, website archiving, monitoring, reporting, and automation pipelines.

---

## 🔧 Inputs

| Name         | Required | Description |
|--------------|----------|-------------|
| `access_key` | ✅        | Your ScreenshotMAX public API access key |
| `secret_key` | ✅        | Your ScreenshotMAX secret API key (used for signature) |
| `querystring` | ✅       | The full query string for the API call (e.g. `url=https://example.com&format=png`) |

---

## How Signature Works

To ensure secure API access, this action generates a signature like this:

signature = HMAC_SHA256(raw_string = access_key={access_key}&{querystring}, key = secret_key )

> Example:
> - `access_key`: `abc123`
> - `querystring`: `url=https://example.com&format=png`
> - `raw_string`: `access_key=abc123&url=https://example.com&format=png`
> - `signature = HMAC_SHA256(raw_string, secret_key)`

This signature is then appended to the request as an additional parameter:  
`...&signature=YOUR_SIGNATURE`

---

## Output

If the API call succeeds (`HTTP 200`), the screenshot is saved locally as:
output.png | output.jpg | ...


The filename depends on the `format=` in your querystring.

The file is then uploaded as a GitHub Actions artifact.

---

## Example Usage

```yaml
name: Take Website Screenshot

on:
  workflow_dispatch:
    inputs:
      access_key:
        required: true
        type: string
      secret_key:
        required: true
        type: string
      querystring:
        required: true
        type: string

jobs:
  screenshot:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Run ScreenshotMAX
        uses: your-org/screenshotmax-action@v1
        with:
          access_key: ${{ github.event.inputs.access_key }}
          secret_key: ${{ github.event.inputs.secret_key }}
          querystring: ${{ github.event.inputs.querystring }}

      - name: Upload result
        uses: actions/upload-artifact@v4
        with:
          name: screenshot
          path: output.*
