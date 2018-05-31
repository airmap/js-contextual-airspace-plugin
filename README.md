![AirMap: The Airspace Platform for Developers](examples/header.png)

# AirMap's Mapbox GL Contextual Airspace Plugin

## Introduction

A control for [mapbox-gl-js](https://github.com/mapbox/mapbox-gl-js) Mapbox GL JS plugin to view
and interact with AirMap's Contextual Airspace Rules.

## Requirements

To use the AirMap Contextual Airspace Plugin, you must register as a developer and obtain an API key from the [AirMap Developer Portal](https://dashboard.airmap.io/developer).
Once your application has been created, simply copy the provided config JSON to provide to the Contextual Airspace Plugin.
You'll also need to register for a [Mapbox Access Token](https://www.mapbox.com/help/create-api-access-token/).

## Installation

### From AirMap's CDN

```html
<!-- Latest patch release -->
<script src="https://cdn.airmap.io/js/contextual-airspace/1.0.0/airmap.contextual-airspace-plugin.min.js"></script>

<!-- Latest minor release -->
<script src="https://cdn.airmap.io/js/contextual-airspace/v1.0/airmap.contextual-airspace-plugin.min.js"></script>
```

### From [npm](https://npmjs.org)

```sh
npm install airmap-contextual-airspace-plugin
```

> After installing the `airmap-contextual-airspace-plugin` module via npm or bower, you'll need bundle it up along with its dependencies
using a tool like [webpack](https://webpack.github.io/) or [browserify](https://browserify.org). If you don't have a
build process in place for managing dependencies, it is recommended that you use the module via the CDN.
[See below](#with-webpack) for instructions on using with webpack and browserify. If you install with bower and intend
to support ES5, you will also need to run your bundle through [babel](https://babeljs.io/).

## Usage

Here's an example of a minimal setup. See [Documentation](#documentation) for more details.

```html
<!doctype html>
<html>
    <head>
        <title>Mapbox-gl-js Ruleset Plugin</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
        <script src="https://cdn.airmap.io/js/contextual-airspace/v1.0/airmap.contextual-airspace-plugin.min.js" async=false defer=false></script>       
        <script src="https://api.tiles.mapbox.com/mapbox-gl-js/v0.44.0/mapbox-gl.js"></script>
        <link href="https://api.tiles.mapbox.com/mapbox-gl-js/v0.44.0/mapbox-gl.css" rel="stylesheet" />
        <style>
            body { margin: 0; padding: 0; }
            .map {
                position: absolute;
                width: 100%;
                height: 100%;
                top: 0;
                right: 0;
            }
        </style>
    </head>
    <body>
        <div id="map" class="map"></div>
        <script>
            const AIRMAP_API_KEY = localStorage.getItem('AIRMAP_API_KEY')
            const MAPBOX_ACCESS_TOKEN = localStorage.getItem('MAPBOX_ACCESS_TOKEN')
            if (AIRMAP_API_KEY && MAPBOX_ACCESS_TOKEN) {
                mapboxgl.accessToken = MAPBOX_ACCESS_TOKEN
                const map = new mapboxgl.Map({
                    container: 'map',
                    style: 'mapbox://styles/mapbox/streets-v8',
                    center: [-118.496475, 34.024212],
                    zoom: 13
                })
                const config = {
                    "airmap": {
                        "api_key": AIRMAP_API_KEY
                    },
                    "auth0": {
                        "client_id": "",
                        "callback_url": ""
                    },
                    "mapbox": {
                        "access_token": MAPBOX_ACCESS_TOKEN
                    }
                }
                const options = {
                    preferredRulesets: [
                        'usa_part_107',
                        'deu_rules_waiver'
                    ],
                    overrideRulesets: [
                        // 'usa_part_107'
                    ],
                    enableRecommendedRulesets: true,
                    theme: 'light'
                    /* refer to the docs for a comprehensive list of options */
                }
                const plugin = new AirMap.ContextualAirspacePlugin(config, options);
                map.addControl(plugin, 'top-left')

                // Example for how ruleset changes are surfaced to the consuming application.
                plugin.on('jurisdictionChange', (data) => console.log('jurisdictionChange', data))
                plugin.on('airspaceLayerClick', (data) => console.log('airspaceLayerClick', data))
                
                // Example for how the consuming app can call the plugin for jurisdictions or selected rulesets.
                setTimeout(() => {
                    console.log({
                        jurisdictions: plugin.getJurisdictions(),
                        selectedRulelsets: plugin.getSelectedRulesets()
                    })
                }, 5000)
            } else {
                console.error(
                    'Missing AIRMAP_API_KEY or MAPBOX_ACCESS_TOKEN. ' +
                    'These are required for developing locally.\n\n' +
                    'Please save these values in localStorage by entering the following in your browser console:\n\n' +
                    'localStorage.setItem(\'AIRMAP_API_KEY\', \'<your_key>\');\n' +
                    'localStorage.setItem(\'MAPBOX_ACCESS_TOKEN\', \'<your_token>\');\n\n'
                );
            }
        </script>
    </body>
</html>
```

Or if using from NPM:

```javascript
import ContextualAirspacePlugin from 'airmap-contextual-airspace-plugin'

const map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/streets-v8',
    center: [-118.4932, 34.0135],
    zoom: 13
})
const config = {
    "airmap": {
        "api_key": AIRMAP_API_KEY
    },
    "auth0": {
        "client_id": "",
        "callback_url": ""
    },
    "mapbox": {
        "access_token": MAPBOX_ACCESS_TOKEN
    }
}
const options = {
    preferredRulesets: [
        'usa_part_107',
        'deu_rules_waiver'
    ],
    overrideRulesets: [
        // 'usa_part_107'
    ],
    enableRecommendedRulesets: true,
    theme: 'light'
}
const plugin = new ContextualAirspacePlugin(config, options);
map.addControl(plugin, 'top-left')
```

### Prebuilt

If you are installing the Contextual Airspace Plugin with npm, a prebuilt package is also available in `dist/airmap.contextualairspaceplugin.min.js`. This will
allow you to use the Contextual Airspace Plugin without changing your webpack/browserify configuration. However, using your bundler to package
all dependencies is the preferred approach (webpack recommends against using prebuilt files).

## Documentation

> [Generated API Documentation](API.md)

> [Official AirMap Docs](https://developers.airmap.com/docs/js-getting-started)

### Developing

[Clone the repo](https://github.com/airmap/js-contextual-airspace-plugin) and run `npm install`. Then run `npm start` and navigate to
[http://localhost:8080/](http://localhost:8080/) in your browser. The server will
listen for changes and live reload as updates are made.

If this is your first time developing with the Contextual Airspace plugin, you'll need to store an AirMap API Key and Mapbox Access Token
in your localStorage for use on the `http://localhost:8080/` demo page:

```javascript
localStorage.setItem('AIRMAP_API_KEY', '<your_key>');
localStorage.setItem('MAPBOX_ACCESS_TOKEN', '<your_token>');
```

Once this is done, you won't need to do it again unless you clear your browser's localStorage.

```
$ npm install
$ npm start
$ open http://localhost:8080/
```

## License

> See [LICENSE](LICENSE.md) for details.

