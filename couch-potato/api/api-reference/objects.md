# Objects

## BOS Objects

The following objects are used to pass data to, or objects from response messages, the five API endpoints:

* ``[`add_game`](https://infra.peerplays.com/couch-potato/api/api-reference#add-game)``
* ``[`start_game`](https://infra.peerplays.com/couch-potato/api/api-reference#start-game)``
* ``[`add_Score`](https://infra.peerplays.com/couch-potato/api/api-reference#add-score)``
* ``[`finish_Game`](https://infra.peerplays.com/couch-potato/api/api-reference#finish-game)``
* ``[`cancel_Game`](https://infra.peerplays.com/couch-potato/api/api-reference#cancel-game)``

### create <a href="#create" id="create"></a>

Parameters for new BOS incident message to create a game and add to Couch Potato database.

{% tabs %}
{% tab title="Object" %}
| Parameter    | Description                   | Type   | Required |
| ------------ | ----------------------------- | ------ | -------- |
| `sport`      | Sport name                    | String | Yes      |
| `league`     | League (Event Group) of sport | String | Yes      |
| `home`       | Home team name                | String | Yes      |
| `away`       | Away team name                | String | Yes      |
| `start_time` | Start date/time of game       | Date   | Yes      |
| `user`       | Current user id               | Number | Yes      |
{% endtab %}

{% tab title="Example" %}
```csharp
{
  "sport": "Soccer",
  "league": "EPL",
  "home": "Norwich City",
  "away": "Manchester City",
  "start_time": "2020-02-04T18:33:00Z",
  "user": 35,
  "match_id": 60
}
```
{% endtab %}
{% endtabs %}

### in\_progress <a href="#in-progress" id="in-progress"></a>

Parameters for new BOS incident message to start a game and add to Couch Potato database.

{% tabs %}
{% tab title="Object" %}
| Parameter            | Description                   | Type   | Required |
| -------------------- | ----------------------------- | ------ | -------- |
| `sport`              | Sport name                    | String | Yes      |
| `league`             | League (Event Group) of sport | String | Yes      |
| `home`               | Home team name                | String | Yes      |
| `away`               | Away team name                | String | Yes      |
| `start_time`         | Start date/time of game       | Date   | Yes      |
| `whistle_start_time` | Actual time the game started  | Date   | Yes      |
| `match_id`           | Unique match identifier       | Number | Yes      |
{% endtab %}

{% tab title="Example" %}
```csharp
{
  "sport": "Soccer",
  "league": "EPL",
  "home": "Norwich City",
  "away": "Manchester City",
  "start_time": "2020-02-04T18:33:00Z",
  "match_id": 60,
  "whistle_start_time": "2020-02-04T18:40:00Z"
}
```
{% endtab %}
{% endtabs %}

### result  <a href="#result" id="result"></a>

Parameters for new BOS incident message to set the score of a game and add to Couch Potato database.

{% tabs %}
{% tab title="Object" %}
| Parameter    | Description                   | Type   | Required |
| ------------ | ----------------------------- | ------ | -------- |
| `sport`      | Sport name                    | String | Yes      |
| `league`     | League (Event Group) of sport | String | Yes      |
| `home`       | Home team name                | String | Yes      |
| `away`       | Away team name                | String | Yes      |
| `start_time` | Start date/time of game       | Date   | Yes      |
| `home_score` | The score for the home team   | Number | Yes      |
| `away_score` | The score for the away team   | Number | Yes      |
| `match_id`   | Unique match identifier       | Number | Yes      |
{% endtab %}

{% tab title="Example" %}
```csharp
{
  "sport": "Soccer",
  "league": "EPL",
  "home": "Norwich City",
  "away": "Manchester City",
  "start_time": "2020-02-04T18:33:00Z",
  "home_score": 1,
  "away_score": 4,
  "match_id": 60
}
```
{% endtab %}
{% endtabs %}

### finish  <a href="#finish" id="finish"></a>

Parameters for new BOS incident message to finish/complete a game and add to Couch Potato database.

{% tabs %}
{% tab title="Object" %}
| Parameter          | Description                   | Type   | Required |
| ------------------ | ----------------------------- | ------ | -------- |
| `sport`            | Sport name                    | String | Yes      |
| `league`           | League (Event Group) of sport | String | Yes      |
| `home`             | Home team name                | String | Yes      |
| `away`             | Away team name                | String | Yes      |
| `start_time`       | Start date/time of game       | Date   | Yes      |
| `whistle_end_time` | The time the game ended       | Date   | Yes      |
| `away_score`       | The score for the away team   | Number | Yes      |
| `match_id`         | Unique match identifier       | Number | Yes      |
{% endtab %}

{% tab title="Example" %}
```csharp
{
  "sport": "Soccer",
  "league": "EPL",
  "home": "Norwich City",
  "away": "Manchester City",
  "start_time": "2020-02-04T18:33:00Z",
  "match_id": 60,
  "whistle_end_time": "2020-02-04T20:30:00Z"
}
```
{% endtab %}
{% endtabs %}

### canceled <a href="#canceled" id="canceled"></a>

Parameters for new BOS incident message to cancel a game and add to Couch Potato database.

{% tabs %}
{% tab title="Object" %}
| Parameter    | Description                   | Type   | Required |
| ------------ | ----------------------------- | ------ | -------- |
| `sport`      | Sport name                    | String | Yes      |
| `league`     | League (Event Group) of sport | String | Yes      |
| `home`       | Home team name                | String | Yes      |
| `away`       | Away team name                | String | Yes      |
| `start_time` | Start date/time of game       | Date   | Yes      |
| `match_id`   | Unique match identifier       | Number | Yes      |
{% endtab %}

{% tab title="Example" %}
```csharp
{
  "sport": "Soccer",
  "league": "EPL",
  "home": "Norwich City",
  "away": "Manchester City",
  "start_time": "2020-02-04T18:33:00Z",
  "match_id": 60,
}
```
{% endtab %}
{% endtabs %}

## Success Response Objects

### Add Game Success Response  <a href="#add-game-success-response" id="add-game-success-response"></a>

Object attributes for a 200 response from an [add\_game](https://infra.peerplays.com/couch-potato/api/api-reference#add-game) call.

{% tabs %}
{% tab title="Attributes" %}
| Name    | Text                                 |
| ------- | ------------------------------------ |
| status  | Always 200                           |
| title   | Game added                           |
| message | \[home ] v \[away ] - \[start\_time] |
{% endtab %}

{% tab title="Example" %}
```java
{
    "status":"200",
    "title":"Game added",
    "message":"Arsenal v Liverpool - 2020-02-25T01:33:00.000Z"
}
```
{% endtab %}
{% endtabs %}

### Start Game Success Response  <a href="#start-game-success-response" id="start-game-success-response"></a>

Object attributes for a 200 response from an [start\_game](https://infra.peerplays.com/couch-potato/api/api-reference#start-game) call.

{% tabs %}
{% tab title="Attributes" %}
| Name    | Text                                          |
| ------- | --------------------------------------------- |
| status  | Always 200                                    |
| title   | Game started                                  |
| message | \[home ] v \[away ] - \[whistle\_start\_time] |
{% endtab %}

{% tab title="Example" %}
```java
{
    "status":"200",
    "title":"Game started",
    "message":"Chelsea v Chelsea - [whistle_start_time]2020-02-04T18:05:47.736Z"
}
```
{% endtab %}
{% endtabs %}

### Add Score Success Response  <a href="#add-score-success-response" id="add-score-success-response"></a>

Object attributes for a 200 response from an [add\_score](https://infra.peerplays.com/couch-potato/api/api-reference#add-score) call.

{% tabs %}
{% tab title="Attributes" %}
| Name    | Text                                             |
| ------- | ------------------------------------------------ |
| status  | Always 200                                       |
| title   | Scores added                                     |
| message | \[home ] \[home\_score]v \[away ]\[away\_score]  |
{% endtab %}

{% tab title="Example" %}
```java
{
    "status":"200",
    "title":"Scores added",
    "message":"Chelsea 4 v Arsenal 2"
}
```
{% endtab %}
{% endtabs %}

### Finish Game Success Response  <a href="#finish-game-success-response" id="finish-game-success-response"></a>

Object attributes for a 200 response from an [finish\_game](https://infra.peerplays.com/couch-potato/api/api-reference#finish-game) call.

{% tabs %}
{% tab title="Attributes" %}
| Name    | Text                                        |
| ------- | ------------------------------------------- |
| status  | Always 200                                  |
| title   | Game finished                               |
| message | \[home ] v \[away ] - \[whistle\_end\_time] |
{% endtab %}

{% tab title="Example" %}
```java
{
    "status":"200",
    "title":"Game finished",
    "message":"Liverpool v Arsenal - [whistle_end_time]2020-02-03T22:45:00.000Z"
}
```
{% endtab %}
{% endtabs %}

### Cancel Game Success Response  <a href="#cancel-game-success-response" id="cancel-game-success-response"></a>

Object attributes for a 200 response from an [cancel\_game](https://infra.peerplays.com/couch-potato/api/api-reference#cancel-game) call.

{% tabs %}
{% tab title="Attributes" %}
| Name    | Text                |
| ------- | ------------------- |
| status  | Always 200          |
| title   | Game canceled       |
| message | \[home ] v \[away ] |
{% endtab %}
{% endtabs %}
