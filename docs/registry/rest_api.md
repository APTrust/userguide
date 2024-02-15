# Registry REST API

The Registry REST API enables you to programmatically query information about intellectual objects, files, premis events, and work items.

For safety purposes, the API is primarily read-only. It does not allow object or file deletion, though it does allow you to request object restoration.

The most common uses of the API are:

* Querying for an inventory of intellectual objects
* Querying for an inventory of files
* Checking the status of pending work items, such as ingests and restorations

!!! warning "Perl API Clients"
    The APTrust Registry is protected against distributed denial of service (DDOS) attacks by CloudFlare. CloudFlare may refuse access to Perl LWP clients. To get around this, you can set the User-Agent name of your LWP client like so: `$ua->agent('Mozilla/5.0');` Another alternative is to use the WWW::Mechanize package as described in this [StackOverflow thread](https://stackoverflow.com/questions/29057331/waiting-for-cloudflare-ddos-protection-lwp-perl){target=_blank}.

## Getting an API Key

To use the API, you'll need to get an API token from Registry. Follow these steps:

1. Log in to Registry.

1. Click the user icon in the upper right corner to see your __My Account__ page.

1. Click the __Get API Key__ button.

![My Account Page](../img/registry/MyAccount.png)

!!! note "Separate Keys for Demo and Production Repositories"
    The key you generate on https://demo.aptrust.org will only work on the demo server. The key you generate on https://repo.aptrust.org will only work on the production server.

## Using Your Key to Connect to the REST API

To connect to the REST API, send your Registry login email address and your API key in the following request headers:

```
X-Pharos-API-User: user@example.com
X-Pharos-API-Key: topsecretapikey
```

## REST API Documentation

You'll find interactive documentation for the Registry REST API at [https://aptrust.github.io/registry/](https://aptrust.github.io/registry/){target=_blank}. If you're curious, you can compare this to the old [Pharos API](./api-changes){target=_blank}.

The Registry API includes a number of new and updated endpoints. See the [API Changes](api_changes.md) page for details.

## Generating a Registry REST Client

You can generate a Registry API client for most commonly used languages by following these steps:

1. Go to the [online Swagger editor](https://editor.swagger.io){target=_blank}.
2. Select **File > Import URL** from the top menu.
3. Enter the URL https://raw.githubusercontent.com/APTrust/registry/master/member_api_v3.yml and click **OK**.
4. Click **Generate Client** from top menu.
5. Choose the programming language you want to use.

The Swagger editor will generate a client that you can use in your local scripts and workflows. Languages include Java, C#, Go, Python, PHP, Ruby, and more.

Because the Registry's API documentation follows the OpenAPI version 3.0 standard, you can use the OpenAPI generator of your choice. The OpenAPI tools website maintains a [list of SDK generators](https://openapi.tools/#sdk){target=_blank}. In addition, the [OpenAPI Generator project](https://openapi-generator.tech/){target=_blank} provides generators for more than [fifty languages](https://openapi-generator.tech/docs/generatorshttps://jobs.lever.co/dbtlabs/6886cf2b-2950-404e-b30a-25414d792e77){target=_blank}.

## Documentation Links

* [Interactive Swagger Docs](https://aptrust.github.io/registry/){target=_blank}
* [Human-Readable OpenAPI Source](https://github.com/APTrust/registry/blob/master/member_api_v3.yml){target=_blank}
* [Machine-Readable OpenAPI Source](https://raw.githubusercontent.com/APTrust/registry/master/member_api_v3.yml){target=_blank}
