
**Site-crawler**
Repo [https://git.dcwp.pl/operations/crawler](https://git.dcwp.pl/operations/crawler "https://git.dcwp.pl/operations/crawler")
Request z nadaniem uprawnień https://jira.grupawp.pl/servicedesk/customer/portal/3/SERVICE-90924

Budowanie obrazu
```
docker build -t crawler-sync-renderer:latest -f Dockerfile.sync-renderer .
```

.env_docker
```
LISTEN=:8888 
BROWSER_TIMEOUT=60s
AD_BLOCK_USE=true
CHROME_DISABLE_IMAGES=true
LOG_LEVEL=info
WORKERS=2
GIN_MODE=release
WORKER_RENDERER=chromium
CHROME_SET_MAX_CACHE_SIZE=true
CHROME_MAX_CACHE_SIZE=200000000
CHROME_GC_RETENTION_TIME=30s
PROXY_LIST=
```

uruchamianie kontenera
```
docker run --env-file=./.env_docker -p 8888:8888 crawler-sync-renderer:latest
```

Ale to nie działa na MacBooku (powinno na Linuxie)

Synchroniczny (może obsługiwać URLe szeregowo) można odpytywać curlem
```
curl --location 'http://site-crawler-sync-renderer-chromium-ds-app-http.nginx.hosting.dc-2.lb.dcwp.pl/api/render_html?url=https%3A%2F%2Fwww.newsweek.pl%2Fpolska%2Fpolityka%2Fgrzegorz-braun-i-kontakty-z-ludzmi-rosyjskich-sluzb-to-trzeba-przeswietlic%2Frsjwl73&proxy=http%3A%2F%2Fproxytoext-1.hosting.dc-2.srv.dcwp.pl%3A3129&wait=2'
```
funkcja w Pythonie
```
def extract_html(url):
	if 'https://' not in url and 'http://' not in url:
		url = 'https://' + url
	
	params = {
	'url': url,
	'proxy': 'http://proxytoext-1.hosting.dc-2.srv.dcwp.pl:3129',
	'wait': '2',
	}
	
	response = requests.get('http://site-crawler-sync-renderer-chromium-ds-app-http.nginx.hosting.dc-2.lb.dcwp.pl/api/render_html', params=params,)
	try:
		return response.json()['html']
	except:
		return None
```

parametr wait oznacza ile daje się sekund na załadowanie js'ów na domenie

**Article-extractor**

Repo: [https://git.dcwp.pl/pixel/go-article-extractor](https://git.dcwp.pl/pixel/go-article-extractor "https://git.dcwp.pl/pixel/go-article-extractor")

.env_docker
```
LOG_LEVEL=info
WORKERS=60
EXTRACTOR_ORDER=custom,trafilatura,goose
TLD_BLACKLIST=
CRAWLER_RENDERER_ADDR=http://site-crawler-sync-renderer-chromium-ds-app-http.nginx.hosting.dc-2.lb.dcwp.pl/api/render_html
CRAWLER_TIMEOUT=15s
LISTEN=:9999
GIN_MODE=release
```

Należy korzystać z tego brancha
```
git pull origin dirty_sync_renderer
git switch dirty_sync_renderer
```

budowanie obrazu i uruchamianie kontenera
```
docker build -t article-extractor:latest .   
```
```
export DOCKER_DEFAULT_PLATFORM=linux/amd64
```
```
docker run --env-file=./.env_docker -p 9999:9999 article-extractor:latest
```


funkcja w Pythonie
```
def extract_text(url):
	if 'https://' not in url and 'http://' not in url:
		url = 'https://' + url
		
	headers = {'Content-Type': 'application/json',}
	json_data = {'url': url,
	'sender': 'embedding-contextual',
	}
	response = requests.post('http://localhost:9999/v2/url', headers=headers, json=json_data)
	try:
		return response.json()['text']
	except:
		return None
```


