# Pretty the Header

You celebrate the successful routing with a trip to
«your happy place». But, now that you've returned,
it's time to do some of the boring stuff. The first
thing is to move over the header that is on all of the
application pages.

![timely - header
highlighted](https://tiy-corp-train.github.io/newline-media/learning-angular-with-timely/header-highlighted.png)

You choose not to worry about the links in the tabs,
just yet. You'll take care of that in the routing in
the near future. You just want to get the HTML into
the correct place so, once you log in, you can see a
pretty header rather than the single, ugly button.

You *do* want the "SIGN OUT" button to still work
appropriately, though. There's a `<form>` in there and
stuff that probably doesn't need to be there, anymore.
You decide to clean that up.

## Assignment

<div style="font-size: 1.5em">
  Migrate the header HTML to the
  <code>main-screen</code> component.
</div>

------------------------------------------------------

## Hints in case you get stuck

Can't find the header HTML?

[reveal]
Take a look in the template
<code>entries/index.html</code>.
[/reveal]

Still can't find the header HTML?

[reveal]
It's all the stuff in the <code>&lt;header&gt;</code>
and <code>&lt;nav&gt;</code> tags.
[/reveal]

Still can't find the header HTML?

[reveal]
Here.

<pre class="highlight html"><span class="highlight-copy-clipboard hint--left hint--rounded hint--no-animate" data-hint="Copy Code"></span><code><span class="nt">&lt;header</span> <span class="na">class=</span><span class="s">"app-bar"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;h1</span> <span class="na">class=</span><span class="s">"app-bar-title"</span><span class="nt">&gt;</span>Timely<span class="nt">&lt;/h1&gt;</span>
  <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">"app-bar-spacer"</span><span class="nt">&gt;&lt;/div&gt;</span>
  <span class="nt">&lt;form</span> <span class="na">method=</span><span class="s">"post"</span> <span class="na">action=</span><span class="s">"/logout"</span><span class="nt">&gt;</span>
    <span class="c">&lt;!-- &lt;input type="hidden" value="delete" name="_method"&gt; --&gt;</span>
    <span class="nt">&lt;input</span> <span class="na">type=</span><span class="s">"hidden"</span> <span class="na">name=</span><span class="s">"{{ _csrf.parameterName }}"</span> <span class="na">value=</span><span class="s">"{{ _csrf.token }}"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;button</span> <span class="na">class=</span><span class="s">"button"</span><span class="nt">&gt;</span>Sign out<span class="nt">&lt;/button&gt;</span>
  <span class="nt">&lt;/form&gt;</span>
<span class="nt">&lt;/header&gt;</span>
<span class="nt">&lt;nav</span> <span class="na">class=</span><span class="s">"tabs"</span><span class="nt">&gt;</span>
  <span class="nt">&lt;ul</span> <span class="na">class=</span><span class="s">"tabs-list"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;li</span> <span class="na">class=</span><span class="s">"tabs-item tabs-item-selected"</span><span class="nt">&gt;&lt;a</span> <span class="na">href=</span><span class="s">"/"</span><span class="nt">&gt;</span>Today's Work<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
    <span class="nt">&lt;li</span> <span class="na">class=</span><span class="s">"tabs-item"</span><span class="nt">&gt;&lt;a</span> <span class="na">href=</span><span class="s">"/clients"</span><span class="nt">&gt;</span>Clients<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
    <span class="nt">&lt;li</span> <span class="na">class=</span><span class="s">"tabs-item"</span><span class="nt">&gt;&lt;a</span> <span class="na">href=</span><span class="s">"/report"</span><span class="nt">&gt;</span>Report<span class="nt">&lt;/a&gt;&lt;/li&gt;</span>
  <span class="nt">&lt;/ul&gt;</span>
<span class="nt">&lt;/nav&gt;</span>
</code></pre>
[/reveal]

Don't know where to put the header HTML after you've
found it?

[reveal]
In the
<code>main-screen/main-screen.component.html</code>
file.
[/reveal]

Don't know how to "clean up" the "SIGN OUT" button
HTML?

[reveal]
In the <code>&lt;form&gt;</code> that contains the
"Sign out" button, get rid of the form and everything
but the button. Leave the button.
[/reveal]

Don't know how to hook up the "SIGN OUT" button?

[reveal]
Assuming you got the right HTML and put it in the
<code>main-screen/main-screen.component.html</code>
file, then on the <code>&lt;button&gt;</code> that
has the content "Sign out", you should put an
<code>ng-click</code> that calls the method on the
controller.
[/reveal]
