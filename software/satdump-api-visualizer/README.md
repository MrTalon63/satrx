# Satdump autotrack API Visualizer

Tl;dr is I got bored with spamming F5 to get latest data from the API, and the builtin html page has slow refresh rate and dosen't have all information presented, like SNR.
So I made this rather simple visualizer, it fetches data from API every 100ms (yes it's very suboptimal to request every 100ms, but I ain't smart enough to implement websockets in Satdump) and displays it in a more user friendly way, with some extra information like SNR, viterbi and deframer locks and a timestamp of the last update.

Tested only with Meteor LRPT because that's all I had on autotrack.

## Usage

Due to Satdump webserver having to CORS rules (at least afaik), you have to run also a reverse proxy, like nginx, to add CORS headers to the API.

Here is an example nginx config for that:

````nginx
        server {
                listen 80;
                server_name _; # Replace with your domain or use "_" for a catch-all

                location / {
                        proxy_cache off;
                        add_header 'Access-Control-Allow-Origin' '*' always;
                        add_header 'Cache-Control' 'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0' a>
                        add_header 'Expires' '0' always;
                        add_header 'Pragma' 'no-cache' always;

                        proxy_pass http://127.0.0.1:8081;

                        proxy_set_header Host $host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        proxy_set_header X-Forwarded-Proto $scheme;
                }
        }
		```
````

You may also use Nginx to serve the visualizer itself, just put the `index.html` in your web root and access it via browser, then input the API URL (e.g. `http://localhost/satdump-api`) and it should start fetching data and displaying it.

And voila, you have a simple Satdump API visualizer ready to use!

## TODO

- Add support for other transmission, not just Meteor LRPT
- Add some graphs for SNR and other parameters, make them then downloadable as CSV or something?
- Implement websockets in Satdump and use them in the visualizer for real time updates without polling every 100ms. Would be banger feature, but I suck at C and the webserver in Satdump might be getting reworked anyway, so maybe not worth the effort.
- Make UI better? It's very basic right now and isn't that good looking, user friendly.
