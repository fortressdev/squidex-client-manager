# Squidex Client Manager

This is a wrapper around the swagger [API][a] provided by Squidex.

> The project is still a in early phase so the featureset is limited but if you
> can generate a token it's usable for you ;-)

Please note all examples below use `cloud.squidex.io` in the examples but you
can easily change out the `https://cloud.squidex.io/api` portion with the
following format `https://<my-server-url>/api/` for self-hosted version.

## Usage

You need to setup the client before using it. The minimial requirement is the
url for the OpenAPI specification and a valid token. 

1. To find the API spec url visit `https://cloud.squidex.io/api/content/<my-app>/docs`
2. For a getting a token see `https://cloud.squidex.io/app/<my-app>/settings/clients`

When you have those two values you can setup the client. but please note that
all examples below assume you are running in an `async` function.

### Setting up the client

```javascript
const { SquidexClientManager } = require('@scanf/squidex-client-manager');
  
const specUrl = 'https://cloud.squidex.io/api/content/<my-app>/swagger/v1/swagger.json';
const token = 'my-secret-token';
  
const client = new SquidexClientManager(specUrl, token);

try {
  await client.ConfigureAsync();
} catch (error) {
  console.log('Failed to setup the CMS. Please check token is setup!');
  console.error(error);
}

```

### Retrieving records

```javascript
const records = await client.RecordsAsync('Articles', {});
console.log(JSON.stringify(records, null, 2));
/* Output:
[squidex-client-manager][debug]: Records(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
{
  "total": 1,
  "items": [
    {
      "id": "ffcbecb0-07a0-45f5-9b8e-bf53059fe25d",
      "createdBy": "subject:5ce8610ec020db00018051c7",
      "lastModifiedBy": "subject:5ce8610ec020db00018051c7",
      "data": {
        "title": {
          "iv": "Hello Squidex"
        },
        "text": {
          "iv": "## Testing markdown support"
        }
      },
      "isPending": false,
      "created": "2019-06-12T17:24:38Z",
      "lastModified": "2019-06-12T17:24:38Z",
      "status": "Published",
      "version": 1
    }
  ]
}
*/
```

### Retrieving a record

```javascript
const records = await client.RecordAsync('Articles', {id: '4bb3a7bb-962d-4183-9ca6-35d170c34f3b'});
console.log(JSON.stringify(records, null, 2));
/* Output: 
[squidex-client-manager][debug]: Record(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
{
  "title": {
    "iv": "Testo-Wed Jun 12 2019 20:11:19 GMT+0200 (Central European Summer Time)"
  }
} */
```
### Creating a record

```javascript
const title = 'My post';
const body = `
## topic 1

Lorem ipsum dolor sit amet, quo ne malis saperet fierent, has ut vivendo
imperdiet disputando, no cum oratio abhorreant. Agam accusata prodesset cu
pri, qui iudico constituto constituam an. Ne mel liber libris expetendis, per
eu imperdiet dignissim. Pro ridens fabulas evertitur ut.
`
const expected = { data: { title: { iv: title }, text: { iv: body } }, publish: true };
const article = await client.CreateAsync('Articles', expected);
console.log(JSON.stringify(article, null, 2))
/* Output:
[squidex-client-manager][debug]: Create(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
{
  "id": "cdbcb9f7-f6f6-4a6a-81d9-0c6f9cf385f8",
  "createdBy": "client:my-blog-squidex:developer",
  "lastModifiedBy": "client:my-blog-squidex:developer",
  "data": {
    "title": {
      "iv": "My post"
    },
    "text": {
      "iv": "\n  ## topic 1\n  \n  Lorem ipsum dolor sit amet, quo ne malis saperet fierent, has ut vivendo\n  imperdiet disputando, no cum oratio abhorreant. Agam accusata prodesset cu\n  pri, qui iudico constituto constituam an. Ne mel liber libris expetendis, per\n  eu imperdiet dignissim. Pro ridens fabulas evertitur ut.\n  "
    }
  },
  "isPending": false,
  "created": "2019-06-12T18:22:51Z",
  "lastModified": "2019-06-12T18:22:51Z",
  "status": "Published",
  "version": 1
}
*/
```

### Deleting a record

```javascript
const deleted = await client.DeleteAsync('Articles', { id: 'cdbcb9f7-f6f6-4a6a-81d9-0c6f9cf385f8' });
console.log(JSON.stringify(deleted, null, 2));
/* Output:
[squidex-client-manager][debug]: Delete(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
{
  "ok": true,
  "url": "https://cloud.squidex.io/api/content/my-blog-squidex/articles/cdbcb9f7-f6f6-4a6a-81d9-0c6f9cf385f8/",
  "status": 204,
  "statusText": "No Content",
  "headers": {
    "date": "Wed, 12 Jun 2019 18:26:10 GMT",
    "connection": "close",
    "set-cookie": "__cfduid=d8ef8efcbcf5e2fb0d137c4ad4f26edf41560363970; expires=Thu, 11-Jun-20 18:26:10 GMT; path=/; domain=.squidex.io; HttpOnly; Secure",
    "etag": "W/2",
    "expect-ct": "max-age=604800, report-uri=\"https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct\"",
    "server": "cloudflare",
    "cf-ray": "4e5ddf1eaef0cae4-ARN"
  },
  "text": {
    "type": "Buffer",
    "data": []
  },
  "data": {
    "type": "Buffer",
    "data": []
  }
}
*/
```

### Updating a record

```javascript
const update = await client.UpdateAsync('Articles', {
  id: '4bb3a7bb-962d-4183-9ca6-35d170c34f3b',
  data: {
    title: { iv: 'the title is updated' },
    text: { iv: `the article text` },
  },
});
console.log(JSON.stringify(update, null, 2));
/* Output:
[squidex-client-manager][debug]: Update(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
{
  "title": {
    "iv": "the title is updated"
  },
  "text": {
    "iv": "the article text"
  }
}
*/

```

### Create or update a record

```javascript
const createOrUpdate = await client.CreateOrUpdateAsync('Articles', {
  id: '4bb3a7bb-962d-4183-9ca6-35d170c34f3b',
  data: {
    title: { iv: 'title here is used as unique value for comparison' },
    text: { iv: 'y' },
  },
}, 'title');
console.log(JSON.stringify(createOrUpdate, null, 2));
/* Output:
[squidex-client-manager][debug]: CreateOrUpdate(Articles, [object Object], title)
[squidex-client-manager][debug]: filter Articles where title eq title here is used as unique value for comparison
[squidex-client-manager][debug]: Records(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
[squidex-client-manager][debug]: Create(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
{
  "id": "3b6c5b1c-51bd-45a2-8c07-736286c71b67",
  "createdBy": "client:my-blog-squidex:developer",
  "lastModifiedBy": "client:my-blog-squidex:developer",
  "data": {
    "title": {
      "iv": "title here is used as unique value for comparison"
    },
    "text": {
      "iv": "y"
    }
  },
  "isPending": false,
  "created": "2019-06-12T18:44:00Z",
  "lastModified": "2019-06-12T18:44:00Z",
  "status": "Draft",
  "version": 0
}
*/
```

### Filtering

The current implementation of filtering only supports comparisons i.e only
`eq`. If you have use case that requests the full support of [OData
Conventions][oc], please reach out by creating a [issue][i].

If you only want to use `eq` then the following example should suffice

```javascript
const input = { data: { title: { iv: 'Hello Squidex' } }, publish: true };
const filter = await client.FilterRecordsAsync('Articles', input, 'title');
console.log(JSON.stringify(filter, null, 2));
/* Output:
[squidex-client-manager][debug]: filter Articles where title eq Hello Squidex
[squidex-client-manager][debug]: Records(Articles, [object Object])
[squidex-client-manager][debug]: GetModelByName(Articles)
[
  {
    "id": "ffcbecb0-07a0-45f5-9b8e-bf53059fe25d",
    "createdBy": "subject:5ce8610ec020db00018051c7",
    "lastModifiedBy": "subject:5ce8610ec020db00018051c7",
    "data": {
      "title": {
        "iv": "Hello Squidex"
      },
      "text": {
        "iv": "## Testing markdown support"
      }
    },
    "isPending": false,
    "created": "2019-06-12T17:24:38Z",
    "lastModified": "2019-06-12T17:24:38Z",
    "status": "Published",
    "version": 1
  }
]
*/
```

## Disclaimer

This project is not affiliated with [Squidex][0] and is an unofficial client.
The project is developed and maintained by [Alexander Alemayhu][twitter].

## Troubleshooting

0. Make sure your client has enough permissions (recommended role for testing is `Developer`).
0. Make sure the model name you are querying is the same in the Squidex schema.
0. Check your token is valid.


[a]: https://docs.squidex.io/guides/02-api
[0]: https://squidex.io/
[twitter]: https://twitter.com/aalemayhu
[oc]: https://docs.squidex.io/guides/02-api#odata-conventions
[i]: https://github.com/scanf/squidex-client-manager/issues/new