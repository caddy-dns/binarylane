Binary Lane module for Caddy
============================

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records in zones hosted by [Binary Lane](https://www.binarylane.com.au), an Australian cloud provider.

## Caddy module name

```
dns.providers.binarylane
```

## Configuration

The module needs one thing: an API token from the Binary Lane control panel. Create it under **Account → API Tokens**; the DNS endpoints are covered by a general-purpose token.

Prefer `{env.BINARYLANE_API_TOKEN}` over writing the token into the config, so it is not sitting in a file Caddy reads at startup.

### Caddyfile

```
tls {
	dns binarylane {env.BINARYLANE_API_TOKEN}
}
```

Or, equivalently, with the token as a subdirective:

```
tls {
	dns binarylane {
		api_token {env.BINARYLANE_API_TOKEN}
	}
}
```

`api_base` is also accepted, and defaults to `https://api.binarylane.com.au/v2/`. It exists for testing against a stand-in server and should not normally be set.

### JSON

To use this module for the ACME DNS challenge, [configure the ACME issuer in your Caddy JSON](https://caddyserver.com/docs/json/apps/tls/automation/policies/issuers/acme/) like so:

```json
{
	"module": "acme",
	"challenges": {
		"dns": {
			"provider": {
				"name": "binarylane",
				"api_token": "{env.BINARYLANE_API_TOKEN}"
			}
		}
	}
}
```

## A note on TTL

Binary Lane fixes the TTL of every record at 3600 seconds and rejects any request that tries to change it. The provider therefore never sends a TTL, and a short one asked for by an ACME client is silently served as 3600. This does not affect DNS-01: the challenge record is created, validated and removed, and the TTL only means a resolver may cache the absence of it beforehand. See the [provider README](https://github.com/libdns/binarylane) for the detail.

## Building

```
xcaddy build --with github.com/caddy-dns/binarylane
```
