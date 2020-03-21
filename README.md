# blazordemoci
![CI Github pages](https://github.com/vienpt/blazordemoci/workflows/CI%20Github%20pages/badge.svg)

![Azure pipelines build Status](https://dev.azure.com/viendev0909/blazor-ghpages/_build/latest?definitionId=9&branchName=master)


## Step by Step

**Step 1: Add `.nojekyll` file**

3 reason we need to understand why we should add .nojekyll to wwwroot content

- nojekyll empty file is required when publishing to GitHub Pages
- Jekyll will discard any files that begin with `_` (it will discard _framework/blazor-assembly.js)
- GitHub Pages are powered by Jekyll behind the scenes

**Step 2: Add `404.html` file**

Because by the default, Github-page doesn't support SPA (Single Page Application)

we need to add this code:

``` javascript
<script type="text/javascript">
	// Single Page Apps for GitHub Pages
	// https://github.com/rafrex/spa-github-pages
	// Copyright (c) 2016 Rafael Pedicini, licensed under the MIT License
	// ----------------------------------------------------------------------
	// This script takes the current url and converts the path and query
	// string into just a query string, and then redirects the browser
	// to the new url with only a query string and hash fragment,
	// e.g. http://www.foo.tld/one/two?a=b&c=d#qwe, becomes
	// http://www.foo.tld/?p=/one/two&q=a=b~and~c=d#qwe
	// Note: this 404.html file must be at least 512 bytes for it to work
	// with Internet Explorer (it is currently > 512 bytes)

	// If you're creating a Project Pages site and NOT using a custom domain,
	// then set segmentCount to 1 (enterprise users may need to set it to > 1).
	// This way the code will only replace the route part of the path, and not
	// the real directory in which the app resides, for example:
	// https://username.github.io/repo-name/one/two?a=b&c=d#qwe becomes
	// https://username.github.io/repo-name/?p=/one/two&q=a=b~and~c=d#qwe
	// Otherwise, leave segmentCount as 0.
	var segmentCount = 1; // NOT using a custom domain.
						  // Otherwise, set 0

	var l = window.location;
	l.replace(
		l.protocol + '//' + l.hostname + (l.port ? ':' + l.port : '') +
		l.pathname.split('/').slice(0, 1 + segmentCount).join('/') + '/?p=/' +
		l.pathname.slice(1).split('/').slice(segmentCount).join('/').replace(/&/g, '~and~') +
		(l.search ? '&q=' + l.search.slice(1).replace(/&/g, '~and~') : '') +
		l.hash
	);

	</script>

```

**Step 3: Add `index.html` file**

Need to add this code below for run SPA.

Set code between `apploading` and `blazor-error-ui`

``` javascript
<!-- Start Single Page Apps for GitHub Pages  -->
<script type="text/javascript">
	// Single Page Apps for GitHub Pages
	// https://github.com/rafrex/spa-github-pages
	// Copyright (c) 2016 Rafael Pedicini, licensed under the MIT License
	// ----------------------------------------------------------------------
	// This script checks to see if a redirect is present in the query string
	// and converts it back into the correct url and adds it to the
	// browser's history using window.history.replaceState(...),
	// which won't cause the browser to attempt to load the new url.
	// When the single page app is loaded further down in this file,
	// the correct url will be waiting in the browser's history for
	// the single page app to route accordingly.
	(function(l) {
	if (l.search) {
		var q = {};
		l.search.slice(1).split('&').forEach(function(v) {
		var a = v.split('=');
		q[a[0]] = a.slice(1).join('=').replace(/~and~/g, '&');
		});
		if (q.p !== undefined) {
		window.history.replaceState(null, null,
			l.pathname.slice(0, -1) + (q.p || '') +
			(q.q ? ('?' + q.q) : '') +
			l.hash
		);
		}
	}
	}(window.location))
</script>
<!-- End Single Page Apps for GitHub Pages -->
```

**Step 4: Add Github CI**

root folder: .github/workflows/main.yml

``` yaml
name: CI Github pages	// set name CI

on: [push]			 	// set action event

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v1
    - name: Set up .NET Core
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.1.100'	// set up dotnet core version
    
    - name: Publish app
      run: dotnet publish -c Release -o build	// set up publish folder /build
    
    - name: Deploy to Github Pages
      uses: JamesIves/github-pages-deploy-action@releases/v3	// use this repos to deploy
      with:
        ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}	// set up access token from Github setting
        BASE_BRANCH: master
        BRANCH: gh-pages
        FOLDER: build/demoblazorci/dist		// folder generate file
        CLEAN: true
```
