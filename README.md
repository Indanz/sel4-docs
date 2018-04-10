# seL4 Documentation site.

Currently WIP

These are the sources for the seL4 Documentation site located at <https://docs.sel4.systems>.
It is for cooperatively developing and sharing documentation on [seL4](https://sel4.systems).

See [CONTRIBUTING.md](DocsContributing.md) for information on how to contribute. _TL;DR click edit on the page in GitHub, make your changes and then submit a pull request._

Ask on the [mailing list][mailing-list] or open an issue if something doesn't make sense.

We've tried to make sure the hosted site is [WCAG 2.0 AA](https://www.w3.org/TR/WCAG20/) compliant.  Please let us know if we have missed something.  [This is how we test it.](#wcag-2.0-aa-conformance)

[mailing-list]: https://sel4.systems/lists/listinfo/

## Diagnosing a problem

Our documentation is contained in a collection of Markdown files stored in this repository.
We use [Jekyll](https://jekyllrb.com/) to generate a static html website that is then hosted on GitHub pages.
Our continuous integration is configured to regenerate and update the live site whenever a pull request is merged.
There is a timestamp in the source that indicates when it was last generated.  Additionally, each page has a timestamp and revision hash from when it was last updated located in the footer based on the git history.


Our markdown pages are rendered using [Kramdown](https://kramdown.gettalong.org/), a ruby based markdown converter that is configured
to interpret the markdown files (.md) as GitHub flavoured markdown (GFM).  We are currently using the `jekyll-theme-bootstrap` theme
that provides a sass implementation of bootstrap.  Our custom styling is contained in `assets/css/style.scss`.  `HTML` templates are in either \_layouts or \_includes.

To build and host locally:
```
git submodule init
git submodule update

# If this doesn't work see: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/
bundle install

bundle exec jekyll serve
#   Server address: http://127.0.0.1:4000/
#   Server running... press ctrl-c to stop.
```
Or with docker: 
```
docker run --network=host -v $PWD:/host -w /host -it ruby bash -c 'bundle install && bundle exec jekyll serve'
```

### Makefile and JEKYLL_ENV=production version

Jekyll provides an environment flag for providing some content only in a production environment.

One way we use this is to show data generated by rules in the Makefile and saved in \_data/generated.yml.
If you want to serve the site locally in production mode there are make rules for this.  You will need
to call the make rule to generate the \_data/generated.yml also.

## Quick compliance, style and formatting checks

### WCAG 2.0 AA conformance

There is a make rule to check conformance to all testable statements from the guidelines. `make check_conformance`.
It requires the site to be hosted locally (using `make serve`) and a local server of
[Automated Accessibility Testing Tool (AATT)](https://github.com/paypal/AATT).

`make check_conformance` will output a file named `conformance_results.xml` which is a junit testsuite output file that will
contain a testcase for each generated html file of the site.  A make rule `make check_conformance_errors` will grep for failing testcases
and output the html page name.  The idea here is to detect if any pages are failing and then manually using the AATT tool's webinterface
to check what parts of the page violate the guidelines.

### Linters

Linting checks require the linters to be installed.

- HTML output checking using `tidy`: `make check_html_output`
- Liquid syntax checking using `liquid-linter`: `make check_liquid_syntax`
