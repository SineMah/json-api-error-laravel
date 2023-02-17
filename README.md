# JSON:API error response

## Spec
https://jsonapi.org/examples/#error-objects-basics

## Installation
`composer require sinema/json-api-error`

## Usage

In your Laravel controller:
```php
<?php

namespace App\Http\Controllers;

use Sinemah\JsonApi\Error\Error;
use Sinemah\JsonApi\Error\Responses\Laravel;
use Illuminate\Http\JsonResponse;

class AnyController extends Controller
{
    public function show(): JsonResponse
    {
        return Laravel::get()->json(
            Error::fromArray(
                [
                    'status' => 404,
                    'source' => null,
                    'title' => 'Item not found',
                    'detail' => sprintf('Item %s not found', request('item_uuid')),
                ]
            ),
            404
        );
    }
}
```

Response
```json
{
    "errors": [
        {
            "status": 404,
            "title": "Bike not found",
            "detail": "Bike bd11f048-8663-4d95-8c7a-02a5579b0682 not found in customer data"
        }
    ]
}
```

Build an error stack.
```php
<?php

namespace App\Http\Controllers;

use Sinemah\JsonApi\Error\Error;
use Sinemah\JsonApi\Error\Responses\Laravel;
use Illuminate\Http\JsonResponse;

class AnyController extends Controller
{
    public function show(): JsonResponse
    {
        return Laravel::get()
            ->add(Error::fromArray(['status' => 500, 'code' => 'first_error']))
            ->add(Error::fromArray(['status' => 500, 'code' => 'second_error']))
            ->add(Error::fromArray(['status' => 500, 'code' => 'third_error']))
            ->json();
    }
}
```
Response
```json
{
    "errors": [
        {
            "status": 500,
            "code": "first_error"
        },
        {
            "status": 500,
            "code": "second_error"
        },
        {
            "status": 500,
            "code": "third_error"
        }
    ]
}
```

## Laravel Response
You do not need to pass a status code via the json method. The status code will be fetched from the first error you pushed in the bag.
```php
->json()
```
You can also overwrite the status code with the json method.
```php
->json(null, 401)
```