# Symfony Testing Workshop

## Automate a Symfony project tests using functional (and some unit) tests

#### Walkthrough

Cross the following steps one by one and improve your testing skills

#### The context

It's a common best practice to use automated tests in a project.

It helps raise the code quality and robustness, find bugs before going to production, detect regressions,  _etc._

In this workshop, we will use the Symfony testing toolbox to write what the Symfony documentation calls :

- **Unit tests**
: Tests for a standalone piece of code

- **Integration tests**
: Tests for non-autonomous services' methods that depends on other services to work

- **Application tests**
: Tests for a complete functionality from the HTTP Request to the HTTP Response

#### First: let's read some fine manuals

Here's some useful resources that will help you through the process

- [The phpunit documentation](https://phpunit.readthedocs.io/)
- [The Symfony testing documentation](https://symfony.com/doc/current/testing.html)

Take a first look at [the Symfony testing page](https://symfony.com/doc/current/testing.html) and [the writing test with PHPUnit page](https://phpunit.readthedocs.io/en/9.5/writing-tests-for-phpunit.html) before we start.

#### The project

We need a project to write tests in.
In this workshop, we will re-use the checkpoint 3.

Let's adopt a TDD approach:

1. Use an undone version of the checkpoint 3
2. write tests
3. launch them to check that they fail
4. then write code solving the problem
5. and pass the tests (hopefully)

To do so, you should get back your own local git repository of the checkpoint 3 then :

1. Checkout to the master/main branch to reposition your repository in it's original state
2. Create a new branch from master/main for your new TDD solution

You could also make a new clone of the remote github repository.

#### Install the toolbox

Use composer to install phpunit/phpunit & symfony/test-pack.

Follow the first section of the [Symfony doc](https://symfony.com/doc/current/testing.html#the-phpunit-testing-framework)

#### Configure a test database

Tests run in the *test* environment in Symfony projects.

It differs from your usual *dev* environment and has a specific configuration stored in specific configuration files.

So your `.env.local` file will **not** be used.

You have to configure a `env.test` file (for global configuration) and/or a `.env.test.local` (for your local configuration) to access a test database.

A standard `.env.test` file should have been created by Symfony flex and will do for now.

Now, let's configure a test database to run our tests on a separate dataset:

1. Configure a `DATABASE_URL` in a `env.test.local` file to access a specific database for test purposes.
Usualy it has the same name as your dev database with a `_test` suffix.
2. Create the database using `php bin/console --env=test doctrine:database:create`
3. Create the database structure using `php bin/console --env=test doctrine:schema:create`
4. Load the fixtures with `php bin/console --env=test doctrine:fixtures:load`

cf : [Database configuration](https://symfony.com/doc/current/testing.html#configuring-a-database-for-tests)

#### Create the BoatControllerTest class

The first step of the checkpoint is the moveDirection controller.

We need a test class to host the tests for the `src/Controller/BoatController` class.

Test classes live in the `tests` project's directory.

It's a common best practice to have the same structure in `tests` and `src` having *one* test class related to *one* class of the project.

Your job :

- Create an (empty for now) `App\Tests\Controller\BoatControllerTest` class in the `tests/Controller/BoatControllerTest.php` file for your tests

This class will host not only unit tests but application tests also. So it needs to extends the `Symfony\Bundle\FrameworkBundle\Test\WebTestCase`.

See: [the application test section](https://symfony.com/doc/current/testing.html#write-your-first-application-test) of the Symfony documentation to help creating that class.

> **Warning** : The maker bundle version of the checkpoint 3 repository may not be up-to-date with the Symfony documentation.
>
> The `make:test` console command does not exists. Use `make:functional-test` instead or write your class manually. 

#### Test the moveDirection controller

1. Add a unit test (a `testMoveDirectionExists()` method in the `BoatControllerTest`class, for example) to check if a moveDirection() method exists.

> Hint: Take a look at the [assertTrue](https://phpunit.readthedocs.io/en/9.5/assertions.html#asserttrue) PHPunit method and the [method_exists](https://www.php.net/manual/en/function.method-exists.php) PHP function.

2. Add an application test (a `testMoveWrongDirection()` method, for example) to check that some invalid directions generate 404 responses ("A", "Up", "Treasure", "Rome"...).

> Hints:

> - Take a look at [application tests](https://symfony.com/doc/current/testing.html#write-your-first-application-test) in the symfony documentation to see how to create a test client to generate requests and check for responses.
> - Check also the different [response assertions](https://symfony.com/doc/current/testing.html#response-assertions) from the `WebTestCase` class.
> - You could use [test dependencies](https://phpunit.readthedocs.io/en/9.5/writing-tests-for-phpunit.html#test-dependencies) to test for wrong directions only if `moveDirection()` exists.
> - Use a [data provider](https://phpunit.readthedocs.io/en/9.5/writing-tests-for-phpunit.html#data-providers) method to run one test method on multiple cases.

3. Add an application test to check that the four valid directions redirects to /map with a 302 status code.
4. And _of course_ run you tests, check for failure, write your code and re-run until the tests pass

#### Improved moveDirection tests (optional)

4. (optional) You could use the [DAMADoctrineTestBundle](https://github.com/dmaicher/doctrine-test-bundle) to ensure that you tests are independants (don't change the database).
5. (optional) Add an application test to check that there is a boat (a node with a .boat class) on the result page after a valid move.
6. (optional) Add an application test to check the arrival coordinates after a valid move (Coordinates are displayed in div before the boat image).

#### Test the presence of naviguation links on the map

1. Create a tests/Controller/MapControllerTest.php class for your new tests
2. Add an application test to check the presence of four links in the navigation menu

> Hint :
>
> - Take a look at the `DOMCrawler` and it's `filter()` method in the [application test doc](https://symfony.com/doc/current/testing.html#write-your-first-application-test).

3. (optional) Add an application test to check the presence of links with an href attribute for each of the four possible directions

#### Testing the MapManager service

The next checkpoint question is about a `tileExists()` method in a brand new `MapManager` service.

This service will need another service (the `TileRepository`) to function properly. So we'll need Symfony's integration test features to boot the `Kernel` and access the `Service Container`.

To do so, classes that contain integration tests need to extends the `Symfony\Bundle\FrameworkBundle\Test\KernelTestCase`.

More info in the [integration tests section](https://symfony.com/doc/current/testing.html#integration-tests) of the Symfony doc.

1. Create a tests/Service/MapManagerTest.php class for your new tests
2. Add a unit test to check the existance of a App\Service\MapManager class

> Hint: Take a look at the [class_exists](https://www.php.net/manual/en/function.class-exists) PHP function.

3. Add a unit test to check the existance of a tileExists method in the App\Service\MapManager class
3. Add an integration test to check that all valid tile coordinates (from (0,0) to (11,5)) returns true

> Hint: a specific section in the doc shows [how to retrieve services](https://symfony.com/doc/current/testing.html#retrieving-services-in-the-test) in a test class.

4. Add an integration test to check that some invalid tile coordinates (0,-1), (-1,0), (50,50), etc. returns false.

#### Now sail alone (optional)

Walk your way through the rest of the checkpoint 3 features and implement your own test ideas !

Some leads :

- Check that the error message is displayed after a move to a non existing tile attempt
- Check that `getRandomIsland()` returns a valid island tile
- *etc.*
