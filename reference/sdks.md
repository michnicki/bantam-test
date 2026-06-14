---
title: SDKs
description: Official and community client libraries for the Pigeon Post API, with install commands and minimal dispatch examples.
icon: package
sidebar_position: 1
---

# SDKs

Talk to the Pigeon Post API through a typed client instead of hand-rolling HTTP. The official libraries handle authentication, retries with idempotency keys, pagination, and webhook signature verification for you.

## Available libraries

| Language | Package | Install | Support |
| --- | --- | --- | --- |
| Node / TypeScript | `@pigeonpost/node` | `npm i @pigeonpost/node` | Official |
| Python | `pigeonpost` | `pip install pigeonpost` | Official |
| Go | `github.com/pigeonpost-community/pigeonpost-go` | `go get github.com/pigeonpost-community/pigeonpost-go` | Community |
| Ruby | `pigeonpost-rb` | `gem install pigeonpost-rb` | Community |

> Community libraries are maintained by the wider flock, not by the Pigeon Post team. They are great, but they may lag the official clients on the newest endpoints.

## Minimal dispatch

Each example below authenticates with a test key, dispatches a single pigeon, and prints the resulting `pgn_…` id.

### Node

```js
import { PigeonPost } from "@pigeonpost/node";

const pigeonpost = new PigeonPost("pgn_test_4f3c9a2b7e1d8c0a5b6f");

const pigeon = await pigeonpost.pigeons.dispatch({
  recipient: "ada@example.com",
  message: "Wing and a prayer.",
});

console.log(pigeon.id);
```

### Python

```python
from pigeonpost import PigeonPost

client = PigeonPost(api_key="pgn_test_4f3c9a2b7e1d8c0a5b6f")

pigeon = client.pigeons.dispatch(
    recipient="ada@example.com",
    message="Wing and a prayer.",
)

print(pigeon.id)
```

### Go (community)

```go
package main

import (
	"context"
	"fmt"

	pigeonpost "github.com/pigeonpost-community/pigeonpost-go"
)

func main() {
	client := pigeonpost.New("pgn_test_4f3c9a2b7e1d8c0a5b6f")

	pigeon, err := client.Pigeons.Dispatch(context.Background(), &pigeonpost.DispatchParams{
		Recipient: "ada@example.com",
		Message:   "Wing and a prayer.",
	})
	if err != nil {
		panic(err)
	}

	fmt.Println(pigeon.ID)
}
```

### Ruby (community)

```ruby
require "pigeonpost"

client = PigeonPost::Client.new(api_key: "pgn_test_4f3c9a2b7e1d8c0a5b6f")

pigeon = client.pigeons.dispatch(
  recipient: "ada@example.com",
  message: "Wing and a prayer."
)

puts pigeon.id
```

## Versioning and support

The official SDKs follow [semantic versioning](https://semver.org). The API itself is versioned in the URL (`/v1`); a new SDK minor or patch release never changes API behavior on its own. We support the two most recent major versions of each official SDK and aim to ship support for new endpoints within a week of their release.

See the [CLI](/reference/cli) for command-line workflows, or the [Glossary](/reference/glossary) for terminology.
