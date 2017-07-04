# Welcome to Timely

Congratulations! You've made it to the big time! As a
Web developer, your services are in high demand. You
have clients dripping out of your ears and you need an
easy way to track the time you spend each day working
for which client. Then, you want to see how much you
worked per month for a client so you can send them an
invoice. Lovely, lovely billing!

Your friend Michaela has a little app that she wrote to
do just that. She said that she'd share it with you if
you'd make it a SPA because it's just a dumb old Web
app, right now. You decided to take on the challenge
and learn yourself a little AngularJS along the way.

You go over to GitHub and create a fork from the repo,
clone it, and run the Java application after following
the directions in the README file. You open a browser
to [http://localhost:5000](http://localhost:5000) and
start figuring out the application.

**Reminder** \
You remind yourself that all of the
server-side stuff is done. You just have to deal with
the stuff in the browser: the HTML and JavaScript and
CSS. Michaela has alread written the API for you to
use. (Thanks, Michaela!) So, you take a deep breath
and...

## The Login Screen

Well, you haven't logged in, yet, so you get sent to
the login screen. It seems Michaela may have had some
idea of turning this into a product at some point what
with that "Not a user?" box. Hmmm...

![timely - login
screen](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/login-screen.png)

You type in the demo account username of "maria" and
the password "maria". That takes you to the main page
of timely.

## The Time Entry Page

After logging in, you see the main page where you can
enter your daily time that you spend for each of your
clients.

![timely - main screen after
login](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/timely-work-1.png)

You click the "FINISH" button to see what happens.

![timely - main screen after you finish
work](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/timely-work-finish.png)

Well, that seems reasonable: it set the current time
for end of that entry. Work is done for Bret's
Refrigeration.

You select a client from the drop down and click start
to see a new line item appear. You do it again with
another client to see that it completes the previous
entry and starts a new one. That makes sense.

Having discovered all you can with that, you click the
"CLIENTS" tab.

## The Clients Maintenance Page

On the clients page, you see the list of clients and a
big yellowish/light-orangey ADD button.

![timely - clients
list](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/clients-list.png)

You remember that "Active" clients show up in the drop
down on the first page, that you can book work against
"Active" clients. Just on a lark, you click "Allie's
Sweets" and see the edit form.

![timely - edit form for allie's
sweets](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/client-edit-form.png)

You decide to click the "deactivate" button in the
upper right corner and, sure enough, Allie's Sweets
becomes an inactive client. You click the left arrow to
close the edit form and click the big ADD button. You
type "Marigold Cabs" into the client's name box and hit
the Enter key. Sure enough, "Marigold" shows up in your
list.

![timely - clients list iwth marigold cabs in
it](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/clients-list-with-marigold.png)

You go back to the "TODAY'S WORK" tab and check out the
drop down with the clients in it. *Allie's Sweets* is
not there because you made that client inactive, and
*Marigold Cabs* is there because you just added it!

![timely - marigold in drop down, allie's sweets
not](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/marigold-in-drop-down.png)

## The Report Page

You click the "REPORT" tab at the top of the screen,
choose "Zantina Unlimited" from the drop down, and
click the "GENERATE REPORT" button. Sure enough, it
shows the number of hours worked for "Zantina
Unlimited".

![timely - report
page](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/report.png)

On a lark, you generate the report for "Marigold Cabs"
and see nothing. That makes sense: you've done no work
for them.

![timely - empty report page for
Marigold](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/empty-report-for-marigold.png)

## The Logout

So, you click the "SIGN OUT" button. It takes you back
to the login screen.

![timely - login
screen](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/login-screen.png)

## The Sign Up Experience

The only thing left to do is try out that "SIGN UP!"
button. You click it and see the sign up page.

![timely - sign up
screen](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/sign-up-page.png)

You type in your name and a password and click the
"REGISTER" button to see an empty "TODAY'S WORK" page
telling you that you need some active clients before
you can do anything.

![timely - sign up
screen](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/empty-clients-list.png)

That makes total sense.

## Reflections

Well, now that you've seen the application, you decide
to start small and take baby steps on getting the
front-end from static HTML to AngularJS.

You start by creating and using an AngularJS
application.
