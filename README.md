# Verifying WAF Issue With Streaming Responses

NextJs uses streams to send data asynchronously to web clients.

When a NextJs application is deployed behind a Coraza WAF extended Caddy server, streamed responses are hindered by the SecRuleEngine. This repo is to reproduce the issue.

- The example application is a simple counter with a delayed response 
- When clicking on the text _Increment count_ there is a request sent to the server
- During the delay, the bottom text is expected to change to _Loading..._, which is equivalent with a NextJs rendered spinner

## Non Working Response

```
   docker compose up -d
```
- Go to https://localhost and accept the unsafe environment
- The page will "freeze" until the NextJs server is ready
- When clicking on "Increment count" the page is doing nothing until the response is coming through
- We do expect the bottom text to change to _Loading..._ and it doesn't happen as the response is stuck somewhere in Coraza/ModSecurity.

## Working Response

Now 
```
   docker compose down
```
and set the config `SecRuleEngine On` in `caddy/etc/Caddyfile` to `SecRuleEngine Off` and then restart the compose
```
   docker compose up -d
```

- Let's go to https://localhost once again
- The page appear with _Loading..._ in the bottom
- When clicking on _Increment count_ the bottom text change to _Loading..._ and this is the expected behaviour

## Additional Info

- We've tried to run SecRuleEngine with no rules added
- We've tried rules in phase 3 that allow the response disregarding what it is
- We've tried SecRuleEngine with no config at all

