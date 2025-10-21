Nazywanie terminala
```
echo -e "\033]0;dgiemza\007"
```

echo -e "\033]0;processor\007"

Logi na grafanie
https://grafana.monitoring.k8s-gpu.dc-2.dcwp.pl/a/grafana-lokiexplore-app/explore/namespace/moth-platform/logs?var-ds=ddz4jzfiqlo8wd&from=now-15m&to=now&var-filters=namespace%7C%3D%7C__CV%CE%A9__moth-platform,moth-platform&patterns=%5B%5D&var-fields=pod%7C%3D%7C%7B%22value%22:%22small-moth-lin-ucb-processor-11%22__gfc__%22parser%22:%22mixed%22%7D,small-moth-lin-ucb-processor-11&var-levels=&var-metadata=&var-patterns=&var-lineFilterV2=&urlColumns=%5B%5D&visualizationType=%22logs%22&displayedFields=%5B%5D&var-labelBy=$__all&var-lineFilters=&timezone=browser&var-all-fields=pod%7C%3D%7C%7B%22value%22:%22small-moth-lin-ucb-processor-11%22__gfc__%22parser%22:%22mixed%22%7D,small-moth-lin-ucb-processor-11&sortOrder=%22Ascending%22&wrapLogMessage=false&refresh=10s

Repo
[https://git.dcwp.pl/ZDS/lin-ucb-argo](https://git.dcwp.pl/ZDS/lin-ucb-argo "https://git.dcwp.pl/zds/lin-ucb-argo")

Aplikacja na Argo CD
https://argocd-gpu.grupawp.pl/applications/argocd/moth-lin-ucb-argo?resource=

JupyterLab (należy uważać, gdyż procesy są współdzielone)
http://lin-ucb-argo-jlab.moth-platform.k8s-gpu.dc-2.dcwp.pl/lab/workspaces/dgiemza

Grafana
[http://grafana.monitoring.k8s-gpu.dc-2.dcwp.pl/goto/FW9nUq5Hg?orgId=1](http://grafana.monitoring.k8s-gpu.dc-2.dcwp.pl/goto/FW9nUq5Hg?orgId=1 "http://grafana.monitoring.k8s-gpu.dc-2.dcwp.pl/goto/fw9nuq5hg?orgid=1")
Wykresy są budowane na tym pliku tekstowym lin-ucb-metrics.zds-online.k8s-gpu.dc-2.dcwp.pl/metrics/
Definicja wysyłania Argo metryk za pomocą prometeusza (argocd/resources/servicemonitor.yaml) oraz kod apki (metrics-app/metrics-app.py)

Główny obraz to Dockerfile_worker, na którym budowane są inne obrazy. Przez to, że jest on budowany sporadycznie, zmniejsza się ryzyko konfliktów i złych wersji pakietów.

Pliki downloader, processor i deployer nie są ze sobą zesynchronizowane.

Monitoring Triton
http://grafana.monitoring.k8s-gpu.dc-2.dcwp.pl/d/aearjw3sfa6tcf/nvidia-triton-inference-server-models?orgId=1&from=now-5m&to=now&timezone=browser&var-ds=PBFA97CFB590B2093&var-workload=big-moth-inference-service-prod-http-svc&var-models=linucb_desktop&var-models=linucb_mobile&var-models=linucb_ucb_desktop&var-models=linucb_ucb_mobile

Big moth serving inference
https://argocd-gpu.grupawp.pl/applications/argocd/big-moth-serving-infrastructure?resource=

Small moth serving inference
https://argocd-gpu.grupawp.pl/applications/argocd/small-moth-serving-infrastructure?resource=

**Opis aplikacji i jej elementów**
- W aplikacji działa JupyterLab. Przez to kwestia dostępów jest automatycznie powiązana pomiędzy elementami produkcyjnymi i devowymi (argocd/resources/deployment.yaml). Pod notebook podmontowane są zasoby z rservera oraz lin-ucb-volume, gdzie są dane produkcyjne i nie można ich ruszać. Jest tam folder linucb-sketch-v4, gdzie są 32 shardy dla modeli (desktop i mobile) oraz folder models, gdzie trafiają cząstkowe modele z każdego poda. W models każdy typ modelu ma swój własny folder.
- *linucb-app/deployer.py* - wystawia modele dla CEPH i stamtąd ładowane są do Tritone. Modele zapisują się w ten sposób, że są ich 4 wersje na s3 i przy kolejnym zapisie do modelu dodawany jest kolejny numer, a ten z najmniejszym jest usuwany. Tritone należy zawsze restartować, gdyż w inny sposób nie potrafi on pobrać modeli.
- *linucb-app/metrics.py* - wystawia metryki na Grafanę
- *linucb-app/downloader.py* - 4 pody, które pobierają dane ostatnich paru minut z Clickhouse'a (argocd/resources/ss_downloaders.yaml). Każdy z 4 podów odkłada dane w czterech lokalizacjach (jest 16 takich lokalizacji). To co finalnie ląduje na dysku mieści się w zmiennej data_chunks, który to jest słownikiem po kluczach: datetime oraz teaserid.
- *linucb-app/processor.py* - 16 podów (8 shardów * 2 sub_shardy) do przetwarzania danych i trenowania modeli (argocd/resources/ss_processors.yaml). Każdy z tych podów wystawia modele w lokalnej ścieżce, a następnie deployer wystawia te modele na Tritone. Dla desktopa i mobile trenowane są osobne modele w pętli while = True, ale muszą się wykonywać co najmniej 30 sekund. Processing i modeling zajmują najdłuższe części czasu, gdzie preprocessing wykonuje się jednokrotnie dla wszystkich modeli. Procesory wczytują dane z pamięci ephemeral
- linucb-app/libs/triton.py - requesty do Tritone
- linucb-app/libs/loaders.py - query SQL i łączenie się z clickhousem
- .gitlab-ci.yml - wszystkie buildy muszą być wyzwolone ręcznie



**Modelowanie**

Aby dodać nowy model, należy: kopiować plik w linucb-app/libs/models/weighted_clicks.py i przekształcenie go. Następnie w init.py należy dodać ten model - wtedy model pojawia się w obiekcie model_engines. Wtedy model automatycznie zostaje przeliczany i zapisywany w odpowiednim folderze na linucb-sketch-v4.

W linucb-app/libs/models/base.py jest logika trenowania modeli, logowania czasu działania (dekorator timing)

Oprócz tego taki model musi predykować. Mój model będzie musiał dostać na wejściu sketch i geo. Należy w tym celu przygotować torch skrypt (linucb-app/libs/models/torch_modules.py). Prawdopodobnie będę musiał wprowadzić zmianę w metodzie forward, tak by X zawierał nie tylko sketche, ale też geo.
One-hot encodowanie geoRegion powinno się odbywać na końcu.

Model znajduje się w linucb-app/libs/models/utils.py w funkcji ridge_numpy_solve


## **Jak mam pracować z aplikacją?**

1. Dewelopowanie zmian w notatniku
2. Zaciągnięcie repo w notatniku i odpalenie odpowiedniego modułu dewelopersko
3. Zmiany w kodzie (na osobnym branchu) - należy pamiętać o dodaniu modelu do linucb-app/libs/models/__init__.py
4. Merge Request i możliwy rebase oraz usuwanie konfliktów
5. Budowanie odpowiedniego obrazu na repo [https://git.dcwp.pl/ZDS/lin-ucb-argo](https://git.dcwp.pl/ZDS/lin-ucb-argo "https://git.dcwp.pl/zds/lin-ucb-argo")
6. Refresh, i restart odpowiedniego modułu na Argo CD [https://argocd-gpu.grupawp.pl/applications/argocd/lin-ucb-argo?conditions=false&resource=](https://argocd-gpu.grupawp.pl/applications/argocd/lin-ucb-argo?conditions=false&resource= "https://argocd-gpu.grupawp.pl/applications/argocd/lin-ucb-argo?conditions=false&resource=")
7. Weryfikacja, czy pojawiają się odpowiednie dane, wytrenowane modele, modele na s3
8. Przy nowym modelu należy dodać jego konfigurację na https://git.dcwp.pl/ZDS/moth-inference-server, gdzie należy opracować jaki będzie input z innych modeli (można kopiować z innych plików .config.pbtxt). Zrobić to na nowym branchu i poprosić Julię o approval. Jacek zajmuje się odpytywaniem Tritona, więc wszelkie zmiany w inpucie muszą być konsultowane z nim.
9. Wysłać zapytanie testowe do Triton:![[request.sh]]
10. Jeśli wartości są w porządku (nie ma zer), to można uruchomić warianty testów AB na https://ab-tests-service-abtests-http.nginx.services.dc-2.lb.dcwp.pl/ui/
			Przykład konfiguracji testu
```
{
  "tritonModelInputs" : [ "USER_VECTOR", "GEO" ],
  "authority" : "analytics",
  "tritonModelName" : "linucb_ucb_geo_desktop",
  "tritonEnabled" : true,
  "taskId" : "PCMA-542",
  "allowPinning" : true
}
```
Ustawić end date daleko w przyszłości

Sprawdzać wyniki na Clickhouse
[https://metabase-mc.grupawp.pl/question/4511-big-moth-wyniki-linucb-geo](https://metabase-mc.grupawp.pl/question/4511-big-moth-wyniki-linucb-geo "https://metabase-mc.grupawp.pl/question/4511-big-moth-wyniki-linucb-geo")


Debugowanie tritona
https://www.wp.pl/v1/debug/triton?_test=linucb_geo_a
