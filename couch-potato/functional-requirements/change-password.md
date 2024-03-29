# Change Password

The Change Password screen is opened by clicking on the `Change Password` menu item in the [account menu.](dashboard/account-menu.md)

![](../../.gitbook/assets/17.png)

**Captions**

| Text              | Type   | Comments |
| ----------------- | ------ | -------- |
| Change Password   | Static |          |
| Required fields\* | Static |          |

**Inputs**

| Name             | Constraints                               | Placeholder Text |
| ---------------- | ----------------------------------------- | ---------------- |
| Current Password | <p>Max Length: 40</p><p>Min Length: 8</p> | Current Password |
| New Password     | <p>Max Length: 40</p><p>Min Length: 8</p> | New Password     |
| Confirm Password | <p>Max Length: 40</p><p>Min Length: 8</p> | Confirm Password |

**Actions**

| Caption         | Type   | Action                                                                                  |
| --------------- | ------ | --------------------------------------------------------------------------------------- |
| CHANGE PASSWORD | Button | Validate all fields then update the password and return to the [Dashboard](dashboard/)  |
| X               | Image  | Close the screen without adding a new account and return to the [Dashboard](dashboard/) |

**Validation**

| **Exception**                   | Error Message                                       |
| ------------------------------- | --------------------------------------------------- |
| No current password             | Current password not entered                        |
| Current password is wrong       | Current password is incorrect.                      |
| No new password                 | New password not entered                            |
| New password too short          | New password must be at least 8 characters          |
| No confirm new password         | Confirm new password not entered.                   |
| Confirm new password too short  | Confirm new password must be at least 8 characters  |
| New and confirm new don't match | New password and confirm new password are different |

{% hint style="warning" %}
**Note**: For the first release there will be no additional validation on the password format for strength or special characters etc. The only constraint is that the length must be >=8 and <= 40 characters.
{% endhint %}
