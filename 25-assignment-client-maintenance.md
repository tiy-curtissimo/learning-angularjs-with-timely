# The Client-Management List-Detail Page

Here's the stretch, the last and final page. You
know there's some difficulty in this one, but you're
not worried because you know you've got all the smarts
to get it done.

You pull up Michaela's app one more time and notice
that the client management stuff has three different
URLs:

* `/clients` - Lists the clients by themselves
* `/clients/new` - Shows the list of clients and the
  new client form
* `/clients/«clientId»` - Shows the list of clients
  and the client to edit

You realize that `/clients/new` and
`/clients/«clientId»` are just nested states of the
state that represents `/clients`. You know how to make
nested states! The only thing you need to figure out
is that `clientId` parameter thing and how to use it
with `ui-router` `resolve`s, but you know you can find
it on the
[ui-router](https://ui-router.github.io) site. You
keep a browser open to the [ui-router
API](https://ui-router.github.io/ng1/docs/latest/) to
keep all the weirdness close to you. The documentation
is kind of hard to read; you know that this is just a
pill you'll have to swallow. So you don't worry to much
about that. You know about component bindings from the
dropdown component. You're covered.

Otherwise, it's just data-binding and some `ui-sref`s
and some `ng-click` handling.

You know you can do this. You work slowly and
methodically, knocking out each piece of functionality
one-by-one, until the entire client management portion
of the application Just Works!

During the work, you refer back to the documentation
that you've come across and the code that you've
written. Luckily, you had it handy.

* [ui-router state parameters](https://ui-router.github.io/ng1/tutorial/hellosolarsystem#state-parameters)
* [nesting states in ui-router](https://ui-router.github.io/ng1/tutorial/hellogalaxy#nesting-states)

The rest you know, you've come across it many times,
like handling clicks and form submissions. You know
this may hurt a little as you do it, but you're so
close to the finish line, it'd make little sense to
give up, now...

You take a deep breath.

You lightly rest your fingertips on your keyboard.

Your eyes narrow a little.

You get to typing.
