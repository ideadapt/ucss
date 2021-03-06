## uCSS (v0.1.1-beta)
Find unused CSS selectors, as well as duplicate selectors. Also, you can get an overview of how many matches there are for each rule in your markup.

Key features:
* Find unused CSS selectors in a HTML code base.
* See how many times each CSS selector has been used.
* Find duplicate CSS selectors.

But wait, there's more! By setting up a config file, uCSS can also:
* Visit several URLs/HTML files in one go.
* Use several CSS files at once.
* Do login, and visit pages both as a logged in and logged out user.
* Whitelist CSS rules that should be ignored.

uCSS is written for Node (http://www.nodejs.org/). It can be used both as a library and as a command line tool. With a little tweaking, it should also be easy to use it in other contexts as well.

Please note: uCSS is currently in beta.

### Installation

`npm install ucss`

### Usage (command line)

```
$ ucss --help
Usage: ucss [OPTION]...

Options:
  --help            This help text.                                         
  --html, -h        HTML to load.                                           
  --css, -c         CSS to load.                                            
  --config, -g      Config file to use.                                       [default: true]
  --used, -u        Show numbers on used rules, in addition to unused rules.  [default: false]
  --nosummary, -n   Output summary.                                           [default: false]
  --duplicates, -d  Show duplicates.                                          [default: false]

Either a config file, or HTML and CSS files are required. If no arguments are specified, uCSS
will look for a ucss.json file in the current directory.
```
So, to check a web page you could write
```
$ ucss -h http://example.com/foo.html -c foo.css
```
Note that the CSS file has to be stored locally (for the time being). To check multiple pages, and also output duplicates as well as all used and unused rules, you can do
```
$ ucss -d -u -h http://example.com/foo.html -h http://example.com/bar.html -c foo.css
```
To use a config file ("ucss.json") that you have created in the current folder, simply run
```
$ ucss
```
or specify another file name using the -g option. For more info on the config file, see below.

### Usage (as library)

```
var css = ".foo {} .bar {} .baz {}";
var html = "<html><head></head><body class='foo'></body></html>";
var whitelist = [".baz"];
var auth = null;
ucss.analyze(css, html, whitelist, auth, function(result) {
    require('../lib/helpers/output').standard(
        result, false, false, false);
    };);
```

### Setting up a config file
uCSS allows multiple HTML files/URLs as argument, but it soon gets tiresome to write (and remember) them all. By creating a small config file, you can set up uCSS to automatically visit a set of URLs when you run it.

In addition to this, you can also specify a function that uCSS can use for login. uCSS will then visit each of the URLs in your config file both as a logged in and logged out user.

Furthermore, you can create a white list of selectors that should be ignored. This is useful if you have e.g. classes that are toggled by JavaScript and thus might not be visible when uCSS visits the page, or if you have special styling for various error situations that is tricky to trigger.

As well as using several html files, uCSS can also combine CSS from several files, which can also be specified in your config file.

If you name your config file "ucss.json", it will automatically be picked up by ucss. You can also name your config file something else, and use the -g option to point to it.

Please see the [example config file](https://github.com/operasoftware/ucss/blob/master/examples/ucss.json). If you want to write a custom login function, see below.

#### Logging in
Login requires you to set up a config file. In the config file, you can specify your own login function:

```
{
    ...,
    auth: {
        "username": "foo",
        "password": "bar",
        "loginUrl": "http://example.com/login/",
        "loginFunc": function(url, username, password, callback) {
            // Do login, get cookie
            var cookie = "sessionid:1234"
            callback(cookie);
        }
   },
   ...
}

```
...or you can just specify a login helper:

```
    "loginFunc": "djangoLogin"
```
There is currently only one login helper available, for Django.