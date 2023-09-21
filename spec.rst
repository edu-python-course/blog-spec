..
    the specification for a training blog project (Django framework)

.. _Starter template:
    https://github.com/edu-python-course/django-template
.. _Bootstrap template:
    https://github.com/edu-python-course/blog-bootstrap

Blog Site
=========

The project aims to develop a robust and user-friendly web application using
the Django framework. The primary objective is to create a blogging platform
that allows users to publish and manage articles on various topics.
The application will provide an intuitive interface for authors to compose
and format their articles, while also offering a seamless reading experience
for visitors.

**Key Features**

.. rubric:: User Registration and Authentication

The application will provide user registration and authentication
functionality, allowing individuals to create accounts, log in, and
manage their profile information. This feature will enable authors
to have personalized accounts and maintain ownership of their published
articles.

.. rubric:: Article Management

Authors will have the ability to create, edit, and delete articles within
the application. The system will offer a user-friendly editor.
Additionally, authors will be able to categorize articles by assigning
relevant topics to them.

.. rubric:: Topic Subscription:

The application will include a subscription feature that allows users
to subscribe to topics of interest. By subscribing to specific topics,
users will receive notifications or updates whenever new articles are
published in those categories. This feature will enhance user engagement
and ensure that readers stay informed about the topics they find most
valuable.

Overall, the project aims to deliver an efficient blogging platform that
caters to both authors and readers. By providing a seamless user experience
and incorporating essential functionalities such as user authentication,
article management, and topic subscriptions, the application will empower
users to create, share, and explore engaging content within a well-structured
and organized platform.

Here is a starter repository template, that may helps: `Starter template`_.

Challenge: Functional views
---------------------------

It's ok not to return **actual** content for now. Just ensure all the
routes are available and provide correct data. Use anything you like to
return within HTTP response.

-   ``/about/``: Provides a regular text for a user, describing the
    functionalities of the django site.
-   ``/``: A site's homepage. This will contain a list of blogs
    published via the django site.
-   ``/<article>/``: A single article detail view. The URL contains
    dynamic part in it. This will be used to fetch a single article from
    the database.
-   ``/<article>/comment/``: This view will be used to add comments to a
    article.
-   ``/create/``: A article creation form.
-   ``/<article>/update/``: A view to update an existing article data.
-   ``/<article>/delete/``: A view to confirm article deletion.
-   ``/topics/``: A list of topics available on the site.
-   ``/topics/<topic>/subscribe/``: A view to subscribe for a topic.
-   ``/topics/<topic>/unsubscribe/``: A view to unsubscribe from a topic.
-   ``/profile/<str:username>/``: A site user's personal page.
-   ``/set-password/``: This route will be used to change users'
    credential data.
-   ``/set-userdata/``: This route will be user to change users' data.
-   ``/deactivate/``: Account deactivation (deletion) view.
-   ``/register/``: This view contains a user registration form.
-   ``/login/``: This view contains a login form.
-   ``/logout/``: Logout page. This view should redirect user back to
    homepage.

.. warning::
    Optional task(s)

    -   ``/archive/<int:year>/<int:month>/``:
        This view provides a list of articles published on a specified month
        of a specified year. This view should apply date validation and raise
        ``Http404`` for invalid paths. The valid date is a year's 4-digits form
        followed by 1 or 2 digits representing month. Month range should be
        limited as [1..12], leading zero may be omitted. Valid URLs are:

        ::

            /archive/2023/1/
            /archive/2023/01/
            /archive/2023/10/

Challenge: Data models
----------------------

.. hint::
    Django admin may be used to create some dummy data. To gather access to
    admin section you need to create a superuser. The easiest way to do that
    is to use a django command:

    .. code-block:: shell

        python manage.py createsuperuser

.. rubric:: General

-   Each model will be registered for admin site.

.. rubric:: Article topic

-   This is a simple model, that contains information about topic:

    -   topic title (unique value, 64 characters or fewer)
    -   topic short description (255 characters or fewer)

.. rubric:: Article

-   Article requires title (255 characters or fewer).
-   Article requires content (at least 255 characters).
-   Creation date should be autogenerated at article creation, and would
    never be updated.
-   Updated date will be update at each article save.

.. rubric:: Article comment

-   Comment requires creation date (autogenerated).
-   Comment requires message text.

.. rubric:: Relationships

.. note::
    Standard Django user model **will be** used for now. To apply model
    reference pass ``"auth.User"`` as related model. Users can be created
    via admin page. You can also refer the same model as shown below:

    .. code-block:: python

        from django.contrib.auth import get_user_model
        from django.conf import settings

        UserModel = get_user_model            # option 1
        UserModel = settings.AUTH_USER_MODEL  # option 2
        UserModel = "auth.User"               # option 3

-   ``article`` and ``topics`` have *many-to-many* relationship.
-   ``article`` and ``user`` have *one-to-many* relationship. A article
    can have **only one** author, but users can create as many articles as they
    want.
-   ``article`` and ``comment`` have *one-to-many* relationship. A article
    may be a container for many comments, but a comment is related to a single
    article.
-   ``comment`` and ``user`` have *one-to-many* relationship. It's similar to
    *article - user* relationship.
-   ``topic`` and ``user`` use *many-to-many* relationship. A single user can
    prefer none or as many topics as needed and vice versa.
    This relationship represents topics preferred by a certain blog user.
    Also this provides an additional option to mark some of preferred topics
    with a **notify** flag, to receive newsletters about specified topics
    updates.
    The difference between *prefer* and *notify* is that *preferred* topics
    affect the articles list for a user, and *notify* is responsible for
    newsletters for the user.

.. rubric:: UML diagram

.. mermaid:: mermaid/models.mmd
    :align: center

Challenge: ORM
--------------

Update existing views to represent entities that are actually stored
inside the project's database.

-   ``/``: should represent a list of existing articles.
-   ``/<article>/``: should represent a single existing article.
-   Detail view should obtain a list of article related comments.
-   ``/profile/<str:username>/``: should contain information about user and a
    list of articles authored by this user.
-   All views related to a single entity (*detail*, *update*, *delete*,
    *profile*) should raise ``Http404`` in case entity could not be
    fetched.

.. hint::
    It's common to store dedicated functions/classes that supports some
    specific business logic in a separate module called ``services.py`` or
    ``utils.py`` within an application directory.

.. warning::
    Optional task(s)

    -   Create a service to fetch articles, ordered by a specified user
        preferences. This means, topics preferred by a user affects articles
        orders. Queryset should be ordered in way, to have articles with most
        corresponding topics at the begging.

Challenge: Templates
--------------------

.. hint::
    Helpful link: `Bootstrap template`_

.. important::
    Links related to user data **may be** placeholders for now.

.. rubric:: General

-   **All** templates should be inherited from the ``base.html`` template.
-   Each page has a descriptive title HTML tag, which included ``| Blog``
    suffix, e.g. "Articles | Blogs", "Sample | Blog", "Login | Blog" etc.
-   Each page should contain a link to homepage (``/`` URL route).
-   Each page contains a list of registered topics. Each view of this kind
    filters only articles of a relevant topic. This should be implemented
    with a custom template context processor.
-   Replace content block for *about* page with some static content.
-   Application-level templates should be located within appropriate
    applications.

.. warning::
    Optional task(s)

    -   Each page should contain a list of links to archive views for the
        last year.
    -   Each page should contain an inclusion-block(s) with following links:

        -   ``/registration/``: new user registration form
        -   ``/login/``: user login form
        -   ``/create/``: article creation page

.. rubric:: Articles list

-   Main page contains a list of published articles.
-   Each article entity is rendered using a custom template tag.
-   ``article`` template tag renders information about a article object:

    -   article title
    -   article content (trimmed to ~50 characters)
    -   article creation date
    -   related topics (3 topics or fewer)
    -   number of related comments

.. todo:
    Include a block with most commented articles in user's preferred topics,
    up to 3 articles. This require additional service/util implementation.

.. rubric:: Article details

.. important::
    Article update and deletion views **would not** affect data for now.

-   A article detail page contains links to **update** or **delete**
    current article.
-   Page provides information about article:

    -   Article title
    -   Creation date
    -   Author name
    -   Related topics
    -   Article content

-   Page contains a list of related comments.
-   Each comment contains:

    -   Author name
    -   Comment creation time
    -   Comment message

.. rubric:: Profile page

-   Author page contains information about author:

    -   First name
    -   Last name

    Add more information, if needed.

-   Author page contains a list of articles created by this author.
-   Author page contains buttons/links to change user data and password
    or deactivate a user's account.

.. rubric:: Forms

.. important::
    There is **no need** to add actual forms now. They will be generated
    by Django. This section describes the final view of these pages.
    It's ok just to create a dedicated templates for future use.

-   ``/register/`` page contains a new user registration form. It should take
    inputs from the user:

    -   username
    -   email
    -   password
    -   confirm password

-   ``/login/`` page contains a user login form. It should take the inputs
    from the user:

    -   username
    -   password

-   ``/create/`` and ``/<article>/update/`` pages contain a form to collect
    a article's data:

    -   title
    -   relevant topics
    -   content

-   ``/<article>/delete`` page contains a simple delete confirmation form.
-   Change password form has two fields:

    -   new password
    -   confirm password

-   Change user's data form collects all information, that can be changed,
    e.g. ``username``, ``first name``, ``last name`` etc.
-   User preferences page contains a list of available topics. User can
    mark some topics as preferred. Also for preferred topics an option
    to *subscribe* for the newsletters becomes available.

Challenge: Articles' slugs
--------------------------

.. warning::
    This is an optional challenge in addition to:

    - `Challenge: Functional views`_
    - `Challenge: Templates`_
    - `Challenge: Data models`_
    - `Challenge: ORM`_

-   Update ``Article`` model with ``slug`` field. The slug value is:

    -   required for each article
    -   unique for each article

-   Create a data migration to provide slugs for existing articles.
-   ``slug`` should be auto-generated on article save.
    The pattern is ``article.title-article.created_date``,
    e.g. "Sample article" created at "03/24/2023" should receive slug:
    ``sample-blog-article-2023-03-23``.
-   Update detail view URL path with article slug as dynamic portion.

Challenge: Auth forms
---------------------

-   Create form for new users registration with required fields:

    -  ``username``
    -  ``email``
    -  ``password``
    -  ``confirm password``

-   ``username`` value should be validated against existing values.
-   ``password`` and ``confirm password`` values should match.
-   Create form for existing users login.
-   Validations errors are to be rendered on the template.

Challenge: Authentication
-------------------------

-   For anonymous users ``/register/`` and ``/login/`` links should be
    visible in navbar.
-   For authenticated users ``/logout/`` and ``/create/`` links should be
    visible in navbar.
-   If authenticated user is admin or stuff they should see a link to
    admin page.
-   ``/register/``: Users should provide all required information about
    them: desired username and email. Name data (both first and last) is
    optional. After user creation they should be redirected to login page
    to perform authentication process. Invalid form should provide
    information about error(s).
-   ``/login/``: Users should provide their credentials to login. In case
    login is successful they should be redirected to their profile (if no
    ``?next=url`` query string available).
-   ``/create/``: Only authenticated users should be able to visit this
    page. In case anonymous user is trying to request this view they
    should be redirected to the login view first, and after successful
    authentication get back to article creation. Then a article is created, it
    should be authored by the currently authenticated user.
-   ``/<article>/comment/``: Currently authenticated user should be
    referenced as a comment's author.
-   Articles may be modified or deleted only by their authors from the detail
    article page. However, admins can still performs articles actions from
    the admin page.
-   User related paths are restricted for non-authorized users.

    -   ``/set-password/``
    -   ``/set-userdata/``

-   ``POST`` request ``/deactivate/`` should mark current authenticated user as
    *deleted* and log them user.

.. warning::
    Optional task(s)

    -   Adjust the order of article list according to authenticated user
        preferences. For anonymous users keep default ordering.
    -   Implement account reactivation behavior. The exact workflow does not
        mater. The sample scenario is to collect email for the user and check
        it for existing in the database. After that create a request for admin
        to activate an account and send a confirmation email, when all is done.

Challenge: Article related forms
--------------------------------

-   Implement a article form. This form will be used to create new articles
    and to update existing ones.
-   Each created article should have at least one related topic.
-   Implement a comment form to gather a comment from a user.
-   Only ``POST`` requests to ``/<article>/comment/`` are allowed from this
    moment.
-   Implement functionality:

    -   article creation
    -   article update
    -   article comment (create a related comment)
    -   article deletion

-   Only authenticated users can create articles.
-   Article can be edited only by its author.
-   Article can be deleted only by its author.
-   Only authenticated users can comment articles.

.. todo:
    Add admin site challenge: customize admin site view, forms etc.
    Apply admin permissions and restrictions for the admin site.

Challenge: Class-Based Views
----------------------------

-   Replace **all** existing views via ``CBV``.
-   Existing functionality should not be corrupted.

.. note::
    It's ok to use built-in Django CBV if needed.

Challenge: Serializers
----------------------

.. rubric:: Article topic

-   Topic serializer is for read-only purposes only. Topics can be created
    via admin page only.
-   Serialized data should contain all available data, e.g. ``pk``, ``title``,
    ``description``.

.. rubric:: Article comment

-   article comment serializer can perform both reading and writing
    operations. But it can't be used to *update* or *delete* comment.
-   Random, or pre-defined user may be used as comment's author for now.
    This will be fixed in the future.

.. rubric:: Article

-   article serializer provides full access to articles. All operations
    are available: list, retrieve, create, update and destroy.

.. rubric:: User

-   User serializer provides full access to site users data. All operations
    are available for now: list, retrieve, create, update and destroy.
    This behavior will be fixed in the future, to prevent unauthorized data
    modifications.

Challenge: API views
--------------------

All blog-site functionality are to be reflected via REST API.

.. note::
    It's ok to pass *pre-defined* user as argument in request's body.
    This will be fixed in the next challenge.

Challenge: Authentication and Permissions
-----------------------------------------

-   Implement authentication system for REST API.

    -   For non-authenticated users it is possible to create a new account
    -   For non-authenticated users it is possible to obtain authentication
        data.

-   Access to user data is restricted. Authorized users can manipulate
    only their own data (e.g. ``retrieve``, ``update``).
-   Admins can retrieve all users data (``list``), but can't change them
    via REST API. However, it is still possible via admin page.
-   Authorized users can ``create`` articles or ``update`` and ``delete``
    articles created by them.
-   Authorized users can add comments to a specified article.
