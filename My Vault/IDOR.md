#webattacks

IDOR stands for **Insecure Direct Object Reference**.

It is an access control vulnerability. It happens when web server does not perform checks on whether the user is authorized to access the data.

For e.g.: Suppose we need to supply our package ID to track our Amazon Package. If the package ID is a sequential number, if we try to check in for any other number which is not ours and we are able to access it, it is basically IDOR.

**Authentication**: The process by which you verify who you are
**Authorization**: The process by which the web servers verify your permissions.

**Vertical Privilege Escalation:** Privilege Escalation which allows you to use more features. For e.g. Admin Panel, Admin actions, etc.

**Horizontal Privilege Escalation:** Privilege Escalation which allows yo to view data which is not yours using an allowed feature.

IDOR usually relates to Horizontal Privilege Escalation.


