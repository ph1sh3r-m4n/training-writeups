# Lab: Unprotected admin functionality with unpredictable URL

## LAB DESC
```
This lab has an unprotected admin panel. It's located at an unpredictable location, but the location is disclosed somewhere in the application.

Solve the lab by accessing the admin panel, and using it to delete the user carlos.
```
## SOLUTION

So the admin access page is obsfucated to hide from attackers but I found the path in a `js` script.

`/admin-gxfcam` is the path to admin access, it was retrieved from 
```javascript

var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-gxfcam');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
```
> LAB SOLVED
