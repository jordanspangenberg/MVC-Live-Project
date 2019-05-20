# ASP.NET MVC Live Project

[TOC]

## Introduction

For six weeks I participated in a group live project coding a web app for a construction company.  This was an already established project so a lot of time was spent debugging, refactoring, and adding features. The project objectives changed over the course of the project and we had to pivot and refocus multiple times. I worked mostly on [back end ](#back-end)stories  but worked on [front end](front-end) stories as well. Compared to my time on the Django projects, I spent much more time debugging and refactoring. 

## Back End

### Refactor Check Code Function

This function had unnecessary, poorly named variables and was much longer and harder to read. Now it is much simpler. Check if the model is valid, query the database to see if there is a matching entry, and if it is allow the user to register and pass along the prepopulated data.

```csharp
        [HttpPost]
        [AllowAnonymous]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CheckCode(CreateUserRequest model)
        {
            if (ModelState.IsValid)
            {
                var createUserRequest =
                    (from requests in db.CreateUserRequests
                        where requests.UserName == model.UserName
                        where requests.ConfirmationCode == 
                        model.ConfirmationCode
                        select requests).First();
                if (createUserRequest != null)
                {
                    return RedirectToAction("Register", createUserRequest) ;
                }
            }

            return View(model);
        }

```

### Get Enum Description

We wanted to get the description from an Enum decorator, Oregon instead of OR, Washington instead of WA. MVC doesn't come with the capability to get an Enum description built in, so we had to build our own extension method. This is the generic solution that can get the description from any Enum, and could be easily repurposed to get the display name as well. 

```csharp 
        public static string GetDescription(this Enum genericEnum)
        {
            var genericEnumType = genericEnum.GetType();
            var memberInfo =
                genericEnumType.GetMember(genericEnum.ToString());

            if (memberInfo != null && memberInfo.Length > 0)
            {
                dynamic _Attribs = memberInfo[0].GetCustomAttributes
                    (typeof(DescriptionAttribute), false);
                if (_Attribs != null && _Attribs.Length > 0) 
                    return ((DescriptionAttribute) _Attribs[0]).Description;
            }

            return genericEnum.ToString();
        }
```



## Front End

### SignalR Chat Capture Debugging

This was a lesson and a reminder to always check your assumptions. Sometimes the answer is glaringly obvious, but you convince yourself it is complicated. The send chat message function was not receiving the message from the chat box. I found the solution when I inspected the HTML and found that there was a zero-width element with the same ID as my chat message text box. I just had to change the ID and it worked immediately. 

```javascript
    $.connection.hub.start().done(function () {

        chat.server.getMessages();

        $('#sendmessage').click(function () {
            var message = $('#messageBox').val();
            // Call the Send method on the hub.
            chat.server.send($('#displayname').val(), message);
            // Clear text box and reset focus for next comment.
            $('#message').val('').focus();
        });
    });
```

### Dashboard View Creation

I created multiple partial views to add to the index page to create a dashboard view. This view uses user roles to restrict certain displays and functions based on whether they are an admin or an employee. ![Dashboard View](https://github.com/jordanspangenberg/MVC-Live-Project/blob/master/img/index_dashboard.png)

## Other Skills Learned

- Working independently and as a team to develop features and fix bugs. 
- Working with Azure DevOps to manage the project.
- Git workflow: Branching, committing, pulling, merging, pushing, and creating a pull request.
- Learning common design patterns and anti-patterns. Suggested by my project lead: [Code Simplicity](https://www.amazon.com/Code-Simplicity-Fundamentals-Max-Kanat-Alexander-ebook/dp/B007NZU848/ref=sr_1_2?crid=1QJ3PGNERYNVV&keywords=code+simplicity&qid=1557355484&s=digital-text&sprefix=code+simplici%2Cdigital-text%2C196&sr=1-2)
