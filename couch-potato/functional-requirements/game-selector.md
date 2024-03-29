# Game Selector

The Game Selector is opened by clicking on the day cell of any calendar. The Game Selector is the engine behind all of the game incidents that are created and then posted to BOS.

![](../../.gitbook/assets/16-a.png)

The Game Selector is both used for creating new games/matches and then moving each game through the following standard incident workflow of create -> in\_progress -> result -> finished.

The selector can also be used to Cancel or Delete games.

## Header

The game selector header displays the following information:

**Captions**

| Text/Image      | Type    | Comments                         |
| --------------- | ------- | -------------------------------- |
| \[league logo]  | Dynamic | The logo of the selected league  |
| \[league name]  | Dynamic |  The name of the selected league |
| \[date]         | Dynamic | The date of the games.           |

**Actions**

| Caption | Type   | Action                   |
| ------- | ------ | ------------------------ |
| X       | Button | Close the game selector. |

## Add Game <a href="#add-game" id="add-game"></a>

To add new game use the input fields at the bottom of the screen and then click on the `ADD` button.

![](../../.gitbook/assets/16-b.png)

**Captions**

| Text/Image | Type   | Comments |
| ---------- | ------ | -------- |
| Start      | Static |          |
| Home Team  | Static |          |
| Away Team  | Static |          |

**Inputs**

| Name      | Type               | Constraints                                                                |
| --------- | ------------------ | -------------------------------------------------------------------------- |
| Start     | Date Selector      |  Any valid date                                                            |
| Home Team | Drop Down selector | Drop down list of all teams associated with the selected sport and league. |
| Away Team | Drop Down selector | Drop down list of all teams associated with the selected sport and league. |

**Actions**

| Caption | Type   | Action                                     |
| ------- | ------ | ------------------------------------------ |
| ADD +   | Button | Add the game to the list of created games. |

**Validation**

| **Exception**                             | Error Message           |
| ----------------------------------------- | ----------------------- |
| No start time                             | Start time not entered  |
| Home Team                                 | No home team selected   |
| Away Team                                 | No away team selected   |
| Home Team and Away Team must be different | Teams must be different |

{% hint style="warning" %}
**Note**: There is no validation to stop the same game from being created twice. The reason for this is because it's common in certain sports to have 'double-headers' where two teams play each other more than once in a day.
{% endhint %}

{% hint style="warning" %}
**Note**: There is no validation to stop a game start time from being in the passed. This is because game start times do change and it maybe necessary to start a game in the selector that has already started in real time.
{% endhint %}

Each new game will have its status set to `Not Started`

A [`create`](broken-reference) incident will be pushed to the BOS instances.

## Start Game

To start a game click on the `Start` button next to the game. The game status will then change to `In Progress`

**Actions**

| Caption  | Type   | Action                   |
| -------- | ------ | ------------------------ |
| `Start`  | Button | Start the selected game. |

An [`in_progress`](broken-reference) incident will be pushed to the BOS instances with the `whistle_start_time` set to the time when the `Start` button was clicked.

## Finish Game

To finish a game enter the score for both home and away teams and click on the `Finish` button next to the game. The game status will then change to `Finished`

**Inputs**

| Name                     | Type     | Constraints      |
| ------------------------ | -------- | ---------------- |
| Home Score               | Text Box | Numeric, max 999 |
| <p>Away</p><p>Score </p> | Text Box | Numeric, max 999 |

**Actions**

| Caption   | Type   | Action                                         |
| --------- | ------ | ---------------------------------------------- |
| `Finish`  | Button | Finish the selected game and record the score. |

A [`result`](broken-reference) incident followed by a [`finish`](broken-reference) incident will be pushed to the BOS instances with the `whistle_end_time` set to the time when the Finish button was clicked, and the result to the home score and away score values.

{% hint style="warning" %}
Note: It's not possible to corrects scores and re-send them to BOS. For this reason the `finish` incident is sent immediately after the `result` incident as a result of just clicking on the `Finish` button.
{% endhint %}

## Cancel Game

Any game can be cancelled as long as it's either `Not Started` or `In Progress.`

To cancel a game click on the `Cancel` text next to the game.&#x20;

A confirmation message will be shown.&#x20;

![](../../.gitbook/assets/16-c.png)

Click on `Yes` to cancel the game (game status will then change to `Canceled)` or `No` to to return without canceling.

A `canceled` incident will be sent to BOS.

{% hint style="info" %}
**Tip**: for the purposes of BOS incidents 'canceled' can also be interpreted as postponed but not as delayed. A delayed game is expected to restart. But once a game has been canceled it can't be restarted. If a game is canceled and then played the following day it would have to re-created with the new start time.
{% endhint %}

## Delete Game

A game can only be deleted if it hasn't been started (has a status of `Not Started`).

To delete a game click on the Delete text next to the game.&#x20;

A confirmation message will be shown.&#x20;

![](../../.gitbook/assets/16-d.png)

Click on `Yes` to delete the game (game will be removed) or `No` to to return without deleting.

If a game is deleted than a `canceled` incident must also be sent to BOS so that BOS can tag the game in the same way as a canceled game.

{% hint style="warning" %}
**Note**: The difference between a canceled game and a deleted game is that a deleted game is basically a game that was entered in error and once deleted is removed from the database so it can be re-entered correctly if needed. A canceled game is a proper game that for one reason or other doesn't take place after being created correctly.
{% endhint %}

## Selector Grid

The selector grid is where all games are recorded as they get entered and moved through the workflow.

The selector grid is made up as follows:

| Column     | Type              | Description                                                                                                                   |
| ---------- | ----------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Start      | Text              | Start time of the game                                                                                                        |
| Game       | Text              | Home team v Away team with logos                                                                                              |
| Home Score | Input             | The home team score.                                                                                                          |
| Away Score | Input             | The away team score.                                                                                                          |
| Actions    | Button/Hyperlinks | <p>Changes according to the status of a game. Available options are:</p><ul><li>Start<br>Finish<br>Cancel<br>Delete</li></ul> |
| Status     | Caption           | <p>The status of the game, one of:</p><ul><li><p>Not Started</p><p>In Progress</p><p>Finished</p><p>Cancelled</p></li></ul>   |
