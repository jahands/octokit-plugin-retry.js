# plugin-retry.js

> Retries requests for server 4xx/5xx responses except `400`, `401`, `403`, `404`, and `422`.

[![@latest](https://img.shields.io/npm/v/@octokit/plugin-retry.svg)](https://www.npmjs.com/package/@octokit/plugin-retry)
[![Build Status](https://github.com/octokit/plugin-retry.js/workflows/Test/badge.svg)](https://github.com/octokit/plugin-retry.js/actions?workflow=Test)

## Usage

<table>
<tbody valign=top align=left>
<tr><th>
Browsers
</th><td width=100%>

Load `@octokit/plugin-retry` and [`@octokit/core`](https://github.com/octokit/core.js) (or core-compatible module) directly from [esm.sh](https://esm.sh)

```html
<script type="module">
  import { Octokit } from "https://esm.sh/@octokit/core";
  import { retry } from "https://esm.sh/@octokit/plugin-retry";
</script>
```

</td></tr>
<tr><th>
Node
</th><td>

Install with `npm install @octokit/core @octokit/plugin-retry`. Optionally replace `@octokit/core` with a core-compatible module

```js
const { Octokit } = require("@octokit/core");
const { retry } = require("@octokit/plugin-retry");
```

</td></tr>
</tbody>
</table>

```js
const MyOctokit = Octokit.plugin(retry);
const octokit = new MyOctokit({ auth: "secret123" });

// retries request up to 3 times in case of a 500 response
octokit.request("/").catch((error) => {
  if (error.request.request.retryCount) {
    console.log(
      `request failed after ${error.request.request.retryCount} retries`
    );
  }

  console.error(error);
});
```

To override the default `doNotRetry` list:

```js
const octokit = new MyOctokit({
  auth: "secret123",
  retry: {
    doNotRetry: [
      /* List of HTTP 4xx/5xx status codes */
    ],
  },
});
```

To override the number of retries:

```js
const octokit = new MyOctokit({
  auth: "secret123",
  request: { retries: 1 },
});
```

You can manually ask for retries for any request by passing `{ request: { retries: numRetries, retryAfter: delayInSeconds }}`. Note that the `doNotRetry` option from the constructor is ignored in this case, requests will be retried no matter their response code.

```js
octokit
  .request("/", { request: { retries: 1, retryAfter: 1 } })
  .catch((error) => {
    if (error.request.request.retryCount) {
      console.log(
        `request failed after ${error.request.request.retryCount} retries`
      );
    }

    console.error(error);
  });
```

Pass `{ retry: { enabled: false } }` to disable this plugin.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

[MIT](LICENSE)
