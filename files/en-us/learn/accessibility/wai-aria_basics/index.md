---
title: WAI-ARIA basics
slug: Learn/Accessibility/WAI-ARIA_basics
tags:
  - ARIA
  - Accessibility
  - Article
  - Beginner
  - CodingScripting
  - Guide
  - HTML
  - JavaScript
  - Learn
  - WAI-ARIA
  - semantics
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Accessibility/CSS_and_JavaScript","Learn/Accessibility/Multimedia", "Learn/Accessibility")}}</div>

<p>Following on from the previous article, sometimes making complex UI controls that involve unsemantic HTML and dynamic JavaScript-updated content can be difficult. WAI-ARIA is a technology that can help with such problems by adding in further semantics that browsers and assistive technologies can recognize and use to let users know what is going on. Here we'll show how to use it at a basic level to improve accessibility.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Basic computer literacy, a basic understanding of HTML, CSS, and JavaScript, an understanding of the <a href="/en-US/docs/Learn/Accessibility">previous articles in the course</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To gain familiarity with WAI-ARIA, and how it can be used to provide useful additional semantics to enhance accessibility where required.</td>
  </tr>
 </tbody>
</table>

<h2 id="What_is_WAI-ARIA">What is WAI-ARIA?</h2>

<p>Let's start by looking at what WAI-ARIA is, and what it can do for us.</p>

<h3 id="A_whole_new_set_of_problems">A whole new set of problems</h3>

<p>As web apps started to get more complex and dynamic, a new set of accessibility features and problems started to appear.</p>

<p>For example, HTML5 introduced a number of semantic elements to define common page features ({{htmlelement("nav")}}, {{htmlelement("footer")}}, etc.) Before these were available, developers would use {{htmlelement("div")}}s with IDs or classes, e.g. <code>&lt;div class="nav"&gt;</code>, but these were problematic, as there was no easy way to easily find a specific page feature such as the main navigation programmatically.</p>

<p>The initial solution was to add one or more hidden links at the top of the page to link to the navigation (or whatever else), for example:</p>

<pre class="brush: html">&lt;a href="#hidden" class="hidden"&gt;Skip to navigation&lt;/a&gt;</pre>

<p>But this is still not very precise, and can only be used when the screenreader is reading from the top of the page.</p>

<p>As another example, apps started to feature complex controls like date pickers for choosing dates, sliders for choosing values, etc. HTML5 provides special input types to render such controls:</p>

<pre class="brush: html">&lt;input type="date"&gt;
&lt;input type="range"&gt;</pre>

<p>These are not well-supported across browsers, and it is also difficult to style them, making them not very useful for integrating with website designs. As a result, developers quite often rely on JavaScript libraries that generate such controls as a series of nested {{htmlelement("div")}}s or table elements with classnames, which are then styled using CSS and controlled using JavaScript.</p>

<p>The problem here is that visually they work, but screenreaders can't make any sense of what they are at all, and their users just get told that they can see a jumble of elements with no semantics to describe what they mean.</p>

<h3 id="Enter_WAI-ARIA">Enter WAI-ARIA</h3>

<p><a href="https://www.w3.org/TR/wai-aria-1.1/">WAI-ARIA</a> (Web Accessibility Initiative - Accessible Rich Internet Applications) is a specification written by the W3C, defining a set of additional HTML attributes that can be applied to elements to provide additional semantics and improve accessibility wherever it is lacking. There are three main features defined in the spec:</p>

<ul>
 <li><strong><a href="/en-US/docs/Web/accessibility/ARIA/roles">Roles</a></strong> — These define what an element is or does. Many of these are so-called landmark roles, which largely duplicate the semantic value of HTML5 structural elements e.g. <code>role="navigation"</code> ({{htmlelement("nav")}}) or <code>role="complementary"</code> ({{htmlelement("aside")}}), but there are also others that describe different pages structures, such as <code>role="banner"</code>, <code>role="search"</code>, <code>role="tabgroup"</code>, <code>role="tab"</code>, etc., which are commonly found in UIs.</li>
 <li><strong>Properties</strong> — These define properties of elements, which can be used to give them extra meaning or semantics. As an example, <code>aria-required="true"</code> specifies that a form input needs to be filled in order to be valid, whereas <code>aria-labelledby="label"</code> allows you to put an ID on an element, then reference it as being the label for anything else on the page, including multiple elements, which is not possible using <code>&lt;label for="input"&gt;</code>. As an example, you could use <code>aria-labelledby</code> to specify that a key description contained in a {{htmlelement("div")}} is the label for multiple table cells, or you could use it as an alternative to image alt text — specify existing information on the page as an image's alt text, rather than having to repeat it inside the <code>alt</code> attribute. You can see an example of this at <a href="/en-US/docs/Learn/Accessibility/HTML?document_saved=true#Text_alternatives">Text alternatives</a>.</li>
 <li><strong>States</strong> — Special properties that define the current conditions of elements, such as <code>aria-disabled="true"</code>, which specifies to a screenreader that a form input is currently disabled. States differ from properties in that properties don't change throughout the lifecycle of an app, whereas states can change, generally programmatically via JavaScript.</li>
</ul>

<p>An important point about WAI-ARIA attributes is that they don't affect anything about the web page, except for the information exposed by the browser's accessibility APIs (where screenreaders get their information from). WAI-ARIA doesn't affect webpage structure, the DOM, etc., although the attributes can be useful for selecting elements by CSS.</p>

<div class="note">
<p><strong>Note:</strong> You can find a useful list of all the ARIA roles and their uses, with links to futher information, in the WAI-ARIA spec — see <a href="https://www.w3.org/TR/wai-aria-1.1/#role_definitions">Definition of Roles</a> — on on this site — see <a href="/en-US/docs/Web/accessibility/ARIA/roles">ARIA roles</a>.</p>

<p>The spec also contains a list of all the properties and states, with links to further information — see <a href="https://www.w3.org/TR/wai-aria-1.1/#state_prop_def">Definitions of States and Properties (all aria-* attributes)</a>.</p>
</div>

<h3 id="Where_is_WAI-ARIA_supported">Where is WAI-ARIA supported?</h3>

<p>This is not an easy question to answer. It is difficult to find a conclusive resource that states what features of WAI-ARIA are supported, and where, because:</p>

<ol>
 <li>There are a lot of features in the WAI-ARIA spec.</li>
 <li>There are many combinations of operating system, browser, and screenreader to consider.</li>
</ol>

<p>This last point is key — To use a screenreader in the first place, your operating system needs to run browsers that have the necessary accessibility APIs in place to expose the information screenreaders need to do their job. Most popular OSes have one or two browsers in place that screenreaders can work with. The Paciello Group has a fairly up-to-date post that provides data for this — see <a href="https://www.paciellogroup.com/blog/2014/10/rough-guide-browsers-operating-systems-and-screen-reader-support-updated/">Rough Guide: browsers, operating systems and screen reader support updated</a>.</p>

<p>Next, you need to worry about whether the browsers in question support ARIA features and expose them via their APIs, but also whether screenreaders recognize that information and present it to their users in a useful way.</p>

<ol>
 <li>Browser support is generally quite good — at the time of writing, <a href="https://caniuse.com/#feat=wai-aria">caniuse.com</a> stated that global browser support for WAI-ARIA was around 88%.</li>
 <li>Screenreader support for ARIA features isn't quite at this level, but the most popular screenreaders are getting there. You can get an idea of support levels by looking at Powermapper's <a href="https://www.powermapper.com/tests/screen-readers/aria/">WAI-ARIA Screen reader compatibility</a> article.</li>
</ol>

<p>In this article, we won't attempt to cover every WAI-ARIA feature, and its exact support details. Instead, we will cover the most critical WAI-ARIA features for you to know about; if we don't mention any support details, you can assume that the feature is well-supported. We will clearly mention any exceptions to this.</p>

<div class="note">
<p><strong>Note:</strong> Some JavaScript libraries support WAI-ARIA, meaning that when they generate UI features like complex form controls, they add ARIA attributes to improve the accessibility of those features. If you are looking for a 3rd party JavaScript solution for rapid UI development, you should definitely consider the accessibility of its UI widgets as an important factor when making your choice. Good examples are jQuery UI (see <a href="https://jqueryui.com/about/#deep-accessibility-support">About jQuery UI: Deep accessibility support</a>), <a href="https://www.sencha.com/products/extjs/">ExtJS</a>, and <a href="https://dojotoolkit.org/reference-guide/1.10/dijit/a11y/statement.html">Dojo/Dijit</a>.</p>
</div>

<h3 id="When_should_you_use_WAI-ARIA">When should you use WAI-ARIA?</h3>

<p>We talked about some of the problems that prompted WAI-ARIA to be created earlier on, but essentially, there are four main areas that WAI-ARIA is useful in:</p>

<ol>
 <li><strong>Signposts/Landmarks</strong>: ARIA's <code><a href="/en-US/docs/Web/accessibility/ARIA/roles">role</a></code>  attribute values can act as landmarks that either replicate the semantics of HTML5 elements (e.g. {{htmlelement("nav")}}), or go beyond HTML5 semantics to provide signposts to different functional areas, e.g <code>search</code>, <code>tabgroup</code>, <code>tab</code>, <code>listbox</code>, etc.</li>
 <li><strong>Dynamic content updates</strong>: Screenreaders tend to have difficulty with reporting constantly changing content; with ARIA we can use <code>aria-live</code> to inform screenreader users when an area of content is updated, e.g. via <a href="/en-US/docs/Web/API/XMLHttpRequest">XMLHttpRequest</a>, or <a href="/en-US/docs/Web/API/Document_Object_Model">DOM APIs</a>.</li>
 <li><strong>Enhancing keyboard accessibility</strong>: There are built-in HTML elements that have native keyboard accessibility; when other elements are used along with JavaScript to simulate similar interactions, keyboard accessibility and screenreader reporting suffers as a result. Where this is unavoidable, WAI-ARIA provides a means to allow other elements to receive focus (using <code>tabindex</code>).</li>
 <li><strong>Accessibility of non-semantic controls</strong>: When a series of nested <code>&lt;div&gt;</code>s along with CSS/JavaScript is used to create a complex UI-feature, or a native control is greatly enhanced/changed via JavaScript, accessibility can suffer — screenreader users will find it difficult to work out what the feature does if there are no semantics or other clues. In these situations, ARIA can help to provide what's missing with a combination of roles like <code>button</code>, <code>listbox</code>, or <code>tabgroup</code>, and properties like <code>aria-required</code> or <code>aria-posinset</code> to provide further clues as to functionality.</li>
</ol>

<p>One thing to remember though — <strong>you should only use WAI-ARIA when you need to!</strong> Ideally, you should <em>always</em> use <a href="/en-US/docs/Learn/Accessibility/HTML">native HTML features</a> to provide the semantics required by screenreaders to tell their users what is going on. Sometimes this isn't possible, either because you have limited control over the code, or because you are creating something complex that doesn't have an easy HTML element to implement it. In such cases, WAI-ARIA can be a valuable accessibility enhancing tool.</p>

<p>But again, only use it when necessary!</p>

<div class="note">
<p><strong>Note:</strong> Also, try to make sure you test your site with a variety of <em>real</em> users — non-disabled people, people using screenreaders, people using keyboard navigation, etc. They will have better insights than you about how well it works.</p>
</div>

<h2 id="Practical_WAI-ARIA_implementations">Practical WAI-ARIA implementations</h2>

<p>In the next section we'll look at the four areas in more detail, along with practical examples. Before you continue, you should get a screenreader testing setup put in place, so you can test some of the examples as you go through.</p>

<p>See our section on <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Accessibility#screenreaders">testing screenreaders</a> for more information.</p>

<h3 id="SignpostsLandmarks">Signposts/Landmarks</h3>

<p>WAI-ARIA adds the <a href="https://www.w3.org/TR/wai-aria-1.1/#role_definitions"><code>role</code> attribute</a> to browsers, which allows you to add extra semantic value to elements on your site wherever they are needed. The first major area in which this is useful is providing information for screenreaders so that their users can find common page elements. Let's look at an example — our <a href="https://github.com/mdn/learning-area/tree/master/accessibility/aria/website-no-roles">website-no-roles</a> example (<a href="https://mdn.github.io/learning-area/accessibility/aria/website-no-roles/">see it live</a>) has the following structure:</p>

<pre class="brush: html">&lt;header&gt;
  &lt;h1&gt;...&lt;/h1&gt;
  &lt;nav&gt;
    &lt;ul&gt;...&lt;/ul&gt;
    &lt;form&gt;
      &lt;!-- search form  --&gt;
    &lt;/form&gt;
  &lt;/nav&gt;
&lt;/header&gt;

&lt;main&gt;
  &lt;article&gt;...&lt;/article&gt;
  &lt;aside&gt;...&lt;/aside&gt;
&lt;/main&gt;

&lt;footer&gt;...&lt;/footer&gt;</pre>

<p>If you try testing the example with a screenreader in a modern browser, you'll already get some useful information. For example, VoiceOver gives you the following:</p>

<ul>
 <li>On the <code>&lt;header&gt;</code> element — "banner, 2 items" (it contains a heading and the <code>&lt;nav&gt;</code>).</li>
 <li>On the <code>&lt;nav&gt;</code> element — "navigation 2 items" (it contains a list and a form).</li>
 <li>On the <code>&lt;main&gt;</code> element — "main 2 items" (it contains an article and an aside).</li>
 <li>On the <code>&lt;aside&gt;</code> element — "complementary 2 items" (it contains a heading and a list).</li>
 <li>On the search form input — "Search query, insertion at beginning of text".</li>
 <li>On the <code>&lt;footer&gt;</code> element — "footer 1 item".</li>
</ul>

<p>If you go to VoiceOver's landmarks menu (accessed using VoiceOver key + U and then using the cursor keys to cycle through the menu choices), you'll see that most of the elements are nicely listed so they can be accessed quickly.</p>

<p><img alt="" src="landmarks-list.png"></p>

<p>However, we could do better here. the search form is a really important landmark that people will want to find, but it is not listed in the landmarks menu or treated like a notable landmark, beyond the actual input being called out as a search input (<code>&lt;input type="search"&gt;</code>). In addition, some older browsers (most notably IE8) don't recognize the semantics of the HTML5 elements.</p>

<p>Let's improve it by the use of some ARIA features. First, we'll add some <code><a href="/en-US/docs/Web/accessibility/ARIA/roles">role</a></code>  attributes to our HTML structure. You can try taking a copy of our original files (see <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/website-no-roles/index.html">index.html</a> and <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/website-no-roles/style.css">style.css</a>), or navigating to our <a href="https://github.com/mdn/learning-area/tree/master/accessibility/aria/website-aria-roles">website-aria-roles</a> example (<a href="https://mdn.github.io/learning-area/accessibility/aria/website-aria-roles/">see it live</a>), which has a structure like this:</p>

<pre class="brush: html">&lt;header&gt;
  &lt;h1&gt;...&lt;/h1&gt;
  &lt;nav role="navigation"&gt;
    &lt;ul&gt;...&lt;/ul&gt;
    &lt;form role="search"&gt;
      &lt;!-- search form  --&gt;
    &lt;/form&gt;
  &lt;/nav&gt;
&lt;/header&gt;

&lt;main&gt;
  &lt;article role="article"&gt;...&lt;/article&gt;
  &lt;aside role="complementary"&gt;...&lt;/aside&gt;
&lt;/main&gt;

&lt;footer&gt;...&lt;/footer&gt;</pre>

<p>We've also given you a bonus feature in this example — the {{htmlelement("input")}} element has been given the attribute <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-label">aria-label</a></code>, which gives it a descriptive label to be read out by a screenreader, even though we haven't included a {{htmlelement("label")}} element. In cases like these, this is very useful — a search form like this one is a very common, easily recognized feature, and adding a visual label would spoil the page design.</p>

<pre class="brush: html">&lt;input type="search" name="q" placeholder="Search query" aria-label="Search through site content"&gt;</pre>

<p>Now if we use VoiceOver to look at this example, we get some improvements:</p>

<ul>
 <li>The search form is called out as a separate item, both when browsing through the page, and in the Landmarks menu.</li>
 <li>The label text contained in the <code>aria-label</code> attribute is read out when the form input is highlighted.</li>
</ul>

<p>Beyond this, the site is more likely to be accessible to users of older browsers such as IE8; it is worth including ARIA roles for that purpose. And if for some reason your site is built using just <code>&lt;div&gt;</code>s, you should definitely include the ARIA roles to provide these much needed semantics!</p>

<p>The improved semantics of the search form have shown what is made possible when ARIA goes beyond the semantics available in HTML5. You'll see a lot more about these semantics and the power of ARIA properties/attributes below, especially in the {{anch("Accessibility of non-semantic controls")}} section. For now though, let's look at how ARIA can help with dynamic content updates.</p>

<h3 id="Dynamic_content_updates">Dynamic content updates</h3>

<p>Content loaded into the DOM can be easily accessed using a screenreader, from textual content to alternative text attached to images. Traditional static websites with largely text content are therefore easy to make accessible for people with visual impairments.</p>

<p>The problem is that modern web apps are often not just static text — they tend to have a lot of dynamically updating content, i.e. content that updates without the entire page reloading via a mechanism like <a href="/en-US/docs/Web/API/XMLHttpRequest">XMLHttpRequest</a>, <a href="/en-US/docs/Web/API/Fetch_API">Fetch</a>, or <a href="/en-US/docs/Web/API/Document_Object_Model">DOM APIs</a>. These are sometimes referred to as <strong>live regions</strong>.</p>

<p>Let's look at a quick example — see <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/aria-no-live.html">aria-no-live.html</a> (also <a href="https://mdn.github.io/learning-area/accessibility/aria/aria-no-live.html">see it running live</a>). In this example we have a simple random quote box:</p>

<pre class="brush: html">&lt;section&gt;
  &lt;h1&gt;Random quote&lt;/h1&gt;
  &lt;blockquote&gt;
    &lt;p&gt;&lt;/p&gt;
  &lt;/blockquote&gt;
&lt;/section&gt;</pre>

<p>Our JavaScript loads a JSON file via <code><a href="/en-US/docs/Web/API/XMLHttpRequest">XMLHttpRequest</a></code> containing a series of random quotes and their authors. Once that is done, we start up a <code><a href="/en-US/docs/Web/API/setInterval">setInterval()</a></code> loop that loads a new random quote into the quote box every 10 seconds:</p>

<pre class="brush: js">let intervalID = window.setInterval(showQuote, 10000);</pre>

<p>This works OK, but it is not good for accessibility — the content update is not detected by screenreaders, so their users would not know what is going on. This is a fairly trivial example, but just imagine if you were creating a complex UI with lots of constantly updating content, like a chat room, or a strategy game UI, or a live updating shopping cart display — it would be impossible to use the app in any effective way without some kind of way of alerting the user to the updates.</p>

<p>WAI-ARIA fortunately provides a useful mechanism to provide these alerts — the <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-live">aria-live</a></code> property. Applying this to an element causes screenreaders to read out the content that is updated. How urgently the content is read out depends on the attribute value:</p>

<ul>
 <li><code>off:</code> The default. Updates should not be announced.</li>
 <li><code>polite</code>: Updates should be announced only if the user is idle.</li>
 <li><code>assertive</code>: Updates should be announced to the user as soon as possible.</li>
</ul>

<p>We'd like you to take a copy of <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/aria-no-live.html">aria-no-live.html</a> and <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/quotes.json">quotes.json</a>, and update your <code>&lt;section&gt;</code> tag as follows:</p>

<pre class="brush: html">&lt;section aria-live="assertive"&gt;</pre>

<p>This will cause a screenreader to read out the content as it is updated.</p>

<div class="note">
<p><strong>Note:</strong> Most browsers will throw a security exception if you try to do an <code>XMLHttpRequest</code> call from a <code>file://</code> URL, e.g. if you just load the file by loading it directly into the browser (via double clicking, etc.). To get it to run, you will need to upload it to a web server, for example <a href="/en-US/docs/Learn/Common_questions/Using_Github_pages">using GitHub</a>, or a local web server like <a href="https://www.pythonforbeginners.com/modules-in-python/how-to-use-simplehttpserver/">Python's SimpleHTTPServer</a>.</p>
</div>

<p>There is an additional consideration here — only the bit of text that updates is read out. It might be nice if we always read out the heading too, so the user can remember what is being read out. To do this, we can add the <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-atomic">aria-atomic</a></code> property to the section. Update your <code>&lt;section&gt;</code> tag again, like so:</p>

<pre class="brush: html">&lt;section aria-live="assertive" aria-atomic="true"&gt;</pre>

<p>The <code>aria-atomic="true"</code> attribute tells screenreaders to read out the entire element contents as one atomic unit, not just the bits that were updated.</p>

<div class="note">
<p><strong>Note:</strong> You can see the finished example at <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/aria-live.html">aria-live.html</a> (<a href="https://mdn.github.io/learning-area/accessibility/aria/aria-live.html">see it running live</a>).</p>
</div>

<div class="note">
<p><strong>Note:</strong> The <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-relevant">aria-relevant</a></code> property is also quite useful for controlling what gets read out when a live region is updated. You can for example only get content additions or removals read out.</p>
</div>

<h3 id="Enhancing_keyboard_accessibility">Enhancing keyboard accessibility</h3>

<p>As discussed in a few other places in the module, one of the key strengths of HTML with respect to accessibility is the built-in keyboard accessibility of features such as buttons, form controls, and links. Generally, you are able to use the tab key to move between controls, the Enter/Return key to select or activate controls, and occasionally other controls as needed (for example the up and down cursor to move between options in a <code>&lt;select&gt;</code> box).</p>

<p>However, sometimes you will end up having to write code that either uses non-semantic elements as buttons (or other types of control), or uses focusable controls for not quite the right purpose. You might be trying to fix some bad code you've inherited, or you might be building some kind of complex widget that requires it.</p>

<p>In terms of making non-focusable code focusable, WAI-ARIA extends the <code>tabindex</code> attribute with some new values:</p>

<ul>
 <li><code>tabindex="0"</code> — as indicated above, this value allows elements that are not normally tabbable to become tabbable. This is the most useful value of <code>tabindex</code>.</li>
 <li><code>tabindex="-1"</code> — this allows not normally tabbable elements to receive focus programmatically, e.g. via JavaScript, or as the target of links. </li>
</ul>

<p>We discussed this in more detail and showed a typical implementation back in our HTML accessibility article — see <a href="/en-US/docs/Learn/Accessibility/HTML#building_keyboard_accessibility_back_in">Building keyboard accessibility back in</a>.</p>

<h3 id="Accessibility_of_non-semantic_controls">Accessibility of non-semantic controls</h3>

<p>This follows on from the previous section — when a series of nested <code>&lt;div&gt;</code>s along with CSS/JavaScript is used to create a complex UI-feature, or a native control is greatly enhanced/changed via JavaScript, not only can keyboard accessibility suffer, but screenreader users will find it difficult to work out what the feature does if there are no semantics or other clues. In such situations, ARIA can help to provide those missing semantics.</p>

<h4 id="Form_validation_and_error_alerts">Form validation and error alerts</h4>

<p>First of all, let's revisit the form example we first looked at in our CSS and JavaScript accessibility article (read <a href="/en-US/docs/Learn/Accessibility/CSS_and_JavaScript#keeping_it_unobtrusive">Keeping it unobtrusive</a> for a full recap). At the end of this section we showed that we have included some ARIA attributes on the error message box that displays any validation errors when you try to submit the form:</p>

<pre class="brush: html">&lt;div class="errors" role="alert" aria-relevant="all"&gt;
  &lt;ul&gt;
  &lt;/ul&gt;
&lt;/div&gt;</pre>

<ul>
 <li><code><a href="/en-US/docs/Web/accessibility/ARIA/roles/alert_role">role="alert"</a></code> automatically turns the element it is applied to into a live region, so changes to it are read out; it also semantically identifies it as an alert message (important time/context sensitive information), and represents a better, more accessible way of delivering an alert to a user (modal dialogs like <code><a href="/en-US/docs/Web/API/Window/alert">alert()</a></code> calls have a number of accessibility problems; see <a href="https://webaim.org/techniques/javascript/other#popups">Popup Windows</a> by WebAIM).</li>
 <li>An <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-relevant">aria-relevant</a></code> value of <code>all</code> instructs the screenreader to read out the contents of the error list when any changes are made to it — i.e. when errors are added or removed. This is useful because the user will want to know what errors are left, not just what has been added or removed from the list.</li>
</ul>

<p>We could go further with our ARIA usage, and provide some more validation help. How about indicating whether fields are required in the first place, and what range the age should be?</p>

<ol>
 <li>At this point, take a copy of our <a href="https://github.com/mdn/learning-area/blob/master/accessibility/css/form-validation.html">form-validation.html</a> and <a href="https://github.com/mdn/learning-area/blob/master/accessibility/css/validation.js">validation.js</a> files, and save them in a local directory.</li>
 <li>Open them both in a text editor and have a look at how the code works.</li>
 <li>First of all, add a paragraph just above the opening <code>&lt;form&gt;</code> tag, like the one below, and mark both the form <code>&lt;label&gt;</code>s with an asterisk. This is normally how we mark required fields for sighted users.
  <pre class="brush: html">&lt;p&gt;Fields marked with an asterisk (*) are required.&lt;/p&gt;</pre>
 </li>
 <li>This makes visual sense, but it isn't as easy to understand for screenreader users. Fortunately, WAI-ARIA provides the <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-required">aria-required</a></code> attribute to give screenreaders hints that they should tell users that form inputs need to be filled in. Update the <code>&lt;input&gt;</code> elements like so:
  <pre class="brush: html">&lt;input type="text" name="name" id="name" aria-required="true"&gt;

&lt;input type="number" name="age" id="age" aria-required="true"&gt;</pre>
 </li>
 <li>If you save the example now and test it with a screenreader, you should hear something like "Enter your name star, required, edit text".</li>
 <li>It might also be useful if we give screenreader users and sighted users an idea of what the age value should be. This is often presented as a tooltip, or placeholder inside the form field perhaps. WAI-ARIA does include <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-valuemin">aria-valuemin</a></code> and <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-valuemax">aria-valuemax</a></code> properties to specify min and max values, but these currently don't seem very well supported; a better supported feature is the HTML5 <code>placeholder</code> attribute, which can contain a message that is shown in the input when no value is entered, and is read out by a number of screenreaders. Update your number input like this:
  <pre class="brush: html">&lt;input type="number" name="age" id="age" placeholder="Enter 1 to 150" aria-required="true"&gt;</pre>
 </li>
</ol>

<div class="note">
<p><strong>Note:</strong> You can see the finished example live at <a href="https://mdn.github.io/learning-area/accessibility/aria/form-validation-updated.html">form-validation-updated.html</a>.</p>
</div>

<p>WAI-ARIA also enables some advanced form labelling techniques, beyond the classic {{htmlelement("label")}} element. We already talked about using the <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-label">aria-label</a></code> property to provide a label where we don't want the label to be visible to sighted users (see the {{anch("Signposts/Landmarks")}} section, above). There are some other labelling techniques that use other properties such as <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-labelledby">aria-labelledby</a></code> if you want to designate a non-<code>&lt;label&gt;</code> element as a label or label multiple form inputs with the same label, and <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-describedby">aria-describedby</a></code>, if you want to associate other information with a form input and have it read out as well. See <a href="https://webaim.org/techniques/forms/advanced">WebAIM's Advanced Form Labeling article</a> for more details.</p>

<p>There are many other useful properties and states too, for indicating the status of form elements. For example, <code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-disabled">aria-disabled</a>="true"</code> can be used to indicate that a form field is disabled. Many browsers will just skip past disabled form fields, and they won't even be read out by screenreaders, but in some cases they will be perceived, so it is a good idea to include this attribute to let the screenreader know that a disabled input is in fact disabled.</p>

<p>If the disabled state of an input is likely to change, then it is also a good idea to indicate when it happens, and what the result is. For example, in our <a href="https://mdn.github.io/learning-area/accessibility/aria/form-validation-checkbox-disabled.html">form-validation-checkbox-disabled.html</a> demo there is a checkbox that when checked, enables another form input to allow further information be entered. We've set up a hidden live region:</p>

<pre class="brush: html">&lt;p class="hidden-alert" aria-live="assertive"&gt;&lt;/p&gt;</pre>

<p>which is hidden from view using absolute positioning. When this is checked/unchecked, we update the text inside the hidden live region to tell screenreader users what the result of checking this checkbox is, as well as updating the <code>aria-disabled</code> state, and some visual indicators too:</p>

<pre class="brush: js">function toggleMusician(bool) {
  let instruItem = formItems[formItems.length-1];
  if(bool) {
    instruItem.input.disabled = false;
    instruItem.label.style.color = '#000';
    instruItem.input.setAttribute('aria-disabled', 'false');
    hiddenAlert.textContent = 'Instruments played field now enabled; use it to tell us what you play.';
  } else {
    instruItem.input.disabled = true;
    instruItem.label.style.color = '#999';
    instruItem.input.setAttribute('aria-disabled', 'true');
    instruItem.input.removeAttribute('aria-label');
    hiddenAlert.textContent = 'Instruments played field now disabled.';
  }
}</pre>

<h4 id="Describing_non-semantic_buttons_as_buttons">Describing non-semantic buttons as buttons</h4>

<p>A few times in this course already, we've mentioned the native accessibility of (and the accessibility issues behind using other elements to fake) buttons, links, or form elements (see <a href="/en-US/docs/Learn/Accessibility/HTML#ui_controls">UI controls</a> in the HTML accessibility article, and {{anch("Enhancing keyboard accessibility")}}, above). Basically, you can add keyboard accessibility back in without too much trouble in many cases, using <code>tabindex</code> and a bit of JavaScript.</p>

<p>But what about screenreaders? They still won't see the elements as buttons. If we test our <a href="https://mdn.github.io/learning-area/tools-testing/cross-browser-testing/accessibility/fake-div-buttons.html">fake-div-buttons.html</a> example in a screenreader, our fake buttons will be reported using phrases like "Click me!, group", which is obviously confusing.</p>

<p>We can fix this using a WAI-ARIA role. Make a local copy of <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/accessibility/fake-div-buttons.html">fake-div-buttons.html</a>, and add <code><a href="/en-US/docs/Web/accessibility/ARIA/roles/button_role">role="button"</a></code> to each button <code>&lt;div&gt;</code>, for example:</p>

<pre>&lt;div data-message="This is from the first button" tabindex="0" role="button"&gt;Click me!&lt;/div&gt;</pre>

<p>Now when you try this using a screenreader, you'll have buttons be reported using phrases like "Click me!, button" — much better.</p>

<div class="note">
<p><strong>Note:</strong> Don't forget however that using the correct semantic element where possible is always better. If you want to create a button, and can use a {{htmlelement("button")}} element, you should use a {{htmlelement("button")}} element!</p>
</div>

<h4 id="Guiding_users_through_complex_widgets">Guiding users through complex widgets</h4>

<p>There are a whole host of other <a href="https://www.w3.org/TR/wai-aria-1.1/#role_definitions">roles</a> that can identify non-semantic element structures as common UI features that go beyond what's available in standard HTML, for example <code><a href="https://www.w3.org/TR/wai-aria-1.1/#combobox">combobox</a></code>, <code><a href="https://www.w3.org/TR/wai-aria-1.1/#slider">slider</a></code>, <code><a href="https://www.w3.org/TR/wai-aria-1.1/#tabpanel">tabpanel</a></code>, <code><a href="https://www.w3.org/TR/wai-aria-1.1/#tree">tree</a></code>. You can see a number of useful examples in the <a href="https://dequeuniversity.com/library/">Deque university code library</a>, to give you an idea of how such controls can be made accessible.</p>

<p>Let's go through an example of our own. We'll return to our simple absolutely-positioned tabbed interface (see <a href="/en-US/docs/Learn/Accessibility/CSS_and_JavaScript#hiding_things">Hiding things</a> in our CSS and JavaScript accessibility article), which you can find at <a class="external external-icon" href="https://mdn.github.io/learning-area/css/css-layout/practical-positioning-examples/info-box.html">Tabbed info box example</a> (see <a class="external external-icon" href="https://github.com/mdn/learning-area/blob/master/css/css-layout/practical-positioning-examples/info-box.html">source code</a>).</p>

<p>This example as-is works fine in terms of keyboard accessibility — you can happily tab between the different tabs and select them to show the tab contents. It is also fairly accessible too — you can scroll through the content and use the headings to navigate, even if you can't see what is happening on screen. It is however not that obvious what the content is — a screenreader currently reports the content as a list of links, and some content with three headings. It doesn't give you any idea of what the relationship is between the content. Giving the user more clues as to the structure of the content is always good.</p>

<p>To improve things, we've created a new version of the example called <a href="https://github.com/mdn/learning-area/blob/master/accessibility/aria/aria-tabbed-info-box.html">aria-tabbed-info-box.html</a> (<a href="https://mdn.github.io/learning-area/accessibility/aria/aria-tabbed-info-box.html">see it running live</a>). We've updated the structure of the tabbed interface like so:</p>

<pre class="brush: html">&lt;ul role="tablist"&gt;
  &lt;li class="active" role="tab" aria-selected="true" aria-setsize="3" aria-posinset="1" tabindex="0"&gt;Tab 1&lt;/li&gt;
  &lt;li role="tab" aria-selected="false" aria-setsize="3" aria-posinset="2" tabindex="0"&gt;Tab 2&lt;/li&gt;
  &lt;li role="tab" aria-selected="false" aria-setsize="3" aria-posinset="3" tabindex="0"&gt;Tab 3&lt;/li&gt;
&lt;/ul&gt;
&lt;div class="panels"&gt;
  &lt;article class="active-panel" role="tabpanel" aria-hidden="false"&gt;
    ...
  &lt;/article&gt;
  &lt;article role="tabpanel" aria-hidden="true"&gt;
    ...
  &lt;/article&gt;
  &lt;article role="tabpanel" aria-hidden="true"&gt;
    ...
  &lt;/article&gt;
&lt;/div&gt;</pre>

<div class="note">
<p><strong>Note:</strong> The most striking change here is that we've removed the links that were originally present in the example, and just used the list items as the tabs — this was done because it makes things less confusing for screenreader users (the links don't really take you anywhere; they just change the view), and it allows the setsize/position in set features to work better — when these were put on the links, the browser kept reporting "1 of 1" all the time, not "1 of 3", "2 of 3", etc.</p>
</div>

<p>The new features are as follows:</p>

<ul>
 <li>New roles — <code><a href="https://www.w3.org/TR/wai-aria-1.1/#tablist">tablist</a></code>, <code><a href="https://www.w3.org/TR/wai-aria-1.1/#tab">tab</a></code>, <code><a href="https://www.w3.org/TR/wai-aria-1.1/#tabpanel">tabpanel</a></code> — these identify the important areas of the tabbed interface — the container for the tabs, the tabs themselves, and the corresponding tabpanels.</li>
 <li><code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-selected">aria-selected</a></code> — Defines which tab is currently selected. As different tabs are selected by the user, the value of this attribute on the different tabs is updated via JavaScript.</li>
 <li><code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-hidden">aria-hidden</a></code> — Hides an element from being read out by a screenreader. As different tabs are selected by the user, the value of this attribute on the different tabs is updated via JavaScript.</li>
 <li><code>tabindex="0"</code> — As we've removed the links, we need to give the list items this attribute to provide it with keyboard focus.</li>
 <li><code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-setsize">aria-setsize</a></code> — This property allows you to specify to screenreaders that an element is part of a series, and how many items the series has.</li>
 <li><code><a href="https://www.w3.org/TR/wai-aria-1.1/#aria-posinset">aria-posinset</a></code> — This property allows you to specify what position in a series an element is in. Along with <code>aria-setsize</code>, it provides a screenreader with enough information to tell you that you are currently on item "1 of 3", etc. In many cases, browsers should be able to infer this information from the element hierarchy, but it certainly helps to provide more clues.</li>
</ul>

<p>In our tests, this new structure did serve to improve things overall. The tabs are now recognized as tabs (e.g. "tab" is spoken by the screenreader), the selected tab is indicated by "selected" being read out with the tab name, and the screenreader also tells you which tab number you are currently on. In addition, because of the <code>aria-hidden</code> settings (only the non-hidden tab ever has <code>aria-hidden="false"</code> set), the non-hidden content is the only one you can navigate down to, meaning the selected content is easier to find.</p>

<div class="note">
<p><strong>Note:</strong> If there is anything you explicitly don't want screen readers to read out, you can give them the <code>aria-hidden="true"</code>  attribute.</p>
</div>

<h2 id="Test_your_skills!">Test your skills!</h2>

<p>You've reached the end of this article, but can you remember the most important information? You can find some further tests to verify that you've retained this information before you move on — see <a href="/en-US/docs/Learn/Accessibility/WAI-ARIA_basics/Test_your_skills:_WAI-ARIA">Test your skills: WAI-ARIA</a>.</p>

<h2 id="Summary">Summary</h2>

<p>This article has by no means covered all that's available in WAI-ARIA, but it should have given you enough information to understand how to use it, and know some of the most common patterns you will encounter that require it.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://www.w3.org/TR/wai-aria-1.1/#role_definitions">Definition of Roles</a> — ARIA roles reference.</li>
 <li><a href="https://www.w3.org/TR/wai-aria-1.1/#state_prop_def">Definitions of States and Properties (all aria-* attributes)</a> — properties and states reference.</li>
 <li><a href="https://dequeuniversity.com/library/">Deque university code library</a> — a library of really useful practical examples showing complex UI controls made accessible using WAI-ARIA features.</li>
 <li><a href="https://w3c.github.io/aria-practices/">WAI-ARIA Authoring Practices</a> — very detailed design patterns from the W3C, explaining how to implement different types of complex UI control whilst making them accessible using WAI-ARIA features.</li>
 <li><a href="https://www.w3.org/TR/html-aria/">ARIA in HTML</a> — A W3C spec that defines, for each HTML feature, what accessibility (ARIA) semantics that feature implicitly has set on it by the browser, and what WAI-ARIA features you may set on it if extra semantics are required.</li>
</ul>

<p>{{PreviousMenuNext("Learn/Accessibility/CSS_and_JavaScript","Learn/Accessibility/Multimedia", "Learn/Accessibility")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Accessibility/What_is_accessibility">What is accessibility?</a></li>
 <li><a href="/en-US/docs/Learn/Accessibility/HTML">HTML: A good basis for accessibility</a></li>
 <li><a href="/en-US/docs/Learn/Accessibility/CSS_and_JavaScript">CSS and JavaScript accessibility best practices</a></li>
 <li><a href="/en-US/docs/Learn/Accessibility/WAI-ARIA_basics">WAI-ARIA basics</a></li>
 <li><a href="/en-US/docs/Learn/Accessibility/Multimedia">Accessible multimedia</a></li>
 <li><a href="/en-US/docs/Learn/Accessibility/Mobile">Mobile accessibility</a></li>
 <li><a href="/en-US/docs/Learn/Accessibility/Accessibility_troubleshooting">Accessibility troubleshooting</a></li>
</ul>