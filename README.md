# JobPlacement

## Introduction

During the Last two weeks of the Tech Academy course. We work on a live project, this was a full scale MVC web application for a theater written in C#. This opportunity allowed me to get real experience building a full project including debugging, refactoring, and adding new features. I had to orient myself to a project that was already in full swing and far bigger than any previous assignment. I also had the chance to work on plenty of front-end design. Because of the team environment, I also gained practical experience in sprint planning, stand-ups, and proper version control, along with experience in managing merge conflicts.

Below are descriptions of some of the stories I worked on including code snippets and nav links.

## Back End Stories

### Capture PromoPhoto For Production

We had a helper method for uploading images and converting them to byte array formats. The byte array gets stored in the database. 

I invoked this helper method in the productions controller to be able to add a promotion photo to the production class when a user wants to create or edit a production. 

This particular statement is taking in the user input of "upload", which is the image file given by the user. Then testing to see if indeed the user has given a file to work on with the "if" statement, and then calling the helper method to convert that information into bytes for the database.

``` C#
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult Create([Bind(Include = "ProductionId,Title,Playwright,Description,OpeningDay,ClosingDay,PromoPhoto,ShowtimeEve,ShowtimeMat,
TicketLink,Season,IsCurrent,IsWorldPremiere,ShowDays")] Production production, HttpPostedFileBase upload)
        {
            if (ModelState.IsValid)
            {
                if (upload != null)
                {
                    var promoPhoto = ImageUploadController.ImageBytes(upload, out string _64);
                    production.PromoPhoto = promoPhoto;
                }
                db.Productions.Add(production);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(production);
        }
 ```       
### Debugging the Calendar

The Calendar Events controller was not allowing for edits of existing items. 

To solve this I created some dummy events to place in the calendar and then used breakpoints in the controller to see what was happening to the data upon edit.
                
```C#

[Authorize(Roles = "Admin")]
        public ActionResult Edit(int? id)
        {
            
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            CalendarEvent calendarEvents = db.CalendarEvent.Find(id);
            
            if (calendarEvents == null)
            {
                return HttpNotFound();
            }
            return View(calendarEvents);
        }

        
        [HttpPost]
        [ValidateAntiForgeryToken]
        [Authorize(Roles = "Admin")]
        public ActionResult Edit([Bind(Include = "EventId,Title,StartDate,EndDate,TicketsAvailable,ProductionId,RentalRequestId")] CalendarEvent calendarEvents)
        {     
            if (ModelState.IsValid)
            {                
                db.Entry(calendarEvents).State = EntityState.Modified;
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View();
        }
```
### Solution

Through debugging I discovered that the view was lacking a way to post the information, and because it was not listing the EventId on the front end. It was unable to pass it along for a database update. the fix simply required a FormMethod.Post, as well as a way to recieve the item from the model such as

```cshtml

@using (Html.BeginForm("Edit", "CalendarEvents", null, FormMethod.Post, new { enctype = "multipart/form-data" }))

 @Html.Hidden("EventId", Model.EventId)
 
 ```
 
 
 ## Front End Stories
 
 ### Registration Page
 
 The registration page was in need of some re-design. At the time it was using bootstrap cards for the format, and it wasn't using the colors of the application theme. As requested, I got rid of the card for an edge-to-edge presentation, cleaned it up for a more professional look, added some hover effects, and made sure to implement the right color scheme.
 
 ```cshtml
 
 @model TheatreCMS.Models.RegisterViewModel
@{
    ViewBag.Title = "Register";
}

<br />
<h2>@ViewBag.Title.</h2>

@using (Html.BeginForm("Register", "Account", FormMethod.Post, new { @class = "form-horizontal", role = "form" }))
{
    @Html.AntiForgeryToken();
        <h4 class="heading">Create a new account.</h4>
        <p class="small-heading"> If you already have an account, please <a href="~/Account/Login">Log in</a>.</p>
        <hr />
@Html.ValidationSummary("", new { @class = "text-danger" })
<div class="palette-body">
    
    <h5 class="registerTextHead center">Account Info</h5>
    
        <div class="row ml-5">
            <div class="col ml-5">
                <div class="registerText">
                    @Html.LabelFor(m => m.FirstName, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.FirstName, new { @class = "form-control rounded-0" })
                    </div>
                </div>
                <div class="registerText">
                    @Html.LabelFor(m => m.LastName, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.LastName, new { @class = "form-control rounded-0" })
                    </div>
                </div>
                <div class="registerText">
                    @Html.LabelFor(m => m.Email, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.Email, new { @class = "form-control rounded-0" })
                    </div>
                </div>
            </div>
            <div class="col">
                <div class="registerText">
                    @Html.LabelFor(m => m.UserName, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.UserName, new { @class = "form-control rounded-0" })
                    </div>
                </div>
                <div class="registerText">
                    @Html.LabelFor(m => m.Password, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.PasswordFor(m => m.Password, new { @class = "form-control rounded-0" })
                    </div>
                </div>
                <div class="registerText">
                    @Html.LabelFor(m => m.ConfirmPassword, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.PasswordFor(m => m.ConfirmPassword, new { @class = "form-control rounded-0" })
                    </div>
                </div>
            </div>
        </div>
        <br />
        <hr />
        <h5 class="registerTextHead center">Address</h5>
        
        <div class="row ml-5">
            <div class="col ml-5">
                <div class="registerText">
                    @Html.LabelFor(m => m.StreetAddress, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.StreetAddress, new { @class = "form-control rounded-0" })
                    </div>
                </div>
                <div class="registerText">
                    @Html.LabelFor(m => m.City, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.City, new { @class = "form-control rounded-0" })
                    </div>
                </div>
            </div>
            <div class="col">
                <div class="registerText">
                    @Html.LabelFor(m => m.State, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.State, new { @class = "form-control rounded-0" })
                    </div>
                </div>
                <div class="registerText">
                    @Html.LabelFor(m => m.ZipCode, new { @class = "control-label" })
                    <div class="registerInput">
                        @Html.TextBoxFor(m => m.ZipCode, new { @class = "form-control rounded-0" })                        
                    </div>
                    <br/>
                </div>
            </div>
        </div>           
    <br />   
        <div class="form-group center">            
                <button type="submit" class="btn btn-dark registerButton w-25  mb-2" value="Register">Register</button>            
        </div>
    
</div>
<br />
}

@section Scripts {
    @Scripts.Render("~/bundles/jqueryval")
}

```

When I began this user story, the registration page looked like this.

![Before Photo](https://github.com/LewisCWarren/JobPlacement/blob/master/ScreenshotFull(1).png)


### Final Product
My final product looks like this

![After Photo](https://github.com/LewisCWarren/JobPlacement/blob/master/ScreenshotFull(2).png)

## Other Skills Developed

* Working with a group of developers to identify front and back end bugs to the improve usability of an application.
* Managing merge conflicts.
* Improving ability to self teach through google research and tutorials.
 
