- Suchar - zadania, które długo wiszą
- Backlog - do zaplanowania, że będzie zrobione
- UP - Users Profile
- PV - Page View
- CTR - Clicks Through Rate
- Referer - strona, z której zostałem przeniesiony na inną stronę
- Dot - historycznie kiedyś na stronach aby mierzyć ruch nie zamieszczano kody JS, lecz piksel.
- TTL - Time to Live,  czyli czas jaki na SGWP będzie istniała zajawka. Czasy te są ustalane odgórnie, w zależności od sekcji (tutaj można znaleźć te czasy MaxTeasersLifetime w sekundach: [https://git.dcwp.pl/sg-wp-pl/stream-api/-/blob/master/app/config_categories.go](https://git.dcwp.pl/sg-wp-pl/stream-api/-/blob/master/app/config_categories.go))
- Device atlas - baza służąca do identyfikacji urządzeń użytkowników,
- Zeus - zanim istniała pomoc.grupawp.pl, to na tym zlecało się tickety.
- Aby ściągać folder z JupyterLaba należy spakować go do TAR: 
```
tar -cvf zapakowany.tar ścieżka/
```
- Odpakowanie pliku TAR:
```
tar -xvf nazwa_pliku.tar
```
- Indeks administracji: [http://back-1.cr.dc-2.tools.dcwp.pl:9200/adm_wpteasers/wpteaser/_search](http://back-1.cr.dc-2.tools.dcwp.pl:9200/adm_wpteasers/wpteaser/_search)
- Połączenie nieprywatne w Chrome [https://medium.com/@dblazeski/chrome-bypass-net-err-cert-invalid-for-development-daefae43eb12](https://medium.com/@dblazeski/chrome-bypass-net-err-cert-invalid-for-development-daefae43eb12)
- Mapowanie kategorii (numer: Nazwa) [https://git.dcwp.pl/ZDS/teaser_heatmap/-/blob/master/src/category_mapping.py](https://git.dcwp.pl/ZDS/teaser_heatmap/-/blob/master/src/category_mapping.py)
- Janusz - przeciętny użytkownik, który klika tylko w to co się pojawia na górze
- WP Live - sekcja o numerze 200, inaczej Wiadomości. Jest wystawiana przy ważnych wydarzeniach, a decyduje o tym fakcie ktoś z Publishingu. Być może ta sekcja zostanie na stałe (przez to, że są tam sprzedane reklamy na dłuższy czas)
- aby sprawdzić wersje zainstalowanych pakietów na JupyterLabie należy wpisać: !python3 -m pip list | grep pakiet1 || pakiet2 ...
- Aby w Jira przenieść Task z jednego projektu do innego należy kliknąć More -> Move
- MR - merge request
- Aby regulować wielkość obrazka w JupyterLabie mogę zastosować kod:
```
HTML('<img src="picture.svg" style="width:40%;float:left;" / >’)
```
* wpjslib - nasza główna biblioteka z funkcjami wpjsliba do używania na wszystkich serwisach (np funkcje do logowania dot'a)
- Kiedy widzę jakiś błąd na stronie głównej, to mogę zgłaszać na [widzeblad@grupawp.pl](mailto:widzeblad@grupawp.pl)
- Mapowanie nazw na Swiv [https://git.dcwp.pl/admini/swiv/-/tree/master/dataCubesWaw](https://git.dcwp.pl/admini/swiv/-/tree/master/dataCubesWaw)
- proxies, proxy
```
proxies = {
"http" :"http://w3cache.adm.wp-sa.pl:8080",
"https":"http://w3cache.adm.wp-sa.pl:8080"
}
```
* aby zainstalować pakiet pythona na JupyterLab należy w terminalu wpisać:
```
pip install --proxy "http://w3cache.adm.wp-sa.pl:8080" --user pakiet
```
* aby ustawić proxy jako zmienne środowiskowe
```
os.environ["HTTP_PROXY"] = "http://w3cache.adm.wp-sa.pl:8080/"
os.environ["HTTPS_PROXY"] = "http://w3cache.adm.wp-sa.pl:8080/"
```
* aby ubić aplikację na YARN, należy w terminalu JupyterLaba uwierzytelnić się kinit dgiemza oraz podać:
```
yarn application -kill application_<identyfikator_joba>
```
* Adres Yarna (resource managera): [http://rm-2.hadoop.dc-2.batchlayer.dcwp.pl:8088/cluster](http://rm-2.hadoop.dc-2.batchlayer.dcwp.pl:8088/cluster)
* artykuły na temat Machine Learning [https://distill.pub/](https://distill.pub/)
* zmiana hasła do systemów WP [https://pass.grupawp.pl/](https://pass.grupawp.pl/) ; do LDAP [https://ipa.grupawp.pl/ipa/ui/](https://ipa.grupawp.pl/ipa/ui/)
* endpoint z aktualnym statidem [https://www.wp.pl/v1/statid](https://www.wp.pl/v1/statid)
* usuwanie danych z hdfs:
```
path = "user/hive/warehouse/prj_bdc.db/tabela"
os.system(f'hdfs dfs -rm -R {path}')
```
* odpytywanie o konfigurację use-tfs [http://config.zds-tfs.model-serving.ingress.gpu.dcwp.pl/config/batching](http://config.zds-tfs.model-serving.ingress.gpu.dcwp.pl/config/batching)
* otwarte sesje na rserverze [https://rserver.dc-2.tools.dcwp.pl:8889/api/sessions](https://rserver.dc-2.tools.dcwp.pl:8889/api/sessions)
* sprawdzanie zajętości swap na rserverze to komenda htop
- sprawdzanie jakie notebooki na rserverze zajmują najwięcej pamięci (oraz czyszczenie poprzedniego wyniku) 
```
clear && ps aux --sort=-%mem | head
```
- zmiana usera i usergrupy na pyuser (na Polyaxonie); -R to rekursywnie dla całego folderu
```
chown -R 1000:1000 nazwa_pliku
```
- słownik pojęć Hub Analityczny https://confluence.grupawp.pl/pages/viewpage.action?pageId=46222730
- aktualne certyfikaty do ściągania obrazów z common.dockerhub https://confluence.grupawp.pl/display/OPP/Docker+a+certyfikat+WPH
- Logowanie spotkań zespołu: [https://jira.grupawp.pl/browse/BDRDDEP-41](https://jira.grupawp.pl/browse/BDRDDEP-41 "https://jira.grupawp.pl/browse/bdrddep-41")
- Logowanie czasu szkoleń: [https://jira.grupawp.pl/browse/BDRDDEP-100](https://jira.grupawp.pl/browse/BDRDDEP-100 "https://jira.grupawp.pl/browse/bdrddep-100")
- Zmiana hasła na poczcie https://adself.grupawp.pl/authorization.do
* kopiowanie danych z r-servera na lokalny dysk
```
scp dgiemza@rserver.dc-2.tools.dcwp.pl:/var/wpusers/bibd/dags_volumes/bots_detection_hourly/ml1_final.csv /Users/dgiemza/Workspace/other/ml1_final.csv
```
* logowanie czasu spotkań Ćmy https://jira.grupawp.pl/browse/PCMA-1
* Urlopy ĆMA https://confluence.grupawp.pl/display/CMA/Urlopy+2025
* Dane o mnie https://www.wp.pl/v1/debug/profile
* Zajawki z grida (to co może zobaczyć użytkownik) https://www.wp.pl/v1/data
* CIDy są dostępne w wp_pool_ccc_orc oraz https://sgwppl-backend-1-streamapi-http.nginx.wp.dc-2.lb.dcwp.pl/v1/bigdata/active-pool
* 