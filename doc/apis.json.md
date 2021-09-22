# Automate the delivery of your API documentation using `apis.json`

We partnered with API Evangelist to launch in 2014 the `apis.json` format at [Gluecon](https://www.programmableweb.com/news/apis.io-and-apis.json-launched-gluecon-to-make-api-discoverability-more-search/2014/05/21). The idea is to build something as simple as `robots.txt` for websites but for APIs.
It does not matter the language used to build the APIs, on which platform it runs or which container it runs.

With the launch of `APIs.json` we also launched APIs.io, the search engine for APIs.
Now we are happy announce you can have a similar experience on your developer portal.
Being able to list all your apis, and let your users search easily through them.

You can see a working demo below:

![search experience](../img/search.gif)

## Prerequesites

* 3scale account with Developer Portal
* a Swagger spec (3scale ActiveDocs) for each service
    * Only **OpenAPI version 2.0** is supported for embedded documentation.
	* The system name of your ActiveDocs instance should match the system name of the corresponding Product in 3scale.

## Obtain APIs.json file
### Use a liquid template
Feel free to use this sample [file](../apis.json):
```
{
  "name": "3scale APIs",
  "description": "This is an inventory of APIs managed by 3scale.",
  "image": "https://static.redhat.com/libs/redhat/brand-assets/latest/corp/logo--hat-only.svg",
  "tags": [
    "APIs"
  ],
  "specificationVersion": "0.14",
  "apis": [
    {% for service in provider.services %}
    {
      "name": "{{ service.name }}",
      "image":"https://static.redhat.com/libs/redhat/brand-assets/latest/corp/logo--hat-only.svg",
      "description": "{{ service.description }}",
      "X-3scale": {
        "service_id":{{ service.id }},
        "swagger_system_name":"{{ service.system_name }}"
      },
      "humanURL": "",
      "baseURL": "",
      "tags": [
        "all"
      ],
      "properties": [],
      "contact": []
    }{% if service != provider.services.last %},{% endif %}
    {% endfor %}
  ],
  "include": [],
  "maintainers": []
}

```
It will capture all APIs under management by your 3scale instance.

### Build a static file
Alternatively, you can build your own `apis.json` file using the [APIs.io builder](http://apis.io/builder).

If you decide to build your own static file, you should add this attribute to each API to ensure that lookups are performed correctly:
```json
"X-3scale": {
	"service_id": {{ service.id }}, //numerical service ID
	"swagger_system_name":"{{ service.system_name }}" //system name of your ActiveDocs instance (default assumes this matches your Product's system name)
}
```

Once you are done, you will be ready to upload it to your developer portal.

## Add APIs.json to Developer Portal

Go under `Developer Portal` section.
Create a new Page named `apis.json`, it has to be under `Root` section.

Under `Advanced options`:

1. Change content-type to `application/json`
2. Make sure liquid tags are **enabled** (if you're using the template above)
3. Make sure layout field is blank

![](../img/DevPortal_create_apis.json_page.png)

## Display search experience

We will now create a new page named `apilist`.
We recommend to place it under `Root` section.

You can copy paste the content of [apilist.html](../apilist.html) from this repo into the CMS.

Under `Advanced options`:

1. Make sure liquid tags are disabled

Save and preview, you should see your list of APIs, a search box and the categories on the right.

## API details experience

We will now add the page `apidetails` to display more details about each API.
This page will be displayed when user clicks on an API block in the list, they will be redirected to `/apidetails?service_id={SERVICE_ID}`.

1. Create a new page `apidetails` under `Root` section.
2. Under `Advanced options`: Enable liquid tags
3. Copy paste content from [apidetails.html](../apidetails.html)

Data for `About` and `Documentation` sections are loaded dynamically from `apis.json` file.

Other sections have placeholders that you can easily modify, they will be common to every API. If you want to make them dynamic for each API, add more fields under `X-3scale` section of `apis.json`.

## Add Plan Signup
Next, we'll add application plan signup to the Search view. Create a new page called `plans.json` which will be used by `apidetails`:

1. Create a new page `plans.json` under `Root` section.
2. Change content-type to `application/json`
3. Under `Advanced options`: Enable liquid tags
4. Make sure layout field is blank
5. Copy paste content from [plans.json](../plans.json)

## Change Navigation to point to our feature
We've just dramatically improved out of the box Documentation! Update the link in the top navbar to point to our new `apilist`:

1. Open Partials -> submenu
2. Change the `href` tags from `/docs` to `/apilist` on lines **16** and **66**
3. Save and Publish
## Test and customize

Your discovery experience is now ready :) Congrats!

The look and feel could be customize using [Bootstrap](https://getbootstrap.com) CSS framework.
