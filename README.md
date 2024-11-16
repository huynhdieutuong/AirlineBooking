# Flight Booking System
## REST API Documentation (v1)
### Search Flights
#### Step 1: Get airports to show Departure and Destination list
<details>
<summary><code>GET</code> <code><b>/search-service/v1/airports</b></code></summary>

##### Parameters

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | departureCode      |  optional | char(3)   | When show Destination list, pass this params to remove Departure  |


##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `200`         | `application/json`                | `{"code":"200","message":"success","data": airportsData}`              |
> | `400`         | `application/json`                | `{"code":"400","message":"Bad Request", "data": null}`                            |

##### Example cURL

> ```javascript
>  curl -X GET -H "Content-Type: application/json" http://localhost:8888/search-service/v1/airports?departureCode=SGN
> ```

```JSON
{
  "code":"200",
  "message":"success",
  "data": [
    {
      "id": "6ce7c9e9-e5e3-40f5-9902-38a52b0e5fc8",
      "priority": 1000,
      "name": "Việt Nam",
      "airports": [
        {
            "id": "7997e3ad-d4e8-4eb7-b6ee-4a798a61cdd8",
            "code": "SGN",
            "airportGroupId": "6ce7c9e9-e5e3-40f5-9902-38a52b0e5fc8",
            "priority": 1000,
            "name": "Sân bay Tân Sơn Nhất",
            "province": "Tp. Hồ Chí Minh"
        }
      ],
      "total": 1
    }
  ]
}
```
</details>

#### Step 2: Get cheapest prices for each day base on Departure, Destination, Date
<details>
<summary><code>GET</code> <code><b>/search-service/v1/cheapest-prices?flight=&date=</b></code></summary>

##### Parameters

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | flight      |  required | char(3)-char(3)   | flight=DepartureCode-DestinationCode or DestinationCode-DepartureCode |
> | date      |  required | DateTime   | Show all cheapest day prices of a month (Ex: Date=2024-11-16, show all cheapest prices of November 2024, from 16th to 30th)  |


##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `200`         | `application/json`                | `{"code":"200","message":"success","data": cheapestPrices}`              |
> | `400`         | `application/json`                | `{"code":"400","message":"Bad Request", "data": null}`                            |

##### Example cURL

> ```javascript
>  curl -X GET -H "Content-Type: application/json" http://localhost:8888/search-service/v1/cheapest-prices?flight=SGN-HAN&date=2024-11-16
> ```

```JSON
{
  "code":"200",
  "message":"success",
  "data": [
    {
      "date": "2024-11-16",
      "cheapestPrice": 390000
    },
    {
      "date": "2024-11-17",
      "cheapestPrice": 400000
    },
    ...
    {
      "date": "2024-11-29",
      "cheapestPrice": 500000
    },
    {
      "date": "2024-11-30",
      "cheapestPrice": 510000
    },
  ]
}
```
</details>

#### Step 3: Get prices for each flight base on Departure, Destination, Date and Passenger Amount
<details>
<summary><code>GET</code> <code><b>/search-service/v1/prices?flight=&date=&amount=</b></code></summary>

##### Parameters

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | flight      |  required | char(3)-char(3)   | flight=DepartureCode-DestinationCode or DestinationCode-DepartureCode |
> | date      |  required | DateTime   | Show all prices of date  |
> | amount      |  required | int   | Check available seats (if available seats < passenger amount, return disabled=true)  |


##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `200`         | `application/json`                | `{"code":"200","message":"success","data": prices}`              |
> | `400`         | `application/json`                | `{"code":"400","message":"Bad Request", "data": null}`                            |

##### Example cURL

> ```javascript
>  curl -X GET -H "Content-Type: application/json" http://localhost:8888/search-service/v1/prices?flight=SGN-HAN&date=2024-11-16&amount=2
> ```

```JSON
{
  "code":"200",
  "message":"success",
  "data": [
    {
      "flightCode": "VJ197",
      "aircraftName": "Airbus A319",
      "departureTime": "2024-11-16 07:00:00",
      "arrivalTime": "2024-11-16 09:20:00",
      "prices": [
        {
          "class": "business",
          "price": 1990000,
          "disabled": true
        },
        {
          "class": "boss",
          "price": 1590000,
          "disabled": true
        },
        {
          "class": "delux",
          "price": 890000,
          "disabled": false
        },
        {
          "class": "eco",
          "price": 590000,
          "disabled": false
        }
      ]
    },
    {
      "flightCode": "VJ198",
      "aircraftName": "Airbus A320",
      "departureTime": "2024-11-16 22:20:00",
      "arrivalTime": "2024-11-17 00:30:00",
      "prices": [
        {
          "class": "business",
          "price": 1790000,
          "disabled": true
        },
        {
          "class": "boss",
          "price": 1390000,
          "disabled": false
        },
        {
          "class": "delux",
          "price": 790000,
          "disabled": false
        },
        {
          "class": "eco",
          "price": 490000,
          "disabled": false
        }
      ]
    }
  ]
}
```
</details>

### Book a Flight

#### Step 4: Create booking
<details>
<summary><code>POST</code> <code><b>/booking-service/v1/bookings</b></code></summary>

##### Payload

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | flightCode      |  required | char(5)   |  |
> | class      |  required | enum   | business,boss,delux,eco  |
> | passengers      |  required | array   | {firstName, lastName, DOB, gender, phone, email} |

##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `200`         | `application/json`                | `{"code":"200","message":"success", "data": bookingId}`              |
> | `400`         | `application/json`                | `{"code":"400","message":"Bad Request", "data": null}`                            |

##### Example cURL

> ```javascript
>  curl -X POST -H "Content-Type: application/json" http://localhost:8888/booking-service/v1/bookings
> ```
```JSON
payload: {
  "departure": {
    "flightCode": "VJ198",
    "class": "delux",
  },
  "return": {
    "flightCode": "VJ199",
    "class": "delux",
  },
  "passengers": [
    {
      "firstName": "Tuong",
      "lastName": "Huynh",
      "DOB": "1992-01-01",
      "gender": "male",
      "phone": 0908347817,
      "email": "tuonghuynh@gmail.com"
    }
  ],
}
```

```JSON
response: {
  "code":"200",
  "message":"success",
  "data": "49638e96-f4fa-4314-ad54-cc658a286e04"
}
```
</details>

#### Step 5: Payment

### Get History of Booking
#### Get my bookings
<details>
<summary><code>GET</code> <code><b>/booking-service/v1/bookings/my</b></code></summary>

##### Headers

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | Authorization      |  required | string   | Bearer token  |


##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `200`         | `application/json`                | `{"code":"200","message":"success","data": bookings}`              |
> | `400`         | `application/json`                | `{"code":"400","message":"Bad Request", "data": null}`                            |
> | `401`         | `application/json`                | `{"code":"401","message":"Unauthorized", "data": null}`                            |

##### Example cURL

> ```javascript
>  curl -X GET -H "Content-Type: application/json, Authorization: Bearer token" http://localhost:8888/booking-service/v1/bookings/my
> ```

```JSON
{
  "code":"200",
  "message":"success",
  "data": [
    {
      "id": "00b30cf4-d14f-41d0-97f8-e21ea4c9861c",
      "departure": {
        "flightCode": "VJ198",
        "departure": "Tp. Hồ Chí Minh",
        "destination": "Hà Nội",
        "departureTime": "2024-11-16 22:20:00",
        "arrivalTime": "2024-11-17 00:30:00",
        "class": "delux",
        "totalPrice": 790000,
      },
      "return": {
        "flightCode": "VJ199",
        "departure": "Hà Nội",
        "destination": "Tp. Hồ Chí Minh",
        "departureTime": "2024-11-18 22:20:00",
        "arrivalTime": "2024-11-19 00:30:00",
        "class": "delux",
        "totalPrice": 890000,
      },
      "passengers": [
        {
          "firstName": "Tuong",
          "lastName": "Huynh",
          "DOB": "1992-01-01",
          "gender": "male",
          "phone": 0908347817,
          "email": "tuonghuynh@gmail.com"
        }
      ],
      "total": 1680000,
      "createdDate": "2024-11-15",
    }
  ]
}
```
</details>

#### Get tickets by bookingId
<details>
<summary><code>GET</code> <code><b>/booking-service/v1/bookings/bookingId/tickets</b></code></summary>

##### Headers

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | Authorization      |  required | string   | Bearer token  |


##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `200`         | `application/json`                | `{"code":"200","message":"success","data": bookings}`              |
> | `400`         | `application/json`                | `{"code":"400","message":"Bad Request", "data": null}`                            |
> | `401`         | `application/json`                | `{"code":"401","message":"Unauthorized", "data": null}`                            |
> | `403`         | `application/json`                | `{"code":"403","message":"Forbidden", "data": null}`                            |
> | `404`         | `application/json`                | `{"code":"404","message":"Booking not found", "data": null}`                            |

##### Example cURL

> ```javascript
>  curl -X GET -H "Content-Type: application/json, Authorization: Bearer token" http://localhost:8888/booking-service/v1/bookings/00b30cf4-d14f-41d0-97f8-e21ea4c9861c/tickets
> ```

```JSON
{
  "code":"200",
  "message":"success",
  "data": [
    {
      "id": "10b30cf4-d14f-41d0-97f8-e21ea4c9861c",
      "firstName": "Tuong",
      "lastName": "Huynh",
      "gender": "male",
      "flightCode": "VJ198",
      "departure": "Tp. Hồ Chí Minh",
      "destination": "Hà Nội",
      "departureTime": "2024-11-16 22:20:00",
      "arrivalTime": "2024-11-17 00:30:00",
      "class": "delux",
      "seatNumber": "A1"
    },
    {
      "id": "20b30cf4-d14f-41d0-97f8-e21ea4c9861c",
      "firstName": "Tuong",
      "lastName": "Huynh",
      "gender": "male",
      "flightCode": "VJ199",
      "departure": "Hà Nội",
      "destination": "Tp. Hồ Chí Minh",
      "departureTime": "2024-11-18 22:20:00",
      "arrivalTime": "2024-11-19 00:30:00",
      "class": "delux",
      "seatNumber": "A2"
    }
  ]
}
```
</details>