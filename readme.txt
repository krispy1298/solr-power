=== Solr Search for WordPress ===
Contributors: getpantheon, Outlandish Josh, 10up, collinsinternet, andrew.taylor, danielbachhuber, mattleff, mikengarrett, jazzs3quence, jspellman, pwtyler
Tags: search
Requires at least: 4.6
Requires PHP: 7.1
Tested up to: 6.3
Stable tag: 2.5.2
License: GPLv2 or later
License URI: http://www.gnu.org/licenses/gpl-2.0.html

Improve your user experience with the Apache Solr search engine for your WordPress website.

== Description ==

[![Travis Build Status](https://travis-ci.org/pantheon-systems/solr-power.svg?branch=master)](https://travis-ci.org/pantheon-systems/solr-power)
[![Circle CI Build Status](https://circleci.com/gh/pantheon-systems/solr-power.svg?style=shield&circle-token=3af522a81a29eab25828a6b0d52e2f1afa7f044b)](https://circleci.com/gh/pantheon-systems/solr-power)

Search is critical for your site, but the default search for WordPress leaves a lot to be desired. Improve your user experience with the Apache Solr search engine for your WordPress website.

* Fast results, with better accuracy.
* Enables faceting on fields such as tags, categories, author, and page type.
* Indexing and faceting on custom fields.
* Drop-in support for [WP_Query](https://codex.wordpress.org/Class_Reference/WP_Query) with the `solr_integrate` parameter set to true.
* Completely replaces default WordPress search, just install and configure.
* Completely integrated into default WordPress theme and search widget.
* Very developer-friendly: uses the modern [Solarium](http://www.solarium-project.org/) library

== Installation ==

The Solr Power plugin can be installed just like you'd install any other WordPress plugin.

Because Solr Power is intended to be a bridge between WordPress and the Apache Solr search engine, you'll need access to a functioning Solr 3.6 instance for the plugin to work as expected. This plugin does not support other versions of Solr. The plugin also requires PHP 7.1 or higher.

If you're using the Solr Power plugin on Pantheon, setting up Apache Solr is as easy as enabling the Apache Solr add-on in your Pantheon dashboard. Once you've done so:

1. Configure which post types, taxonomies and custom fields to index by going to the **Indexing** tab of the Solr Power settings page.
2. Index your existing content by going to the plugin options screen and selecting the applicable **Actions**:
   - - **Index Searchable Post Types**
3. Search on!
4. See the examples/templates directories for more rich implementation guidelines.

If you're using the Solr Power plugin elsewhere, you'll need to install and configure Apache Solr. On a Linux environment, this involves four steps:

1. Install the Java Runtime Environment.
2. Run `./bin/install-solr.sh` to install and run Apache Solr on port 8983.
3. Configuring Solr Power to use this particular Solr instance by setting the `PANTHEON_INDEX_HOST` and `PANTHEON_INDEX_PORT` environment variables.
4. Copying `schema.xml` to the Solr configuration directory (a path similar to `solr/conf/schema.xml`).

Alternatively, there are a couple of community-maintained Docker containers you may be able to use: [kalabox/pantheon-solr](https://hub.docker.com/r/kalabox/pantheon-solr/), [kshaner/solr](https://hub.docker.com/r/kshaner/solr/).

In a local development environment, you can point Solr Power to a custom Solr instance by creating a MU plugin with:

    <?php
    /**
     * Define Solr host IP, port, scheme and path
     * Update these as necessary if your configuration differs
     */
    putenv( 'PANTHEON_INDEX_HOST=192.168.50.4' );
    putenv( 'PANTHEON_INDEX_PORT=8983' );
    add_filter( 'solr_scheme', function(){ return 'http'; });
    define( 'SOLR_PATH', '/solr/wordpress/' );

** Note for Lando users **

If you are using lando for development, the MU plugin is not needed. Lando auto configures everything for your local environment to connect to the docker index it maintains and if you overrite the ENV variables it will mess with that configuration.

== Development ==

This plugin is under active development on GitHub:

[https://github.com/pantheon-systems/solr-power](https://github.com/pantheon-systems/solr-power)

Please feel free to file issues there. Pull requests are also welcome! See [CONTRIBUTING.md](https://github.com/pantheon-systems/solr-power/blob/main/CONTRIBUTING.md) for information on contributing.

For further documentation, such as available filters and working with the `SolrPower_Api` class directly, please see the project wiki:

[https://github.com/pantheon-systems/solr-power/wiki](https://github.com/pantheon-systems/solr-power/wiki)

You may notice there are two sets of tests running, on two different services:

* [PHPUnit](https://phpunit.de/) test suite against a Solr instance.
* The [Behat](http://behat.org/) test suite against a Pantheon site, to ensure the plugin's compatibility with the Pantheon platform.

Both of these test suites can be run locally, with a varying amount of setup.

PHPUnit requires the [WordPress PHPUnit test suite](https://make.wordpress.org/core/handbook/testing/automated-testing/phpunit/), and access to a database with name `wordpress_test`. If you haven't already configured the test suite locally, you can run `bash bin/install-wp-tests.sh wordpress_test root '' localhost`. You'll also need access to a running Solr instance, in order to run the unit tests against Solr.

Behat requires a Pantheon site with Solr enabled. Once you've created the site, you'll need [install Terminus](https://github.com/pantheon-systems/terminus#installation), and set the `TERMINUS_TOKEN`, `TERMINUS_SITE`, and `TERMINUS_ENV` environment variables. Then, you can run `./bin/behat-prepare.sh` to prepare the site for the test suite.

Note that dependencies are installed via Composer and the `vendor` directory is not committed to the repository. You will need to run `composer install` locally for the plugin to function. You can read more about Composer [here](https://getcomposer.org)

== WP-CLI Support ==

This plugin has [WP-CLI](http://wp-cli.org/) support.

All Solr Power related commands are grouped into the `wp solr` command, see an example:

    $ wp solr
    usage: wp solr check-server-settings
       or: wp solr delete [<id>...] [--all]
       or: wp solr index [--batch=<batch>] [--batch_size=<size>] [--post_type=<post-type>]
       or: wp solr info [--field=<field>] [--format=<format>]
       or: wp solr optimize-index
       or: wp solr repost-schema
       or: wp solr stats [--field=<field>] [--format=<format>]

    See 'wp help solr <command>' for more information on a specific command.

You can see more details about the commands using `wp help solr`:

    **NAME**

      wp solr

    **DESCRIPTION**

      Perform a variety of actions against your Solr instance.

    **SYNOPSIS**

      wp solr <command>

    **SUBCOMMANDS**

      check-server-settings      Check server settings.
      delete                     Remove one or more posts from the index.
      index                      Index all posts for a site.
      info                       Report information about Solr Power configuration.
      optimize-index             Optimize the Solr index.
      repost-schema              Repost schema.xml to Solr.
      stats                      Report stats about indexed content.


== WP_Query Integration ==

Use Solr in a custom WP_Query instead of querying a database. Add ```'solr_integrate' => true``` to the query arguments.

**NOTE:** Currently, only basic queries, tax_query, meta_query and date_query are supported. See ```examples/example.custom_WP_Query.php``` for an example.

A meta_query can use the following compare operators:

* ```'='```
* ```'!='```
* ```'>'```
* ```'>='```
* ```'<'```
* ```'<='```
* ```'LIKE'```
* ```'NOT LIKE'```
* ```'IN'```
* ```'NOT IN'```
* ```'BETWEEN'```
* ```'NOT BETWEEN'```
* ```'EXISTS'```
* ```'NOT EXISTS'```

(```'REGEXP'```, ```'NOT REGEXP'```, and ```'RLIKE'``` are not supported.)

== Configuration Tips ==

= Searching by author name =

To support searching by author name (e.g. where "Pantheon" would return posts authored by the "Pantheon" user), add the following to your custom `schema.xml`:

      <copyField source="post_author" dest="text"/>

= Boosting relevancy score by publish date =

The following guidance can be used to extend the Solr index and modify boosts beyond just this example.

To support math functions on dates, you must add a custom `schema.xml` to Solr and **reindex with the new schema**.

Add the following to `schema.xml`:

      <!-- Add to <types> -->
      <!-- See: https://lucene.apache.org/solr/6_2_0/solr-core/org/apache/solr/schema/TrieDateField.html -->
      <fieldType name="tdate" class="solr.TrieDateField" omitNorms="true" precisionStep="6" positionIncrementGap="0"/>

      <!-- Add to <fields> -->
      <field name="post_date_iso" type="tdate" indexed="true" stored="true" required="true" />

Add the following to your `functions.php` file.


      <?php
      /**
       * Hooks into the document build process to add post date field in proper format.
       */
      function my_solr_build_document( $doc, $post_info ) {
            $post_time = strtotime( $post_info->post_date );
            // Matches format required for TrieDateField
            $doc->setField( 'post_date_iso', gmdate( 'c\Z', $post_time ) );
            return $doc;
      }
      add_filter( 'solr_build_document', 'my_solr_build_document', 10, 2 );

      /**
       * Hooks into query processor, Dismax, to add publish date boost.
       * See: https://www.metaltoad.com/blog/date-boosting-solr-drupal-search-results
       */
      function my_solr_dismax_query( $dismax ) {
            $dismax->setQueryParser( 'edismax' );
            $dismax->setBoostQuery( 'recip(abs(ms(NOW/HOUR,post_date_iso),3.16e-11,1,1))' );
            return $dismax;
      }
      add_filter( 'solr_dismax_query', 'my_solr_dismax_query' );


= Common issues =

* Failing to post the schema.xml will result in an error during indexing, "Missing `post_date_iso` field."
* If you have the field and type in the schema, but don't add the `solr_build_document` filter, you will get a similar error.
* If the `post_date_iso` field is missing from the index, Solr will ignore this boost and return regular results.
* Trying to use a regular date field for the boost query will result in an error in the request instead of results.

= Explicit Commit vs Autocommit =

Once solr has sent the data to the solr server, solr must COMMIT the data to the index and adjust the index and relevancy ratings accordingly before that data can appear in search results. 

By default, Solr Search for WordPress has auto-commit disabled. The index is committed when the uncommitted item is two minutes old, or the cron runs. By default, the cron runs on the Pantheon platform every hour.

When autocommit is enabled, Solr Search for WordPress commits data when it sends every post. When running on Pantheon, we recommend leaving autocommit disabled to aid overall site performance.

To enable autocommit, add the following to `wp-config.php` or an mu-plugin.

      define( 'SOLRPOWER_DISABLE_AUTOCOMMIT', false );

To force-commit data outside of a normal cron run, from the command line, you can run the command below or simply force a cron-run.

      wp solr commit

== Frequently Asked Questions ==
= Where do I report security bugs found in this plugin? =
Please report security bugs found in the source code of the Solr Power plugin through the [Patchstack Vulnerability Disclosure Program](https://patchstack.com/database/vdp/solr-power). The Patchstack team will assist you with verification, CVE assignment, and notify the developers of this plugin.

== Changelog ==

= 2.5.2 (September 12, 2023) =
* Fix incompatibility with Object Cache Pro when running "wp solr index" [[#611](https://github.com/pantheon-systems/solr-power/pull/611)]

= 2.5.1 =
* Fix Solr not indexing automatically [[#598](https://github.com/pantheon-systems/solr-power/pull/598)]
* Fixed issue with empty/zero value for posts_per_page [[#599](https://github.com/pantheon-systems/solr-power/pull/599)] (props @threeiem)

= 2.5.0 =
* Updates CONTRIBUTING.md [[#585](https://github.com/pantheon-systems/solr-power/pull/585)] [[#594](https://github.com/pantheon-systems/solr-power/pull/594)]
* Fixes typo in var which caused undefined notice [[#582](https://github.com/pantheon-systems/solr-power/pull/582)]
* Update Composer dependencies [[#576](https://github.com/pantheon-systems/solr-power/pull/576)] [[#574](https://github.com/pantheon-systems/solr-power/pull/583)] [[#573](https://github.com/pantheon-systems/solr-power/pull/584)]
* Updates security policy [[#589](https://github.com/pantheon-systems/solr-power/pull/589)]
* Disable auto-commit by default. [[#591](https://github.com/pantheon-systems/solr-power/pull/591)]

= 2.4.5 (April 9, 2023) =
* Fixes missing vendor/ directory in previous release [[#580](https://github.com/pantheon-systems/solr-power/pull/580)]

= 2.4.4 (April 7, 2023) =
* Update Composer dependencies [[#576](https://github.com/pantheon-systems/solr-power/pull/576)] [[#574](https://github.com/pantheon-systems/solr-power/pull/574)] [[#573](https://github.com/pantheon-systems/solr-power/pull/573)]
* Fix failing tests [[#577](https://github.com/pantheon-systems/solr-power/pull/577)]
* Update tested up to version

= 2.4.3 (January 19, 2022) =
* Include schema.xml in release distribution [[#568](https://github.com/pantheon-systems/solr-power/pull/568)]

= 2.4.2 (December 2, 2022) =
* Re-add changelog heading to readme.txt [[#564](https://github.com/pantheon-systems/solr-power/pull/564)]

= 2.4.1 (December 1, 2022) =
* Fixes the WordPress `readme.txt` [[#562](https://github.com/pantheon-systems/solr-power/pull/562/)]

= 2.4.0 (November 30, 2022) =
* Adds Github Actions for building tag and deploying to wp.org. Add CONTRIBUTING.md. [[#551](https://github.com/pantheon-systems/solr-power/issues/551)]
* Added SOLRPOWER_DISABLE_AUTOCOMMIT to disable autocommitting of posts, moved CHANGELOG to it's own file, added `$post->score` value to parsed search results [[#559](https://github.com/pantheon-systems/solr-power/pull/559)]

= 2.3.3 (September 28, 2022) =
* Fixes issue where options could not be saved [[#541](https://github.com/pantheon-systems/solr-power/issues/541)]
* Enforces network activation requirement for WordPress multisite [[#538](https://github.com/pantheon-systems/solr-power/issues/538)]

= 2.3.2 (April 1, 2022) =
* Fixes query filtering for `'fields' => 'id=>parent'` [[#528](https://github.com/pantheon-systems/solr-power/pull/528)].

= 2.3.1 (March 29, 2022) =
* Adapts `posts_pre_query()` return values based on 'fields' argument [[#522](https://github.com/pantheon-systems/solr-power/pull/522)].

= 2.3.0 (March 29, 2022) =
* Removes incorrect use of `array_map( 'get_post' )` in `posts_pre_query` [[#521](https://github.com/pantheon-systems/solr-power/pull/521)].

= 2.2.6 (February 22, 2022) =
* Fixes PHP 8 deprecations in `class-solrpower-options.php` [[#513](https://github.com/pantheon-systems/solr-power/pull/513)].

= 2.2.5 (July 27, 2021) =
* Switches to `wp_strip_all_tags()` to remove style and script tag content [[#500](https://github.com/pantheon-systems/solr-power/pull/500)].

= 2.2.4 (May 5, 2021) =
* Introduces `SOLRPOWER_DISABLE_QUERY_ALT` constant for disabling setQueryAlternative behavior [[#495](https://github.com/pantheon-systems/solr-power/pull/495)].

= 2.2.3 (March 8, 2021) =
* Incorporates the value for `$_ENV['FILEMOUNT']` when indicating path for `schema.xml` [[#492](https://github.com/pantheon-systems/solr-power/pull/492)].

= 2.2.2 (December 1, 2020) =
* Updates various Composer dependencies [[#477](https://github.com/pantheon-systems/solr-power/pull/477)].
* Updates README to include detail on how to use TrieDateField for publish date [[#466](https://github.com/pantheon-systems/solr-power/pull/466)].

= 2.2.1 (July 13, 2020) =
* Avoids pinging Solr unless we actually need, to avoid unnecessary requests [[#458](https://github.com/pantheon-systems/solr-power/pull/458)].

= 2.2.0 (May 5, 2020) =
* Uses `posts_pre_query` hook to support use of 'fields' in `WP_Query` [[#448](https://github.com/pantheon-systems/solr-power/pull/448)].

= 2.1.4 (April 24, 2020) =
* Ensures highlighting is also applied to the post excerpt [[#446](https://github.com/pantheon-systems/solr-power/pull/446)].

= 2.1.3 (November 16, 2019) =
* Add `solr_power_ajax_search_query_args` filter to modify AJAX search query arguments [[#432](https://github.com/pantheon-systems/solr-power/pull/432)].

= 2.1.2 (August 28, 2019) =
* Adds `solr_is_private_blog` filter to allow control over whether a blog is indexed [[#423](https://github.com/pantheon-systems/solr-power/pull/423)].

= 2.1.1 (August 14, 2019) =
* Uses some fancy `composer` magic to unblock WordPress.org plugin updates [[#418](https://github.com/pantheon-systems/solr-power/pull/418)].

= 2.1.0 (May 22, 2019) =
* Introduces `solr_index_stat` filter for allowing additional information to be included [[#396](https://github.com/pantheon-systems/solr-power/pull/396)].
* Introduces `solr_facet_operator` filter for allowing facet operator to be overridden [[#388](https://github.com/pantheon-systems/solr-power/pull/388)].
* Ensures warning message appears when activating across the entire network [[#399](https://github.com/pantheon-systems/solr-power/pull/399)].
* Parses `<h1>` tags in Solr error response, in addition to `<title>` [[#407](https://github.com/pantheon-systems/solr-power/pull/407)].
* Fixes incorrect variable name when outputting schema error message [[#404](https://github.com/pantheon-systems/solr-power/pull/404)].

= 2.0.0 =
* Fix PHP 7 warning caused by bad conditional
* Ensure `$post->post_author` remains user ID when processing WP_Query
* Add a test case asserting that `post_title` and `post_content` are not bolded
* Update Solarium to `4.1.0` and other dependencies updates as needed
* Run automated tests against PHP `7.1`
* Increase the minimum supported PHP version to `7.1`


= 1.5.0 =
* Adds support for queries using `post__in` and `post__not_in`.
* Clears batch cache when entire index is deleted.
* CLI: Errors early when there are no posts to index.
* Update Composer dependencies

= 1.4.1 =
* Introduce `batch_size` argument for `wp solr index`
* Ensure custom taxonomies are included in widget facets
* Mention available Docker containers in README
* Properly handle negative integers when indexing and querying
* Increase precision of `test_wp_query_failed_ping` to avoid racy failures
* Catch exception when `$search->getData()` fails
* Remove unused global imports for $current_blog
* Properly escape dismax query strings
* POST actions to `admin.php` in network admin
* Define checked files in PHPCS config so `phpcs` can easily be run
* Remove unused global imports for $current_blog
* Define checked files in PHPCS config so `phpcs` can easily be run
* Rename PHPCS config to correct name

= 1.4.0 =
* Bumps minimum supported version to WordPress 4.6.
* Updates bundled Solarium library to 3.8.1.
* Fixes Solr queries using `orderby=>meta_value_num` [[#299](https://github.com/pantheon-systems/solr-power/pull/299)].
* Use `$_SERVER['HOME']` as a reliable way of finding the cert on Pantheon [[#314](https://github.com/pantheon-systems/solr-power/pull/314)].


= 1.3.0 =
* Add `.distignore` file for wp dist-archive solr-power
* Make Solr in the admin opt-in only using the `solr_allow_admin` filter
* Error early when `PANTHEON_ENVIRONMENT` isn't set
* Clarify error message when environment variables aren't set
* Mention copying `schema.xml` and supported Solr version in README
* Include original plugin attribution in the copyright notice
* Boost `post_title` more than `post_content` with sane boost values
* Add missing filter for custom fields
* Boost posts with matching titles to the top of results
* Remove duplicate options when initializing them
* Match author name in search results
* Bug fixes
* Adhere to WordPress coding standards in PHP files

= 1.2.0 =
* Add multisite support
* Respect all query vars when searching with `WP_Query`
* Display error from schema submit response when Solr includes one

= 1.1.0 =
* Introduce a new class for managing batch indexes
* Rewrite `wp solr index` to provide more verbosity
* Make batch indexes resumeable by logging `paged` to an option
* Remove old `wp solr index` code
* Fire `solr_power_index_all_finished` action when indexing is complete
* Ensure a completion message displays after indexing is complete
* Fix a bug around Solr taking over searches in wp-admin
* Properly apply the ```solr_allow_admin``` and ```solr_allow_ajax``` filters
* Add ```solr_boost_query``` filter for boosted items
* Add ```solr_dismax_query``` filter for Dismax
* Add ```get_post_types``` and ```get_post_statuses``` helper methods
* Remove unnecessary ```get_env``` call
* Add ```solr_post_status``` filter
* Add missing ```solr_post_types``` filter to ```get_post_types``` call
* Use ```PANTHEON_INDEX_PORT``` instead of hard-coded port in curl

= 1.0.0 =
* Add Ajax functionality to the facet search widget
* Add date_query support to WP_Query Integration
* Allow ```s``` parameter for WP_Query when Solr is enabled
* Checks for searchable post type before indexing modified post
* Test with WordPress 4.7
* Add ```solr_power_index_all_finished``` action when indexing all posts is complete
* Allow post_title and post_content to score higher
* Make sure that integers and float values are actually of that type. Otherwise, Solr will fail to index the document.

= 0.6.0 =
* Advanced WP_Query Integration - Meta Queries, Tax Queries
* Translatable strings standardized
* Facet query fixes
* Hide schema submit option if not on the Pantheon platform
* Added a method for API status
* Document available filters
* Fixed single quote/character issues in the facet widget

= 0.5.0 =
* Add facet search widget
* Update options page internals to utilize WordPress settings API
* Add Behat tests to ensure the plugin's compatibility with the Pantheon platform.
* Defork Solarium and update it to version 3.6.0

= 0.4.1 =
* Do not allow plugin activation if the `PANTHEON_INDEX_HOST` or `PANTHEON_INDEX_PORT` environment variables are not set. Instead, show an admin notice to the user advising them to configure the environment variables.

= 0.4 =
* Auto submission of schema.xml
* Moved legacy functions to a separate file
* PHP version check - warn in the WordPress dashboard and disable Solr Power plugin if the PHP version is less than 5.4

= 0.3 =
* Bug fixes
* Settings page updates
* Filters for AJAX/Admin integration
* Indexing all publicly queryable post types
* Debug Bar Extension
* Default sort option on settings page
* Initial WP CLI integration

= 0.2 =
* Works "out of the box" by overriding WP_Query()
* Much improved internal factoring

= 0.1 =
* Initial alpha release (GitHub only)

= 0.0 =
* Note this started as a fork of this wonderful project: https://github.com/mattweber/solr-for-wordpress

== Upgrade Notice ==

= 2.5.0 =
Changes the default auto-commit behavior to disabled. See [the README](https://github.com/pantheon-systems/solr-power/#explicit-commit-vs-autocommit) for instructions for keeping enabled.