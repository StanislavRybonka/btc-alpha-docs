---
title: BTC-Alpha API Reference

language_tabs:
  - python
  - javascript

toc_footers:
  - <a href='https://btc-alpha.com/accounts/register/'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---


# Introduction


Welcome to the BTC-Alpha API docs!


# Sockets Api


> There are two types of order book notification messages

```json
{
  "date": 1485686507.0944622, 
  "oid": 15234,
  "type": "sell", 
  "price": 935.23, 
  "amount": 0.0, 
  "trades": [
    {
      "tid": 14876, 
      "type": "buy",
      "price": "936.00", 
      "amount": "0.1", 
      "o_id": 15201
     }
  ]
}
```

```json
{
  "success": true,
  "date": 1485686413.5688234,
  "oid": 15235, 
  "type": "sell", 
  "price": 935.23, 
  "amount": 0.0123,
  "pair": "BTC_USD",
  "cancelled": true
}
```

You can get live order book updates, using our Sockets Api. 

For receiving notifications, just connect to `wss://btc-alpha.com` and subscribe to necessary channel (Examples: book_BTC_USD, book_ETH_BTC).

Sockets api use engine.io protocol and don`t have authorisation;


# Http Api (v1)


Http api endpoint available on [https://btc-alpha.com/api/v1/](https://btc-alpha.com/api/v1/).

Some methods requires authentication [read below](#authorisation).

<aside class="success">
We have limit in 1 call per seconds to Http Api.
</aside>

## Authorisation

> To generate auth headers, use this code:

```python
import hmac
from time import time
from urllib.parse import urlencode

def get_auth_headers(self, data):
        msg = 'your key' + urlencode(sorted(data.items(), key=lambda val: val[0]))

        sign = hmac.new('your keys secret'.encode(), msg.encode(), digestmod='sha256').hexdigest()

        return {
            'X-KEY': 'your key',
            'X-SIGN': sign,
            'X-NONCE': str(int(time() * 1000)),
        }
```

```javascript
var sortObj = require('sort-object');
var hmacSha256 = require('crypto-js/hmac-sha256');

function serializeObject (object) {
    var str = '';
    for (var key in object) {
        if (str != '')
            str += '&';

        str += key + "=" + encodeURIComponent(object[key]);
    }
    return str;
}

function getAuthHeaders (data) {
    var message = 'your key' + serializeObject(sortObj(data));
    var sign = hmacSha256(message, 'your secret').toString();

    return {
        'X-KEY': 'your key',
        'X-SIGN': sign,
        'X-NONCE': (new Date()).getTime()
    };
}
```

In order for access to private api methods, generate authentication keys [here](https://btc-alpha.com/accounts/api/settings).

All calls to these methods must contain the following headers:

* X-KEY - your key.
* X-SIGN - query's POST data, sorted by keys and signed by your key's "secret" according to the HMAC-SHA256 method.
* X-NONCE - integer value, must be greater then nonce in previous api call.


# Currencies


## List all currencies


```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/currencies/'
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/currencies/', function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "sign": "Ƀ", 
    "short_name": "BTC"
  }, 
  {
    "sign": "Ξ", 
    "short_name": "ETH"
  } 
]
```

### HTTP Request

`GET https://btc-alpha.com/api/v1/currencies/`


# Pairs


## List all pairs

```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/pairs/'
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/pairs/', function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "currency2": "USD", 
    "maximum_order_size": 100000000.00000000, 
    "minimum_order_size": 0.00000001,
    "currency1": "BTC", 
    "name": "BTC_USD", 
    "price_precision": 3
   }
]
```

### HTTP Request

`GET https://btc-alpha.com/api/v1/pairs/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
currency1 | all | Filter by first currency.
currency2 | all | Filter by second currency.


# Wallets


## List own wallets

```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/wallets/',
    'headers': get_auth_headers({})
})
```


```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/wallets/', {headers: getAuthHeaders({})}, function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "currency": "BTC", 
    "balance": 0.00000000, 
    "reserve": 0.00000000
   }
]
```

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`GET https://btc-alpha.com/api/v1/wallets/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
currency_id | all | Filter by currency.


# Orders


Value | Order status name
--------- |  -----------
1 | Active
2 | Canceled
3 | Done


## List all orders


```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/orders/',
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/orders/', function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "amount": 0.00000000, 
    "pair": "BTC_USD", 
    "type": "sell", 
    "status": 3, 
    "price": 845.33000000,
    "id": 11259
  }
]
```

### HTTP Request

`GET https://btc-alpha.com/api/v1/orders/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
type | all | Filter by orders type (sell or buy).
pair | none | Filter by pair.
status | all | Filter by status.
limit | 2000 | Limiting results.

## List own orders

```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/orders/own/',
    'headers': get_auth_headers({})
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/orders/own/', {headers: getAuthHeaders({})}, function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "amount": 0.500000000, 
    "pair": "ETH_USD", 
    "type": "buy", 
    "status": 3, 
    "price": 0.00113000,
    "id": 11249
  }
]
```

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`GET https://btc-alpha.com/api/v1/orders/own/`

### Query Parameters

See [list all orders](#list-all-orders)

## Retrieve single order


```python
import requests

oid = 53189

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/order/{}/'.format(oid),
})
```

```javascript
var request = require('request');

var oid = 53189;

request.get('https://btc-alpha.com/api/v1/order/' + oid + '/', function (error, response, body) {
    // processing response    
});
```

```json
{
  "amount": 0.1250000,
  "pair": "BTC_USD",
  "type": "buy",
  "status": "1",
  "price": 870.69000000, 
  "id": 11259
}
```

### HTTP Request

`GET https://btc-alpha.com/api/v1/order/<pk>/`

## Get orderbook

```python
import requests
from urllib.parse import urlencode

params = { 
    pair: 'BTC_USD',
    limit_bids: 1,
    limit_asks: 1,
}

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/orderbook/?' + urlencode(params),
})
```

```javascript
var request = require('request');

var params = { 
    pair: 'BTC_USD',
    limit_bids: 1,
    limit_asks: 1,
}

request.get('https://btc-alpha.com/api/v1/orderbook/?' + serializeObject(params), function (error, response, body) {
    // processing response    
});
```

```json
{
  "bids": [
    {
      "price": 911.519, 
      "id": 44667, 
      "amount": 0.000446, 
      "timestamp": 1485777324.410015
     }
   ],
  "asks": [
    {
      "price": 911.122, 
      "id": 44647, 
      "amount": 0.001233, 
      "timestamp": 1485777124.415542
    }
  ]
}
```

bids: sell orders, asks, buy orders

### HTTP Request

`GET https://btc-alpha.com/api/v1/orderbook/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
pair | required | Pair name (required).
limit_bids | 100 | Sell orders limit
limit_asks | 100 | Buy orders limit
group | 0 | If set 1, then order will grouped by price


## Create order


```python
import requests

data = {
    type: 'buy',
    pair: 'BTC_USD',
    amount: '1.0',
    price: '870.69'
}

response = requests.request({
    'method': 'post',
    'url': 'https://btc-alpha.com/api/v1/order/',
    'headers': get_auth_headers(data)
})
```

```javascript
var request = require('request');

var data = {
    type: 'buy',
    pair: 'BTC_USD',
    amount: '1.0',
    price: '870.69'    
};

request.post('https://btc-alpha.com/api/v1/order/', {
    form: data,
    headers: getAuthHeaders(data)
}, function (error, response, body) {
    // processing response    
});
```

```json
{
  "type": "buy", 
  "date": 1483721079.51632, 
  "oid": 11268, 
  "price": 870.69000000, 
  "amount": 0.00000000,
  "trades": [
    {
      "type": "sell", 
      "price": 870.69000000, 
      "o_id": 11266, 
      "amount": 0.00010000, 
      "tid": 6049
    }
  ]
}
```

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`POST https://btc-alpha.com/api/v1/order/`

### POST params

Parameter | Description
--------- | -----------
type | Type of order (sell or buy).
pair | Pair of order
amount | Amount of order.
price | Price of order. This param have limited precision (See [pairs](#pairs)).


## Cancel order


```python
import requests

data = {
    order: 63568
}

response = requests.request({
    'method': 'post',
    'url': 'https://btc-alpha.com/api/v1/order-cancel/',
    'headers': get_auth_headers(data)
})
```

```javascript
var request = require('request');

var data = {
    order: 63568
};

request.post('https://btc-alpha.com/api/v1/order-cancel/', {
    form: data,
    headers: getAuthHeaders(data)
}, function (error, response, body) {
    // processing response    
});
```

```json
{
  "order": 63568
}
```

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`POST https://btc-alpha.com/api/v1/order-cancel/`

### POST params

Parameter | Description
--------- | -----------
order | Orders id.


# Exchanges


## List all exchanges


```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/exchanges/',
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/exchanges/', function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "id": 6030, 
    "price": 839.36000000, 
    "pair": "BTC_USD", 
    "type": "sell", 
    "timestamp": 1483705817.735508,
    "amount": 0.00281167
  }
]
```

### HTTP Request

`GET https://btc-alpha.com/api/v1/exchanges/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
pair | all | Filter by pair.
limit | 2000 | Limiting results.

## List own exchanges

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`GET https://btc-alpha.com/api/v1/exchanges/own/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
type | all | Filter by orders type (sell or buy).
pair | all | Filter by pair.
limit | 2000 | Limiting results.


# Deposits


## List own deposits

```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/deposits/',
    'headers': get_auth_headers({})
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/deposits/', {headers: getAuthHeaders({})}, function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "timestamp": 1485363039.18359, 
    "id": 317, 
    "currency": "BTC", 
    "amount": 530.00000000
  }
]
```

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`GET https://btc-alpha.com/api/v1/deposits/`


# Withdraws


Value | Withdraw status name
--------- |  -----------
10 | New
20 | Verified
30 | Approved
40 | Refused
50 | Canceled

## List own made withdraws

```python
import requests

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/v1/withdraws/',
    'headers': get_auth_headers({})
})
```

```javascript
var request = require('request');

request.get('https://btc-alpha.com/api/v1/withdraws/', {headers: getAuthHeaders({})}, function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "id": 403, 
    "timestamp": 1485363466.868539, 
    "currency": "BTC", 
    "amount": 0.53000000, 
    "status": 20
  }
]
```

<aside class="warning">
This method requires authorisation.
</aside>

### HTTP Request

`GET https://btc-alpha.com/api/v1/withdraws/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
currency_id | all | Filter currency.
status | all | Filter by status.

# Charts

```python
import requests
from urllib.parse import urlencode

params = {limit: 1}

response = requests.request({
    'method': 'get',
    'url': 'https://btc-alpha.com/api/charts/BTC_USD/60/chart?' + urlencode(params),
})
```

```javascript
var request = require('request');

var params = {limit: 1}

request.get('https://btc-alpha.com/api/charts/BTC_USD/60/chart?' + serializeObject(params), function (error, response, body) {
    // processing response    
});
```

```json
[
  {
    "volume": 0.262929, 
    "high": 912.236, 
    "low": 910.086, 
    "close": 911.915, 
    "time": 1485777600, 
    "open": 910.424
   }
]
```

Type value | Type name
--------- | -------
1 | 1 minute
5 | 5 minutes
15 | 15 minutes
30 | 30 minutes
60 | 1 hour
240 | 4 hours
D | 1 day

### HTTP Request

`GET https://btc-alpha.com/api/charts/<pair>/<type>/chart`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
limit | 720 | Limiting results.
since | None | Since timestamp.
until | None | Until timestamp.