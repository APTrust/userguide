# APTrust Registry

The APTrust Registry will be replacing Pharos in 2022. We plan to have Registry running in our demo environment by late summer, and in production later in the year.

Pharos, in its old age, has become slow and unmaintainable. The new Registry is faster, easier to maintain and cheaper to run. It also has a cleaner, more modern and intuitive user interface.

The Registry also supports new features, including alerts and multiple preservation storage providers (AWS, Wasabi, and possibly others in the future).

## REST API

You'll find interactive documentation for the Registry REST API at [https://aptrust.github.io/registry/](https://aptrust.github.io/registry/){target=_blank}. If you're curious, you can compare this to the old [Pharos API](https://aptrust.github.io/registry/){target=_blank}.

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
