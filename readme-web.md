---
layout: default
---

### Two components, one system

![A screenshot of the convenor's dashboard](/img/12-Convenor-top.png)

KentProjects was built as 2 discrete and functionally independent components; a web app (built in HTML, JS, & CSS) and
an API (built in PHP). The two are completely decoupled and, assuming the API specifications were matched exactly,
either could be replaced. This README focuses on the Web App, and assumes that the API has been installed and set up
beforehand.

### Language, Timothy

The Web App is almost entirely HTML and JavaScript. Where at all possible, we have used vanilla JS (read: no jQuery,
just pure JS), though there have been occasions where we have needed to. We've largely avoided jQuery because vanilla JS
is *considerably* faster in terms of DOM operations, and using jQuery generally feels lazy. We've used PHP almost
exclusively as a templating engine, and for the sake of some added security.

### Structure
The Web App has 3 main file directories:

- `private/` - Any files we don't want to be directly accessible to the client browser
- `public/` - Any files we *do* want the client browser to be able to access
- `vagrant/` - Configuration files that allow the Web App to be run as a virtual appliance

Within `private/` we have the following:

- `uploads/` - Any files uploaded by the user (e.g. profile pictures) are put in here
- `views/` - Most pages in the `/public/` directory have multiple 'views. For example, `/public/profile.php` will serve
  a different view depending on whether it's being asked to display a project, a student, a supervisor or a group. The
  views are private, and stored in an appropriate subfolder here.
- `api.php` - Performs cURL requests to the API based on specific, pre-defined methods (PUT, GET, POST)
- `auth.php` - Handles authentication against the API
- `functions.php` - Contains some generic global, non-functional methods that may be necessary at various points
- `bootstrap.php` - Connects all the above together in a single file to `require`
- `kentprojects.php` - Contains some global supporting methods are used at various points
- `session.php` - Maintains a PHP session to provide short-term persistance in-browser

Within `public/` we have the following:

- `errors/` - Any pages we want Apache to Apache to redirect the user to when they encounter an error
- `includes/` - Any files that will be included in a page (e.g. CSS, JS, images). Files created by us will be in the
  root of the directory, and files created by a 3rd party will be in a the `lib/` directory
  - `css/` - Stylesheets
  - `fonts/` - Fonts
  - `img/` - Images
  - `js/` - JavaScript
  - `php/` - PHP
- `ajax.php` - Provides a JavaScript-accessible interface for making API requests through `API.php`
- Various user-accessible pages - `dashboard.php`, `intents.php`, `profile.php`, etc.

### Vagrant

For developers with [Vagrant](https://vagrantup.com) installed, a virtual machine pre-configured for the
web app can be easily created by navigating to the root of this repository and running `vagrant up` - this will create
and configure a KentProjects Web App virtual machine, which can be accessed by opening `http://localhost:8080` in a
browser. This environment will (by default) use the KentProjects Development API, which (in theory) will be working off
the same sample data as the KentProjects API.

## Screenshots

![A screenshot](/img/13-Convenor-bottom.png)

![A screenshot](/img/11-SSO.png)

![A screenshot](/img/14-Convenor-students.png)

![A screenshot](/img/15-Convenor-profile.png)

![A screenshot](/img/16-Editor.png)

![A screenshot](/img/17-Profile-Comments.png)

![A screenshot](/img/19-Student-Profile.png)

![A screenshot](/img/20-Student-Staff-Profile.png)

![A screenshot](/img/21-Student-Notifications.png)
