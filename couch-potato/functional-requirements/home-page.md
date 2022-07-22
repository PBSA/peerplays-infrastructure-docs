# Home Page

The home page will be the first page to load and from where the user will be able to login or create an account.

![](../../.gitbook/assets/4.png)

### **Inputs**

| Name      | Constraints                               | Placeholder Text |
| --------- | ----------------------------------------- | ---------------- |
| User Name | <p>Max Length: 24</p><p>Min Length 8</p>  | User name        |
| Password  | <p>Max Length: 40</p><p>Min Length: 8</p> | Password         |

### **Actions**

| Caption        | Type   | Action                                                                    |
| -------------- | ------ | ------------------------------------------------------------------------- |
| LOGIN          | Button | Validate user name and password and then open the [Dashboard](dashboard/) |
| Create Account | Text   | Open the [Create Account](create-account.md) screen                       |

### **Validation**

| **Exception**                    | Error Message                |
| -------------------------------- | ---------------------------- |
| No user name                     | Username not entered         |
| No password                      | Password not entered         |
| Password or user name is invalid | Invalid username or password |

### **Assets**

![](../../.gitbook/assets/5.png)
