# APTrust Registry

The APTrust Registry will be replacing Pharos in 2022. We plan to have Registry running in our demo environment by late summer, and in production later in the year.

Pharos, in its old age, has become slow and unmaintainable. The new Registry is faster, easier to maintain and cheaper to run. It also has a cleaner, more modern and intuitive user interface.

The Registry also supports new features, including alerts and multiple preservation storage providers (AWS, Wasabi, and possibly others in the future).

## REST API

You'll find interactive documentation for the Registry REST API at [https://aptrust.github.io/registry/](https://aptrust.github.io/registry/){target=_blank}. If you're curious, you can compare this to the old [Pharos API](https://aptrust.github.io/registry/){target=_blank}.

### Changes from Pharos

The primary API changes from Pharos to Registry include:

* Change in endpoint prefix from **/member-api/v2/** to **/member-api/v3/**
* Use of numeric IDs in URLs instead of object and file identifiers.
* Changes to some filter and sort parameters in endpoints that return lists of records. Registry includes more filter and sort options than Pharos.
* Minor changes to JSON structure of responses. Registry generally returns richer data that is easier to understand.

The change from identifiers to IDs deserves special mention. In the Pharos REST API, you would request file information with a URL like this:

`GET /member-api/v2/files/test.edu/my-object/data/my-file.pdf`

The corresponding request in the Registry API looks like this:

`GET /member-api/v3/files/21145`

The reason for this change is that file identifiers contain slashes, just like URL path identifiers. They also often contain characters that are illegal in URLs and require special encoding. In addition, Pharos URLs could contain query strings after the embedded file identifer.

In practice, URLs with long, messy file names followed by query strings became nearly impossible to parse. The [pattern](https://github.com/APTrust/pharos/blob/master/config/routes.rb#L53-L54){target=_blank} extract embedded file identifiers from URLs became unmaintainable, and still cannot match a number of identifiers in our system.

This change creates a problem. Object and file identifiers are semantic and knowable to depositors. Numeric IDs are opaque and unknowable. So how can you can map the known to the unknown?

Easy. These requests will return object and file records that include IDs:

`GET /member-api/v3/objects?identifier=<object-identifier>`

`GET /member-api/v3/files?identifier=<file-identifier>`

For more information on specific endpoints, see the [Registry Swagger docs](https://aptrust.github.io/registry/){target=_blank} or the [API Changes](api_changes.md) page.

## Generating a Registry REST Client

You can generate a Registry API client for most commonly used languages by following these steps:

1. Go to the [online Swagger editor](https://editor.swagger.io){target=_blank}.
2. Select **File > Import URL** from the top menu.
3. Enter the URL https://raw.githubusercontent.com/APTrust/registry/master/member_api_v3.yml and click **OK**.
4. Click **Generate Client** from top menu.
5. Choose the programming language you want to use.

The Swagger editor will generate a client that you can use in your local scripts and workflows. Languages include Java, C#, Go, Python, PHP, Ruby, and more.

Because the Registry's API documentation follows the OpenAPI version 3.0 standard, you can use the OpenAPI generator of your choice. The OpenAPI tools website maintains a [list of SDK generators](https://openapi.tools/#sdk){target=_blank}. In addition, the [OpenAPI Generator project](https://openapi-generator.tech/){target=_blank} provides generators for more than [fifty languages](https://openapi-generator.tech/docs/generators){target=_blank}.

## Documentation Links

* [Interactive Swagger Docs](https://aptrust.github.io/registry/){target=_blank}
* [Human-Readable OpenAPI Source](https://github.com/APTrust/registry/blob/master/member_api_v3.yml){target=_blank}
* [Machine-Readable OpenAPI Source](https://raw.githubusercontent.com/APTrust/registry/master/member_api_v3.yml){target=_blank}
