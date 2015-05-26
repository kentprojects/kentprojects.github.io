---
layout: default
---

We chose to have an API to attempt to abstract most of the logic for this project into a single platform, as well as
attempting to build a solid platform for native & web applications to be built off.

## PHP

Okay. Let's deal with the elephant on the page. There is a strong vocal community against PHP. However, given our team
and our experiences, especially experience gained from our industry placements, we chose to write the API in pure PHP,
ignoring all PHP frameworks, writing everything ourselves. This counters against the shallow learning curve with PHP,
and allows us to write our own caching & database classes, to make interacting with data as quick as possible!

There are a number of reasons for and against using a PHP framework, especially those like
[CodeIgniter (External Link)](http://www.codeigniter.com) and [Kohana (External Link)](https://kohanaframework.org). The
arguments in their favour include such reasons as "easy to use", "good documentation" and "well supported". Arguments
against include "poor error handling", “awkward design decisions”, “no tests” and "poorly supported". In the end we went
against using an existing PHP framework due to their footprint and their error handling - most frameworks handle errors
in such a level of detail that the processing cost of handling the error becomes greater than the cost of completing the
original request. It also would allow us to explore the core of frameworks, and build off existing frameworks, and
potentially look to create our own!

## MySQL

Given the hand-in-hand nature that it shares with PHP, MySQL was the easiest choice to store the data for this project.
It handles relationships between data extremely well with `FOREIGN KEY`s, and we can use the
[PHP MySQL Native Driver (External Link)](http://php.net/manual/en/mysqlnd.overview.php)
which offers increased efficiency due to it's specific implementation. What that means for us is quicker interactions
with the MySQL database, which in turn makes each API request faster. MySQL also has excellent documentation available
online, and a member of our team has a lot of experience with MySQL which therefore should make building and interacting
with the database as painless as possible.

## Structure

The initial structure for the API was as follows:

- `classes/` - A folder for classes used throughout the platform.
- `controllers/` - A folder for controller that join all the pieces together.
- `database/` - A folder to contain all the database information.
- `docs/` - A folder to document the various HTTP endpoints made available by the API.
- `logs/` - A folder for any logs created by the platform.
- `models/` - A folder for the models that interact with the database.
- `system/` - A folder for any classes required used in the core functionality of the API.
- `vagrant/` - A folder for any Vagrant-specific files.
- `vendor/` - A folder for any third-party scripts.
- `LICENSE.md` - A file defining the license for the API.
- `README.md` - A simple README for the API.
- `Vagrantfile` - The Vagrant file defining how Vagrant should operate.
- `api.php` - The main PHP file which routes & handles every request receive by the API.
- `config.ini` - The main INI file holding the configuration for the API (such as cache & database details).
- `exceptions.php` - A file defining the various exceptions used throughout the API.
- `functions.php` - A file defining various global functions used throughout the API.
- `kentprojects.sh` - A simple script making certain repetitive actions easier.

When an API HTTP tester was required, the `eye/` folder was added to the structure to hold all the site files for that,
and the `vagrant/apache.conf` file was updated to allow access to the API HTTP tester.

When [PHPUnit (External Link)][phpunit] was pulled into the API, the `tests/` folder was added to the structure to hold
all the test as well as the configuration for those tests.

When we decided to use [CircleCi (External Link)][circleci], we added a configuration file (`circle.yml`) to the
structure, as well as a `config.ci.ini` file since CircleCi requires a particular configuration for databases and such.

When we introduced notifications to the API, a `notifications.php` file was added to the structure to allow a simple
script for the notifications pipe to execute.

When the admin area was added to the API, we added an `admin/` folder to the structure to hold all the relevant files
for the admin area, as well as an `admin.php` file to act as a main PHP file for routing & handling every request to the
admin area.

## Large changes that occurred

Building the API was relatively smooth, with only a couple of breaking change events that were integrated smoothly.

The first substantial addition to the API involved adding global ACLs to control who could perform specific actions to
entities. These actions were generalised into **create**, **read**, **update** and **delete** actions (or **CRUD**
operations, if you prefer). We implemented ACLs with a single table (relevantly named `ACL`) and wrote a single PHP
class to control the ACLs listed in this table. The ACLs were also built with a concept of inheritance, so if a user has
**read** permissions to the `group` entity, they inherently have **read** permissions to all group entities (for
example, `group/12` & `group/42`).

Later on, we encountered a problem where entities were being cached with all sub-entities too, so if a sub-entity was
updated that change did not propagate to other entities. For example, if a *project* entity contained a *group* entity,
and the group's name changed, that change did not appear in the *project* entity unless the cache was cleared. To
counter this, we had to write a unique `render` method in the controllers that individually rendered each entity, and
progressed to sub-entities, like so:

{% highlight text %}
  [ controller ]
    -> [ render ]
      -> [ for each entity ]
        -> [ render entity ]
          -> [ for each sub-entity ]
            -> [ render sub-entity ]
              -> [ for each sub-entity ]
                -> [ ... ]
{% endhighlight %}

The end result was adding a special `render` method to every class that needed to be rendered, which looked something
akin to:

{% highlight php %}
<?php
class Model_Project extends Model
{
  ...

  public function render(Request_Internal $request, Response &$response, ACL $acl, $internal = false)
  {
    $this->getCreator();
    $this->getSupervisor();

    $group = is_object($this->group) ? $this->group->render($request, $response, $acl, "project") : $this->group;

    return array_merge(
      parent::render($request, $response, $acl, $internal),
      array(
        "year" => (string)$this->year,
        "group" => $group,
        "name" => $this->name,
        "description" => $this->getDescription(),
        "creator" => $this->creator->render($request, $response, $acl, true),
        "supervisor" => $this->supervisor->render($request, $response, $acl, true),
        "hasCasSubmission" => $this->hasBeenSubmittedToCAS(),
        "permissions" => $acl->get($this->getEntityName()),
        "created" => $this->created,
        "updated" => $this->updated
      )
    );
  }

  ...
}
{% endhighlight %}

As you can see, each subsequent call to `render` sets `$internal` to `true` (with a special case for the group) which
forces the latest (cached) data for each sub-entity to be rendered. This ensures that the latest and quickest data is
returned for each API call.

To our surprise, caching values this way resulted in a faster request, making this rendering method quicker than
natively encoding the objects to JSON with `json_encode` and
[JsonSeralizable (External Link)](http://php.net/manual/en/class.jsonserializable.php), as we were previously doing.

## Admin Application

James started work on an administration website which interacted with the data closer than the API did, which therefore
was forced to co-exist in the API repository. Even though it wasn't finished, the code is included in the source code
for the API. The purpose of the Admin area was to provide an interface for the conveners and CAS office, offering a
finer level of control over the data the API was storing.

## Vagrant

For developers with [Vagrant (External Link)](https://vagrantup.com) installed, a virtual machine pre-configured for the
API can be easily created by navigating to the root of this repository and running `vagrant up` - this will create and
configure a KentProjects API virtual machine, and import some sample data for developers to interact with. The unit
tests can also be run from this virtual machine, and the API can be accessed by opening `http://localhost:8060` in a
browser.

[circleci]: https://circleci.com/
[phpunit]: https://phpunit.de/
