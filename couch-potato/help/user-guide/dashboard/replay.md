# Replay

The replay screen is displayed by clicking on the `REPLAY` button on the [Dashboard](./) header.

![](../../../../.gitbook/assets/19-a.png)

The purpose of the Replay feature is to give you a manual way to send, or re-send, game create incidents to all of the BOS endpoints if for any reason they weren't correctly sent before.

![](../../../../.gitbook/assets/19-b.png)

Normally you won't need to use this feature very often as a create incident is automatically sent every time a game is created. But there could be occasions when the application correctly records a game as being created but the information isn't recorded by the BOS nodes. If that happened then running a Replay will 'flush' all the games between the start and end dates and send create incidents to the BOS nodes a second time.

{% hint style="danger" %}
**Important**: The Replay feature can only be used for games that are not yet started. Once a game is started a new create incident would be ignored.
{% endhint %}

You can select sports and leagues individually using check-boxes, or select / de-select all sports and leagues by using the Select All checkbox/toggle.

Set the range of data to be replayed by entering values in the Start and End fields.
