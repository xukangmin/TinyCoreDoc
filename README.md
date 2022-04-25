# TinyCoreDoc

### Install dependencies

> **Recommended but not required:**
> `python3 -m venv venv`
> `source venv/bin/activate`

**Required:**
`pip3 install -r requirements.txt`

### Build html

`make html`

### Recommendations

You can run a local server to view the html version of the docs via:

```
$> cd build
$> python3 -m http.server
```

Make sure you know your local IP address then go to:

`http://YOURLOCALIPADDRESS:8000`

### Other
See `make` for other output formats.
