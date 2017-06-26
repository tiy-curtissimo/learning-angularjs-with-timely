# Convert the Sign-Up Card

You just created two AngularJS components and have all
the tools and knowledge you need to do this on your
own.

Doing the same thing that you've done on
`session/login.html`, open `users/form.html` which
has the card on there that allows a person to sign
up for Timely.

1. Add your `app.module.js` to this page
1. Configure the page to be an AngularJS application
   with the `ng-app` attribute
1. Create `.js` and `.html` files for a component
   named `sign-up-card` and organize them correctly
1. Extract the HTML from `users/form.html` for the
   `.html` contents of the component
1. Replace the HTML in `users/form.html` with your
   custom `sign-up-card` component
1. Write your controller to `POST` the new user's
   username and password to the path
   `/api/users`
1. Handle successful username creation by redirecting
   them to the path `/`
1. Handle unsuccessful registrationm by showing an
   error.
