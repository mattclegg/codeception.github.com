---
layout: default
title: Testing with Codeception
---

# Testing with Codeception.

We build web sites with PHP, we build web services and web applications. But is PHP good enough in testing them?

How often do you see PHP projects with no line of test written? From my experience this situation happens quite often. We should state the unpleasent fact: tests are not popular around the PHP world. Sure, advanced developers with 5+ years of experience in PHP and other programming languages know what the TDD means and know how to use a PHPUnit. But juniors and seniors are just skipping testing and produce unstable web applications.

From my point of view the key issue is not in developers themselves. They are pretty good and skilled. But PHP lacks the tools for testing. If you write a site and you want to test it's behavior, what the natural way to do so? Selenium? PHPUnit + Selenium? This tools are powerful but they are rather complex. Their usage require more experience then writing site itself.

For last two months I was developing a simple, yet powerful alternative testing framework: Codeception. The main point of it is making tests to be easy to read, easy to write and easy to debug. This code illustrates a common acceptance test in Codeception:

```
<?php
$I = new WebGuy($scenario);
$I->wantTo('create new blog post');
$I->amOnPage('/blog/posts');
$I->click('Create new post');
$I->fillField('Title','Codeception, a new way of testing!');
$I->fillField('Text','Codeception is new PHP full-stack testing framework.');
$I->click('Send');
$I->see('Congratulations, your post is successfully created!');
?>
```

It's pretty clear, right? But here goes another feature of Codeception: this code can be executed as a functional test in symfony, Symfony2, Zend Framework, with PHP web scrapper Goutte, or even with Selenium!

Codeception is all about behavior-driven testing. For all parts of your application you describe your actions and the results you are expecting to see. User interacts with your web application through a web browser. He opens page, clicks links, fills the forms and in the end he sees the site has generated a proper result in a response. In Codeception you record user's steps and make testing engine reproduce them. You need a web server running to perform a requests through a web browser.

Writing tests is just about choosing actions from a list and injecting proper parameters.
![IDE autocomplition demo](http://dl.dropbox.com/u/930833/codecept.png)

If you are ready to start wiriting first Codeception test just follow the [installtion steps](http://codeception.com/install).
After you install package and it's dependencies, select a project you want to test (I hope you have one) and run a bootstrap command in it's root:

```
$ codecept bootstrap
```
 It has created a 'tests' directory with 3 default testing suites:

* acceptance
* functional
* unit

Let's start with sample acceptance test and place into 'tests/acceptance/FrontPageCept.php'.

``` php
<?php
$I = new WebGuy($scenario);
$I->wantToTest('front page of my site');
$I->amOnPage('/');
$I->see('A sample text on my site');
?>
```

Replace 'A sample text on my site' to text that actually is on your site.
To make it run we should start a local web server with application which is being tested. 
A url of this application should be specified in suite configuration file 'tests/acceptance.suite.yml'.

```
    config:
        PhpBrowser:
            url: 'here goes url'
```

Now a test can be executed with run command.

```
$ codecept run acceptance
```

A result should be seen:

```
<source>Suite acceptance started
Trying to see front page of my site (StartPageCept) - Ok

Time: 1 second, Memory: 21.00Mb

OK (1 test, 1 assertions)
```

If it's ok now, a form testing can be demonstrated. Maybe the most often used forms are login forms.

``` php
<?php
$I = new WebGuy($scenario);
$I->wantTo('log in as regular user');
$I->amOnPage('/login');
$I->fillField('Username','davert');
$I->fillField('Password','qwerty');
$I->click('Login');
$I->see('Hello, davert');
?>
```

The 'fillField' and 'click' command take element name or CSS selector as paramater. Thus, if you don't use labels for fields, you can rewrite this scenario:

``` php
<?php
$I = new WebGuy($scenario);
$I->wantTo('log in as regular user');
$I->amOnPage('/login');
$I->fillField('form#login input[name=login]','davert');
$I->fillField('form#login input[name=password]','qwerty');
$I->click('form#login input[type=submit]');
$I->see('Hello, davert');
?>
```

Or this can be shortened to just one command:

``` php
<?php
$I = new WebGuy($scenario);
$I->wantTo('log in as regular user');
$I->amOnPage('/login');
$I->submitForm('form#login', array('login' => 'davert', 'password' => 'qwerty'));
$I->see('Hello, davert');
?>
```

As we seen tests in Codeception looks pretty simple and compact. Testing environment is prepared out of the box, no bootstrap code should be written. 
You can compare Codeception to Behat, with PHP DSL, but Codeception can do much more then executing scenarios. As you've seen it's not limited to acceptance tests only.

Codeception is fully documented, look into the guides for full reference.
Codeception is beta, but it will evolve. Use it. Test your applications. Make them stable.

Next time a functional tests will be covered.
