---
title: 'Django Tutorial Part 6: Generic list and detail views'
slug: Learn/Server-side/Django/Generic_views
tags:
  - Beginner
  - Learn
  - Tutorial
  - django
  - django templates
  - django views
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Django/Home_page", "Learn/Server-side/Django/Sessions", "Learn/Server-side/Django")}}</div>

<p>This tutorial extends our <a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">LocalLibrary</a> website, adding list and detail pages for books and authors. Here we'll learn about generic class-based views, and show how they can reduce the amount of code you have to write for common use cases. We'll also go into URL handling in greater detail, showing how to perform basic pattern matching.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Complete all previous tutorial topics, including <a href="/en-US/docs/Learn/Server-side/Django/Home_page">Django Tutorial Part 5: Creating our home page</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand where and how to use generic class-based views, and how to extract patterns from URLs and pass the information to views.</td>
  </tr>
 </tbody>
</table>

<h2 id="Overview">Overview</h2>

<p>In this tutorial we're going to complete the first version of the <a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">LocalLibrary</a> website by adding list and detail pages for books and authors (or to be more precise, we'll show you how to implement the book pages, and get you to create the author pages yourself!)</p>

<p>The process is similar to creating the index page, which we showed in the previous tutorial. We'll still need to create URL maps, views, and templates. The main difference is that for the detail pages, we'll have the additional challenge of extracting information from patterns in the URL and passing it to the view. For these pages, we're going to demonstrate a completely different type of view: generic class-based list and detail views. These can significantly reduce the amount of view code needed, making them easier to write and maintain.</p>

<p>The final part of the tutorial will demonstrate how to paginate your data when using generic class-based list views.</p>

<h2 id="Book_list_page">Book list page</h2>

<p>The book list page will display a list of all the available book records in the page, accessed using the URL: <code>catalog/books/</code>. The page will display a title and author for each record, with the title being a hyperlink to the associated book detail page. The page will have the same structure and navigation as all other pages in the site, and we can, therefore, extend the base template (<strong>base_generic.html</strong>) we created in the previous tutorial.</p>

<h3 id="URL_mapping">URL mapping</h3>

<p>Open <strong>/catalog/urls.py</strong> and copy in the line setting the path for <code>'books/'</code>, as shown below.
Just as for the index page, this <code>path()</code> function defines a pattern to match against the URL (<strong>'books/'</strong>), a view function that will be called if the URL matches (<code>views.BookListView.as_view()</code>), and a name for this particular mapping.</p>

<pre class="brush: python">urlpatterns = [
    path('', views.index, name='index'),
    path('books/', views.BookListView.as_view(), name='books'),
]</pre>

<p>As discussed in the previous tutorial the URL must already have matched <code>/catalog</code>, so the view will actually be called for the URL: <code>/catalog/books/</code>.</p>

<p>The view function has a different format than before — that's because this view will actually be implemented as a class. We will be inheriting from an existing generic view function that already does most of what we want this view function to do, rather than writing our own from scratch.</p>

<p>For Django class-based views we access an appropriate view function by calling the class method <code>as_view()</code>. This does all the work of creating an instance of the class, and making sure that the right handler methods are called for incoming HTTP requests.</p>

<h3 id="View_class-based">View (class-based)</h3>

<p>We could quite easily write the book list view as a regular function (just like our previous index view), which would query the database for all books, and then call <code>render()</code> to pass the list to a specified template. Instead, however, we're going to use a class-based generic list view (<code>ListView</code>) — a class that inherits from an existing view. Because the generic view already implements most of the functionality we need and follows Django best-practice, we will be able to create a more robust list view with less code, less repetition, and ultimately less maintenance.</p>

<p>Open <strong>catalog/views.py</strong>, and copy the following code into the bottom of the file:</p>

<pre class="brush: python">from django.views import generic

class BookListView(generic.ListView):
    model = Book</pre>

<p>That's it! The generic view will query the database to get all records for the specified model (<code>Book</code>) then render a template located at <strong>/locallibrary/catalog/templates/catalog/book_list.html</strong> (which we will create below). Within the template you can access the list of books with the template variable named <code>object_list</code> OR <code>book_list</code> (i.e. generically "<code><em>the_model_name</em>_list</code>").</p>

<div class="notecard note">
  <p><strong>Note:</strong> This awkward path for the template location isn't a misprint — the generic views look for templates in <code>/<em>application_name</em>/<em>the_model_name</em>_list.html</code> (<code>catalog/book_list.html</code> in this case) inside the application's <code>/<em>application_name</em>/templates/</code> directory (<code>/catalog/templates/)</code>.</p>
</div>

<p>You can add attributes to change the default behavior above. For example, you can specify another template file if you need to have multiple views that use this same model, or you might want to use a different template variable name if <code>book_list</code> is not intuitive for your particular template use-case. Possibly the most useful variation is to change/filter the subset of results that are returned — so instead of listing all books you might list top 5 books that were read by other users.</p>

<pre class="brush: python">class BookListView(generic.ListView):
    model = Book
    context_object_name = 'my_book_list'   # your own name for the list as a template variable
    queryset = Book.objects.filter(title__icontains='war')[:5] # Get 5 books containing the title war
    template_name = 'books/my_arbitrary_template_name_list.html'  # Specify your own template name/location</pre>

<h4 id="Overriding_methods_in_class-based_views">Overriding methods in class-based views</h4>

<p>While we don't need to do so here, you can also override some of the class methods.</p>

<p>For example, we can override the <code>get_queryset()</code> method to change the list of records returned. This is more flexible than just setting the <code>queryset</code> attribute as we did in the preceding code fragment (though there is no real benefit in this case):</p>

<pre class="brush: python">class BookListView(generic.ListView):
    model = Book

    def get_queryset(self):
        return Book.objects.filter(title__icontains='war')[:5] # Get 5 books containing the title war
</pre>

<p>We might also override <code>get_context_data()</code> in order to pass additional context variables to the template (e.g. the list of books is passed by default). The fragment below shows how to add a variable named "<code>some_data</code>" to the context (it would then be available as a template variable).</p>

<pre class="brush: python">class BookListView(generic.ListView):
    model = Book

    def get_context_data(self, **kwargs):
        # Call the base implementation first to get the context
        context = super(BookListView, self).get_context_data(**kwargs)
        # Create any data and add it to the context
        context['some_data'] = 'This is just some data'
        return context</pre>

<p>When doing this it is important to follow the pattern used above:</p>

<ul>
 <li>First get the existing context from our superclass.</li>
 <li>Then add your new context information.</li>
 <li>Then return the new (updated) context.</li>
</ul>

<div class="notecard note">
  <p><strong>Note:</strong> Check out <a href="https://docs.djangoproject.com/en/3.1/topics/class-based-views/generic-display/">Built-in class-based generic views</a> (Django docs) for many more examples of what you can do.</p>
</div>

<h3 id="Creating_the_List_View_template">Creating the List View template</h3>

<p>Create the HTML file <strong>/locallibrary/catalog/templates/catalog/book_list.html</strong> and copy in the text below. As discussed above, this is the default template file expected by the generic class-based list view (for a model named <code>Book</code> in an application named <code>catalog</code>).</p>

<p>Templates for generic views are just like any other templates (although of course the context/information passed to the template may differ).
As with our <em>index</em> template, we extend our base template in the first line and then replace the block named <code>content</code>.</p>

<pre class="brush: html">{% extends "base_generic.html" %}

{% block content %}
  &lt;h1&gt;Book List&lt;/h1&gt;
  {% if book_list %}
  &lt;ul&gt;
    {% for book in book_list %}
      &lt;li&gt;
        &lt;a href="\{{ book.get_absolute_url }}"&gt;\{{ book.title }}&lt;/a&gt; (\{{book.author}})
      &lt;/li&gt;
    {% endfor %}
  &lt;/ul&gt;
  {% else %}
    &lt;p&gt;There are no books in the library.&lt;/p&gt;
  {% endif %}
{% endblock %}</pre>

<p>The view passes the context (list of books) by default as <code>object_list</code> and <code>book_list</code> aliases; either will work.</p>

<h4 id="Conditional_execution">Conditional execution</h4>

<p>We use the <code><a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins/#if">if</a></code>, <code>else</code>, and <code>endif</code> template tags to check whether the <code>book_list</code> has been defined and is not empty.
If <code>book_list</code> is empty, then the <code>else</code> clause displays text explaining that there are no books to list.
If <code>book_list</code> is not empty, then we iterate through the list of books.</p>

<pre class="brush: html">{% if book_list %}
  &lt;!-- code here to list the books --&gt;
{% else %}
  &lt;p&gt;There are no books in the library.&lt;/p&gt;
{% endif %}
</pre>

<p>The condition above only checks for one case, but you can test on additional conditions using the <code>elif</code> template tag (e.g. <code>{% elif var2 %}</code>). For more information about conditional operators see: <a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins/#if">if</a>, <a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins/#ifequal-and-ifnotequal">ifequal/ifnotequal</a>, and <a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins/#ifchanged">ifchanged</a> in <a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins">Built-in template tags and filters</a> (Django Docs).</p>

<h4 id="For_loops">For loops</h4>

<p>The template uses the <a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins/#for">for</a> and <code>endfor</code> template tags to loop through the book list, as shown below.
Each iteration populates the <code>book</code> template variable with information for the current list item.</p>

<pre class="brush: html">{% for book in book_list %}
  &lt;li&gt; &lt;!-- code here get information from each book item --&gt; &lt;/li&gt;
{% endfor %}
</pre>

<p>While not used here, within the loop Django will also create other variables that you can use to track the iteration. For example, you can test the <code>forloop.last</code> variable to perform conditional processing the last time that the loop is run.</p>

<h4 id="Accessing_variables">Accessing variables</h4>

<p>The code inside the loop creates a list item for each book that shows both the title (as a link to the yet-to-be-created detail view) and the author.</p>

<pre class="brush: html">&lt;a href="\{{ book.get_absolute_url }}"&gt;\{{ book.title }}&lt;/a&gt; (\{{book.author}})
</pre>

<p>We access the <em>fields</em> of the associated book record using the "dot notation" (e.g. <code>book.title</code> and <code>book.author</code>), where the text following the <code>book</code> item is the field name (as defined in the model).</p>

<p>We can also call <em>functions</em> in the model from within our template — in this case we call <code>Book.get_absolute_url()</code> to get a URL you could use to display the associated detail record. This works provided the function does not have any arguments (there is no way to pass arguments!)</p>

<div class="notecard note">
  <p><strong>Note:</strong> We have to be a little careful of "side effects" when calling functions in templates. Here we just get a URL to display, but a function can do pretty much anything — we wouldn't want to delete our database (for example) just by rendering our template!</p>
</div>

<h4 id="Update_the_base_template">Update the base template</h4>

<p>Open the base template (<strong>/locallibrary/catalog/templates/<em>base_generic.html</em></strong>) and insert <strong>{% url 'books' %} </strong>into the URL link for <strong>All books</strong>, as shown below. This will enable the link in all pages (we can successfully put this in place now that we've created the "books" URL mapper).</p>

<pre class="brush: python">&lt;li&gt;&lt;a href="{% url 'index' %}"&gt;Home&lt;/a&gt;&lt;/li&gt;
&lt;li&gt;&lt;a href="{% url 'books' %}"&gt;All books&lt;/a&gt;&lt;/li&gt;
&lt;li&gt;&lt;a href=""&gt;All authors&lt;/a&gt;&lt;/li&gt;</pre>

<h3 id="What_does_it_look_like">What does it look like?</h3>

<p>You won't be able to build the book list yet, because we're still missing a dependency — the URL map for the book detail pages, which is needed to create hyperlinks to individual books. We'll show both list and detail views after the next section.</p>

<h2 id="Book_detail_page">Book detail page</h2>

<p>The book detail page will display information about a specific book, accessed using the URL <code>catalog/book/<em>&lt;id&gt;</em></code> (where <code><em>&lt;id&gt;</em></code> is the primary key for the book). In addition to fields in the <code>Book</code> model (author, summary, ISBN, language, and genre), we'll also list the details of the available copies (<code>BookInstances</code>) including the status, expected return date, imprint, and id. This will allow our readers to not only learn about the book, but also to confirm whether/when it is available.</p>

<h3 id="URL_mapping_2">URL mapping</h3>

<p>Open <strong>/catalog/urls.py</strong> and add the path named '<strong>book-detail</strong>' shown below.
This <code>path()</code> function defines a pattern, associated generic class-based detail view, and a name.</p>

<pre class="brush: python">urlpatterns = [
    path('', views.index, name='index'),
    path('books/', views.BookListView.as_view(), name='books'),
    path('book/&lt;int:pk&gt;', views.BookDetailView.as_view(), name='book-detail'),
]</pre>

<p>For the <em>book-detail</em> path the URL pattern uses a special syntax to capture the specific id of the book that we want to see.
The syntax is very simple: angle brackets define the part of the URL to be captured, enclosing the name of the variable that the view can use to access the captured data.
For example, <strong>&lt;something&gt;</strong> , will capture the marked pattern and pass the value to the view as a variable "something". You can optionally precede the variable name with a <a href="https://docs.djangoproject.com/en/3.1/topics/http/urls/#path-converters">converter specification</a> that defines the type of data (int, str, slug, uuid, path).</p>

<p>In this case we use <code>'&lt;int:pk&gt;'</code> to capture the book id, which must be a specially formatted string and pass it to the view as a parameter named <code>pk</code> (short for primary key). This is the id that is being used to store the book uniquely in the database, as defined in the Book Model.</p>

<div class="notecard note">
  <p><strong>Note:</strong> As discussed previously, our matched URL is actually <code>catalog/book/&lt;digits&gt;</code> (because we are in the <strong>catalog</strong> application, <code>/catalog/</code> is assumed).</p>
</div>

<div class="notecard warning">
  <p><strong>Warning:</strong> The generic class-based detail view <em>expects</em> to be passed a parameter named <strong>pk</strong>. If you're writing your own function view you can use whatever parameter name you like, or indeed pass the information in an unnamed argument.</p>
</div>

<h4 id="Advanced_path_matchingregular_expression_primer">Advanced path matching/regular expression primer</h4>

<div class="notecard note">
  <p><strong>Note:</strong> You won't need this section to complete the tutorial! We provide it because knowing this option is likely to be useful in your Django-centric future.</p>
</div>

<p>The pattern matching provided by <code>path()</code> is simple and useful for the (very common) cases where you just want to capture <em>any</em> string or integer. If you need more refined filtering (for example, to filter only strings that have a certain number of characters) then you can use the <a href="https://docs.djangoproject.com/en/3.1/ref/urls/#django.urls.re_path">re_path()</a> method.</p>

<p>This method is used just like <code>path()</code> except that it allows you to specify a pattern using a <a href="https://docs.python.org/3/library/re.html">Regular expression</a>. For example, the previous path could have been written as shown below:</p>

<pre class="brush: python"><strong>re_path(r'^book/(?P&lt;pk&gt;\d+)$', views.BookDetailView.as_view(), name='book-detail'),</strong>
</pre>

<p><em>Regular expressions</em> are an incredibly powerful pattern mapping tool. They are, frankly, quite unintuitive and can be intimidating for beginners. Below is a very short primer!</p>

<p>The first thing to know is that regular expressions should usually be declared using the raw string literal syntax (i.e. they are enclosed as shown: <strong>r'&lt;your regular expression text goes here&gt;'</strong>).</p>

<p>The main parts of the syntax you will need to know for declaring the pattern matches are:</p>

<table class="standard-table no-markdown">
 <thead>
  <tr>
   <th scope="col">Symbol</th>
   <th scope="col">Meaning</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td>^</td>
   <td>Match the beginning of the text</td>
  </tr>
  <tr>
   <td>$</td>
   <td>Match the end of the text</td>
  </tr>
  <tr>
   <td>\d</td>
   <td>Match a digit (0, 1, 2, ... 9)</td>
  </tr>
  <tr>
   <td>\w</td>
   <td>Match a word character, e.g. any upper- or lower-case character in the alphabet, digit or the underscore character (_)</td>
  </tr>
  <tr>
   <td>+</td>
   <td>Match one or more of the preceding character. For example, to match one or more digits you would use <code>\d+</code>. To match one or more "a" characters, you could use <code>a+</code></td>
  </tr>
  <tr>
   <td>*</td>
   <td>Match zero or more of the preceding character. For example, to match nothing or a word you could use <code>\w*</code></td>
  </tr>
  <tr>
   <td>( )</td>
   <td>Capture the part of the pattern inside the brackets. Any captured values will be passed to the view as unnamed parameters (if multiple patterns are captured, the associated parameters will be supplied in the order that the captures were declared).</td>
  </tr>
  <tr>
   <td>(?P&lt;<em>name</em>&gt;...)</td>
   <td>Capture the pattern (indicated by ...) as a named variable (in this case "name"). The captured values are passed to the view with the name specified. Your view must therefore declare a parameter with the same name!</td>
  </tr>
  <tr>
   <td>[  ]</td>
   <td>Match against one character in the set. For example, [abc] will match on 'a' or 'b' or 'c'. [-\w] will match on the '-' character or any word character.</td>
  </tr>
 </tbody>
</table>

<p>Most other characters can be taken literally!</p>

<p>Let's consider a few real examples of patterns:</p>

<table class="standard-table">
 <thead>
  <tr>
   <th scope="col">Pattern</th>
   <th scope="col">Description</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td><strong>r'^book/(?P&lt;pk&gt;\d+)$'</strong></td>
   <td>
    <p>This is the RE used in our URL mapper. It matches a string that has <code>book/</code> at the start of the line (<strong>^book/</strong>), then has one or more digits (<code>\d+</code>), and then ends (with no non-digit characters before the end of line marker).</p>

    <p>It also captures all the digits <strong>(?P&lt;pk&gt;\d+)</strong> and passes them to the view in a parameter named 'pk'. <strong>The captured values are always passed as a string!</strong></p>

    <p>For example, this would match <code>book/1234</code> , and send a variable <code>pk='1234'</code> to the view.</p>
   </td>
  </tr>
  <tr>
   <td><strong>r'^book/(\d+)$'</strong></td>
   <td>This matches the same URLs as the preceding case. The captured information would be sent as an unnamed argument to the view.</td>
  </tr>
  <tr>
   <td><strong>r'^book/(?P&lt;stub&gt;[-\w]+)$'</strong></td>
   <td>
    <p>This matches a string that has <code>book/</code> at the start of the line (<strong>^book/</strong>), then has one or more characters that are <em>either</em> a '-' or a word character (<strong>[-\w]+</strong>), and then ends. It also captures this set of characters and passes them to the view in a parameter named 'stub'.</p>

    <p>This is a fairly typical pattern for a "stub". Stubs are URL-friendly word-based primary keys for data. You might use a stub if you wanted your book URL to be more informative. For example <code>/catalog/book/the-secret-garden</code> rather than <code>/catalog/book/33</code>.</p>
   </td>
  </tr>
 </tbody>
</table>

<p>You can capture multiple patterns in the one match, and hence encode lots of different information in a URL.</p>

<div class="notecard note">
  <p><strong>Note:</strong> As a challenge, consider how you might encode a URL to list all books released in a particular year, month, day, and the RE that could be used to match it.</p>
</div>

<h4 id="Passing_additional_options_in_your_URL_maps">Passing additional options in your URL maps</h4>

<p>One feature that we haven't used here, but which you may find valuable, is that you can pass a <a href="https://docs.djangoproject.com/en/3.1/topics/http/urls/#views-extra-options">dictionary containing additional options</a> to the view (using the third un-named argument to the <code>path()</code> function). This approach can be useful if you want to use the same view for multiple resources, and pass data to configure its behavior in each case.</p>

<p>For example, given the path shown below, for a request to <code>/myurl/halibut/</code> Django will call <code>views.my_view(request, fish=halibut, my_template_name='some_path')</code>.</p>

<pre class="brush: python">path('myurl/&lt;int:fish&gt;', views.my_view, <strong>{'my_template_name': 'some_path'}</strong>, name='aurl'),
</pre>

<div class="notecard note">
  <p><strong>Note:</strong> Both named captured patterns and dictionary options are passed to the view as <em>named</em> arguments. If you use the <strong>same name</strong> for both a capture pattern and a dictionary key, then the dictionary option will be used.</p>
</div>

<h3 id="View_class-based_2">View (class-based)</h3>

<p>Open <strong>catalog/views.py</strong>, and copy the following code into the bottom of the file:</p>

<pre class="brush: python">class BookDetailView(generic.DetailView):
    model = Book</pre>

<p>That's it! All you need to do now is create a template called <strong>/locallibrary/catalog/templates/catalog/book_detail.html</strong>, and the view will pass it the database information for the specific <code>Book</code> record extracted by the URL mapper. Within the template you can access the book's details with the template variable named <code>object</code> OR <code>book</code> (i.e. generically "<code><em>the_model_name</em></code>").</p>

<p>If you need to, you can change the template used and the name of the context object used to reference the book in the template. You can also override methods to, for example, add additional information to the context.</p>

<h4 id="What_happens_if_the_record_doesnt_exist">What happens if the record doesn't exist?</h4>

<p>If a requested record does not exist then the generic class-based detail view will raise an <code>Http404</code> exception for you automatically — in production, this will automatically display an appropriate "resource not found" page, which you can customise if desired.</p>

<p>Just to give you some idea of how this works, the code fragment below demonstrates how you would implement the class-based view as a function if you were <strong>not</strong> using the generic class-based detail view.</p>

<pre class="brush: python">def book_detail_view(request, primary_key):
    try:
        book = Book.objects.get(pk=primary_key)
    except Book.DoesNotExist:
        raise Http404('Book does not exist')

    return render(request, 'catalog/book_detail.html', context={'book': book})
</pre>

<p>The view first tries to get the specific book record from the model. If this fails the view should raise an <code>Http404</code> exception to indicate that the book is "not found". The final step is then, as usual, to call <code>render()</code> with the template name and the book data in the <code>context</code> parameter (as a dictionary).</p>

<p>Alternatively, we can use the <code>get_object_or_404()</code> function as a shortcut to raise an <code>Http404</code> exception if the record is not found.</p>

<pre class="brush: python">from django.shortcuts import get_object_or_404

def book_detail_view(request, primary_key):
    book = get_object_or_404(Book, pk=primary_key)
    return render(request, 'catalog/book_detail.html', context={'book': book})</pre>

<h3 id="Creating_the_Detail_View_template">Creating the Detail View template</h3>

<p>Create the HTML file <strong>/locallibrary/catalog/templates/catalog/book_detail.html</strong> and give it the below content. As discussed above, this is the default template file name expected by the generic class-based <em>detail</em> view (for a model named <code>Book</code> in an application named <code>catalog</code>).</p>

<pre class="brush: html">{% extends "base_generic.html" %}

{% block content %}
  &lt;h1&gt;Title: \{{ book.title }}&lt;/h1&gt;

  &lt;p&gt;&lt;strong&gt;Author:&lt;/strong&gt; &lt;a href=""&gt;\{{ book.author }}&lt;/a&gt;&lt;/p&gt; &lt;!-- author detail link not yet defined --&gt;
  &lt;p&gt;&lt;strong&gt;Summary:&lt;/strong&gt; \{{ book.summary }}&lt;/p&gt;
  &lt;p&gt;&lt;strong&gt;ISBN:&lt;/strong&gt; \{{ book.isbn }}&lt;/p&gt;
  &lt;p&gt;&lt;strong&gt;Language:&lt;/strong&gt; \{{ book.language }}&lt;/p&gt;
  &lt;p&gt;&lt;strong&gt;Genre:&lt;/strong&gt; \{{ book.genre.all|join:", " }}&lt;/p&gt;

  &lt;div style="margin-left:20px;margin-top:20px"&gt;
    &lt;h4&gt;Copies&lt;/h4&gt;

    {% for copy in book.bookinstance_set.all %}
      &lt;hr&gt;
      &lt;p class="{% if copy.status == 'a' %}text-success{% elif copy.status == 'm' %}text-danger{% else %}text-warning{% endif %}"&gt;
        \{{ copy.get_status_display }}
      &lt;/p&gt;
      {% if copy.status != 'a' %}
        &lt;p&gt;&lt;strong&gt;Due to be returned:&lt;/strong&gt; \{{ copy.due_back }}&lt;/p&gt;
      {% endif %}
      &lt;p&gt;&lt;strong&gt;Imprint:&lt;/strong&gt; \{{ copy.imprint }}&lt;/p&gt;
      &lt;p class="text-muted"&gt;&lt;strong&gt;Id:&lt;/strong&gt; \{{ copy.id }}&lt;/p&gt;
    {% endfor %}
  &lt;/div&gt;
{% endblock %}</pre>


<div class="notecard note">
  <p><strong>Note:</strong> The author link in the template above has an empty URL because we've not yet created an author detail page to link to.
  Once the detail page exists we can get its URL with either of these two approaches:</p>
  <ul>
    <li>Use the <code>url</code> template tag to reverse the 'author-detail' URL (defined in the URL mapper), passing it the author instance for the book:
      <pre class="brush: python">&lt;a href="{% url 'author-detail' book.author.pk %}"&gt;\{{ book.author }}&lt;/a&gt;</pre>
    </li>
    <li>Call the author model's <code>get_absolute_url()</code> method (this performs the same reversing operation):
      <pre class="brush: plain">&lt;a href="\{{ book.author.get_absolute_url }}"&gt;\{{ book.author }}&lt;/a&gt;</pre>
    </li>
  </ul>
  <p>While both methods effectively do the same thing, <code>get_absolute_url()</code> is preferred because it helps you write more consistent and maintainable code (any changes only need to be done in one place: the author model).</p>
</div>

<p>Though a little larger, almost everything in this template has been described previously:</p>

<ul>
 <li>We extend our base template and override the "content" block.</li>
 <li>We use conditional processing to determine whether or not to display specific content.</li>
 <li>We use <code>for</code> loops to loop through lists of objects.</li>
 <li>We access the context fields using the dot notation (because we've used the detail generic view, the context is named <code>book</code>; we could also use "<code>object</code>")</li>
</ul>

<p>The first interesting thing we haven't seen before is the function <code>book.bookinstance_set.all()</code>. This method is "automagically" constructed by Django in order to return the set of <code>BookInstance</code> records associated with a particular <code>Book</code>.</p>

<pre class="brush: python">{% for copy in book.bookinstance_set.all %}
  &lt;!-- code to iterate across each copy/instance of a book --&gt;
{% endfor %}</pre>

<p>This method is needed because you declare a <code>ForeignKey</code> (one-to many) field in only the "one" side of the relationship (the <code>BookInstance</code>). Since you don't do anything to declare the relationship in the other ("many") models, it (the <code>Book</code>) doesn't have any field to get the set of associated records. To overcome this problem, Django constructs an appropriately named "reverse lookup" function that you can use. The name of the function is constructed by lower-casing the model name where the <code>ForeignKey</code> was declared, followed by <code>_set</code> (i.e. so the function created in <code>Book</code> is <code>bookinstance_set()</code>).</p>

<div class="notecard note">
  <p><strong>Note:</strong> Here we use <code>all()</code> to get all records (the default). While you can use the <code>filter()</code> method to get a subset of records in code, you can't do this directly in templates because you can't specify arguments to functions.</p>

  <p>Beware also that if you don't define an order (on your class-based view or model), you will also see errors from the development server like this one:</p>

  <pre class="brush: plain">[29/May/2017 18:37:53] "GET /catalog/books/?page=1 HTTP/1.1" 200 1637
/foo/local_library/venv/lib/python3.5/site-packages/django/views/generic/list.py:99: UnorderedObjectListWarning: Pagination may yield inconsistent results with an unordered object_list: &lt;QuerySet [&lt;Author: Ortiz, David&gt;, &lt;Author: H. McRaven, William&gt;, &lt;Author: Leigh, Melinda&gt;]&gt;
  allow_empty_first_page=allow_empty_first_page, **kwargs)
</pre>

<p>That happens because the <a href="https://docs.djangoproject.com/en/3.1/topics/pagination/#paginator-objects">paginator object</a> expects to see some ORDER BY being executed on your underlying database. Without it, it can't be sure the records being returned are actually in the right order! </p>

<p>This tutorial hasn't covered <strong>Pagination</strong> (yet!), but since you can't use <code>sort_by()</code> and pass a parameter (the same with <code>filter()</code> described above) you will have to choose between three choices:</p>

<ol>
 <li>Add a <code>ordering</code> inside a <code>class Meta</code> declaration on your model.</li>
 <li>Add a <code>queryset</code> attribute in your custom class-based view, specifying an <code>order_by()</code>.</li>
 <li>Adding a <code>get_queryset</code> method to your custom class-based view and also specify the <code>order_by()</code>.</li>
</ol>

<p>If you decide to go with a <code>class Meta</code> for the <code>Author</code> model (probably not as flexible as customizing the class-based view, but easy enough), you will end up with something like this:</p>

<pre class="brush: python">class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return f'{self.last_name}, {self.first_name}'

    class Meta:
        ordering = ['last_name']</pre>

  <p>Of course, the field doesn't need to be <code>last_name</code>: it could be any other.</p>

  <p>Last but not least, you should sort by an attribute/column that actually has an index (unique or not) on your database to avoid performance issues. Of course, this will not be necessary here (we are probably getting ahead of ourselves with so few books and users), but it is something worth keeping in mind for future projects.</p>
</div>


<p>The second interesting (and non-obvious) thing in the template is where we set a class (<code>text-success</code>, <code>text-danger</code>, <code>text-warning</code>) to color-code the human readable status text for each book instance ("available", "maintenance", etc.). Astute readers will note that the method <code>BookInstance.get_status_display()</code> that we use to get the status text does not appear elsewhere in the code.</p>

<pre class="brush: python"> &lt;p class="{% if copy.status == 'a' %}text-success{% elif copy.status == 'm' %}text-danger{% else %}text-warning{% endif %}"&gt;
 \{{ copy.get_status_display }} &lt;/p&gt;</pre>

<p>This function is automatically created because <code>BookInstance.status</code> is a <a href="https://docs.djangoproject.com/en/3.1/ref/models/fields/#choices">choices field</a>.
Django automatically creates a method <code>get_<strong>FOO</strong>_display()</code> for every choices field "<code>Foo</code>" in a model, which can be used to get the current value of the field. </p>

<h2 id="What_does_it_look_like_2">What does it look like?</h2>

<p>At this point, we should have created everything needed to display both the book list and book detail pages. Run the server (<code>python3 manage.py runserver</code>) and open your browser to <a href="http://127.0.0.1:8000/">http://127.0.0.1:8000/</a>.</p>

<div class="notecard warning">
<p><strong>Warning:</strong> Don't click any author or author detail links yet — you'll create those in the challenge!</p>
</div>

<p>Click the <strong>All books</strong> link to display the list of books. </p>

<p><img alt="Book List Page" src="book_list_page_no_pagination.png"></p>

<p>Then click a link to one of your books. If everything is set up correctly, you should see something like the following screenshot.</p>

<p><img alt="Book Detail Page" src="book_detail_page_no_pagination.png"></p>

<h2 id="Pagination">Pagination</h2>

<p>If you've just got a few records, our book list page will look fine. However, as you get into the tens or hundreds of records the page will take progressively longer to load (and have far too much content to browse sensibly). The solution to this problem is to add pagination to your list views, reducing the number of items displayed on each page. </p>

<p>Django has excellent inbuilt support for pagination. Even better, this is built into the generic class-based list views so you don't have to do very much to enable it!</p>

<h3 id="Views">Views</h3>

<p>Open <strong>catalog/views.py</strong>, and add the <code>paginate_by</code> line shown below.</p>

<pre class="brush: python">class BookListView(generic.ListView):
    model = Book
    paginate_by = 10</pre>

<p>With this addition, as soon as you have more than 10 records the view will start paginating the data it sends to the template.
The different pages are accessed using GET parameters — to access page 2 you would use the URL <code>/catalog/books/?page=2</code>.</p>

<h3 id="Templates">Templates</h3>

<p>Now that the data is paginated, we need to add support to the template to scroll through the results set. Because we might want paginate all list views, we'll add this to the base template. </p>

<p>Open <strong>/locallibrary/catalog/templates/<em>base_generic.html</em></strong> and find the "content block" (as shown below).</p>
<pre class="brush: python">{% block content %}{% endblock %}</pre>
<p>Copy in the following pagination block immediately following the <code>{% endblock %}</code>. The code first checks if pagination is enabled on the current page. If so, it adds <em>next</em> and <em>previous</em> links as appropriate (and the current page number). </p>


<pre class="brush: python">
{% block pagination %}
    {% if is_paginated %}
        &lt;div class="pagination"&gt;
            &lt;span class="page-links"&gt;
                {% if page_obj.has_previous %}
                    &lt;a href="\{{ request.path }}?page=\{{ page_obj.previous_page_number }}"&gt;previous&lt;/a&gt;
                {% endif %}
                &lt;span class="page-current"&gt;
                    Page \{{ page_obj.number }} of \{{ page_obj.paginator.num_pages }}.
                &lt;/span&gt;
                {% if page_obj.has_next %}
                    &lt;a href="\{{ request.path }}?page=\{{ page_obj.next_page_number }}"&gt;next&lt;/a&gt;
                {% endif %}
            &lt;/span&gt;
        &lt;/div&gt;
    {% endif %}
  {% endblock %}</pre>

<p>The <code>page_obj</code> is a <a href="https://docs.djangoproject.com/en/3.1/topics/pagination/#paginator-objects">Paginator</a> object that will exist if pagination is being used on the current page. It allows you to get all the information about the current page, previous pages, how many pages there are, etc. </p>

<p>We use <code>\{{ request.path }}</code> to get the current page URL for creating the pagination links. This is useful because it is independent of the object that we're paginating.</p>

<p>That's it!</p>

<h3 id="What_does_it_look_like_3">What does it look like?</h3>

<p>The screenshot below shows what the pagination looks like — if you haven't entered more than 10 titles into your database, then you can test it more easily by lowering the number specified in the <code>paginate_by</code> line in your <strong>catalog/views.py</strong> file. To get the below result we changed it to <code>paginate_by = 2</code>.</p>

<p>The pagination links are displayed on the bottom, with next/previous links being displayed depending on which page you're on.</p>

<p><img alt="Book List Page - paginated" src="book_list_paginated.png"></p>

<h2 id="Challenge_yourself">Challenge yourself</h2>

<p>The challenge in this article is to create the author detail and list views required to complete the project. These should be made available at the following URLs:</p>

<ul>
 <li><code>catalog/authors/</code> — The list of all authors.</li>
 <li><code>catalog/author/<em>&lt;id&gt;</em></code> — The detail view for the specific author with a primary key field named <em><code>&lt;id&gt;</code></em></li>
</ul>

<p>The code required for the URL mappers and the views should be virtually identical to the <code>Book</code> list and detail views we created above. The templates will be different but will share similar behavior.</p>

<div class="note notecard">
  <p><strong>Note:</strong></p>
  <ul>
    <li>Once you've created the URL mapper for the author list page you will also need to update the <strong>All authors</strong> link in the base template.
    Follow the <a href="#update_the_base_template">same process</a> as we did when we updated the <strong>All books</strong> link.</li>
    <li>Once you've created the URL mapper for the author detail page, you should also update the <a href="#creating_the_detail_view_template">book detail view template</a> (<strong>/locallibrary/catalog/templates/catalog/book_detail.html</strong>) so that the author link points to your new author detail page (rather than being an empty URL).
    The recommended way to do this is to call <code>get_absolute_url()</code> on the author model as shown below.
      <pre class="brush: html">&lt;p&gt;&lt;strong&gt;Author:&lt;/strong&gt; &lt;a href="\{{ book.author.get_absolute_url }}"&gt;\{{ book.author }}&lt;/a&gt;&lt;/p&gt;
    </pre>
    </li>
  </ul>
</div>

<p>When you are finished, your pages should look something like the screenshots below.</p>

<p><img alt="Author List Page" src="author_list_page_no_pagination.png"></p>

<p><img alt="Author Detail Page" src="author_detail_page_no_pagination.png"></p>

<h2 id="Summary">Summary</h2>

<p>Congratulations, our basic library functionality is now complete! </p>

<p>In this article, we've learned how to use the generic class-based list and detail views and used them to create pages to view our books and authors. Along the way we've learned about pattern matching with regular expressions, and how you can pass data from URLs to your views. We've also learned a few more tricks for using templates. Last of all we've shown how to paginate list views so that our lists are manageable even when we have many records.</p>

<p>In our next articles, we'll extend this library to support user accounts, and thereby demonstrate user authentication, permissions, sessions, and forms.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/class-based-views/generic-display/">Built-in class-based generic views</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/class-based-views/generic-display/">Generic display views</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/class-based-views/intro/">Introduction to class-based views</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/templates/builtins">Built-in template tags and filters</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/pagination/">Pagination</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/db/queries/#related-objects">Making queries &gt; Related objects</a> (Django docs)</li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Django/Home_page", "Learn/Server-side/Django/Sessions", "Learn/Server-side/Django")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Introduction">Django introduction</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/development_environment">Setting up a Django development environment</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">Django Tutorial: The Local Library website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/skeleton_website">Django Tutorial Part 2: Creating a skeleton website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Models">Django Tutorial Part 3: Using models</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Admin_site">Django Tutorial Part 4: Django admin site</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Home_page">Django Tutorial Part 5: Creating our home page</a></li>
 <li><strong>Django Tutorial Part 6: Generic list and detail views</strong></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Sessions">Django Tutorial Part 7: Sessions framework</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Authentication">Django Tutorial Part 8: User authentication and permissions</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Forms">Django Tutorial Part 9: Working with forms</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Testing">Django Tutorial Part 10: Testing a Django web application</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Deployment">Django Tutorial Part 11: Deploying Django to production</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/web_application_security">Django web application security</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/django_assessment_blog">DIY Django mini blog</a></li>
</ul>