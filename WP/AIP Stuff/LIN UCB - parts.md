Pobieranie danych - w downloader.py najpierw wybierany jest odpowiedni config, sprawdzana jest nazwa aplikacji zawarty w hostname. Jeśli jest inny niż lin-ucb-downloader, to aplikacja korzysta z host_num = 0, co oznacza deweloperskie korzystanie z aplikacji (np w środowisku JupyterLab).

Tworzenie folderów dla głównego katalogu aplikacji oraz podfolderów dla wszystkich shardów i sub_shardów.

pętla główna load_loop 
minimalny czas działania pętli to 30 sekund
num_batches_in_one_query określa ile batchy danych będzie ściągane 
	mins_start = batches * batch_mins + time_offset = 2 * 3  + 0= 6
	mins_end = time_offset-batch_mins = 0 - 3 = -3
	num_batches_in_one_query = int((config.mins_start - config.mins_end) / config.batch_mins) = (6 + 3)/3 = 3

times_to_include pochodzi z generate_times_in_range, gdzie end_time jest wielokrotnością batch_mins (..., 12, 15, 18, ...) przy zerowych wartościach second i microsecond. Zwracana jest lista wszystkich znaczników czasowych pomiędzy end_time i start_time co interwał batch_mins

Następnie iteracja po mobileview i sub_shardach (shar) zostaną pobrane dane z CH i utworzony chunk, w zależności od rodzaju ćmy. Chunki są zapisywane w folderach subchunków, w zależności od subshard_test
		


Co z DEBUG_MODE?
czy host_num pochodzi z nazwy poda?
Dlaczego w query odejmowana jest jedna sekunda?
Dlaczego dane są zapisywane w folderach subshardów? To optymalizacja? Jaka jest idea subchunkowania?





Processor 
Aplikacja może być odpalana w trybie ram_mode lub init_ephemeral (domyślnie ephemeral)
init_ram vs init_ephemeral:
rozumiem, że ram pobiera obiekty do pamięci poda, a ephemeral do ścieżki na /opt/
jakie główne różnice pomiędzy tymi metodami
czy init_ram już gdzieś działa
czy nie można również iterować po config.mobileviews
na ephemeral zapisuje się chunki danych aby był szybszy dostęp? po co, skoro files_path i dest_path oba są w folderze /opt/. Czy też init oznacza warm up przed korzystaniem z tych lokalizacji? czym właściwie charakteryzuje się lokalizacja data_folder i local_storage?
config.shard_sub_list[config.host_num] - na podstawie host_num wskazywany jest odpowiedni shard i sub_shard

dlaczego do tid_list dodaje się active_pool tid_list = tid_list & actp_tids ?


PoolBuilder


czym charakteryzuje się Redis

czy wersja devowa będzie tylko dla dużej ćmy, czy też robimy także dla small_moth?
ApiPoolBuilder - pobiera tidy z active_pool na backendzie


jak działa klasa ModelEngines - sprawdzić w gpt


trenowanie modeli:
iteracja po dostępnych modelach w model_engines

Modelowanie:
* tworzenie nowej instancji klasy za każdym razem trenowania danego modelu
- - preprocess ma za zadanie utworzyć obiekt w klasie parallel_inputs, który zawiera tuplet (tid, (X, y))
- train - do obiektu self.models zapisywane są modele trenowane za pomocą metody base_trainer równolegle
- base_trainer - jeśli clicki są powyżej config.min_size, to trenowanie się odbywa metodą ridge_numpy_solve, inaczej ctr_model, która zwraca zerowe wartości z wyjątkiem ostatniego elementu
- ridge_numpy_solve:
	- dostaje na wejsciu t (X), out (y)
	- A_inv powstaje w ten sposób: transpozycja t (inaczej t^T); mnożenie tej macierzy transponowanej z oryginalną; dodawana jest regularyzacja, czyli macierz jednostkowa o wymiarze shape[1] macierzy oryginalnej razy współczynnik regularyzacji; na końcu sumę tych dwóch macierzy odwraca się, czyli szuka macierzy, która pomnożona przez oryginalną macierz daje macierz jednostkową
- 




**Deployer**

dodać if __name__ == '__main__': na początku skryptu


Model mesh commit
https://git.dcwp.pl/ZDS/lin-ucb-argo/-/merge_requests/27/diffs#fb69d7ecd7bfb34035d2e276ec22d7ba2795c371


# **Konfiguracja Argo CD**

* **Kustomization**. Tworzenie configmapy za pomocą configMapGenerator z pliku /configs/.env.

* **Ingress**. Pozwala wystawiać na zewnątrz klastra aplikacje, które normalne są dostępne wyłącznie wewnątrz klastra kubernetesowego. ingress.yaml przekierowuje (routing) ruch z hostów na serwisy (przykładowo lin-ucb-argo-jlab.zds-online.k8s-gpu.dc-2.dcwp.pl na serwis lin-ucb-service-jlab); ingress_patch.yaml nadpisuje (op: replace) wartości hostów (path: /spec/rules/0/host) nową wartością (value: ...)
* **service_jlab**. Obiekt typu Service, o nazwie lin-ucb-service-jlab, który nasłuchuje na porcie 8888 (klastra kubernetesowego) i przekierowuje ruch wyłącznie na wewnętrzne (parametr ClusterIP) porty podów 8888, które mają nazwę lin-ucb-argo. W kontenerze o nazwie lin-ucb-argo na porcie 8888 działa jupyterLab (deployment.yaml).
Flow:
```text
Przeglądarka użytkownika
           ↓
Adres URL: lin-ucb-argo-jlab.zds-online.k8s-gpu.dc-2.dcwp.pl
           ↓
[Ingress "lin-ucb-argo-ingress"]
           ↓
[Service "lin-ucb-service-jlab" port: ui (8888)]
           ↓
[Pod pasujący do etykiety "app_name: lin-ucb-argo" (na port 8888)]
           ↓
Aplikacja działająca w Podzie przetwarza żądanie (w deployment.yaml jest jupyter na porcie 8888)
           ↓
Powrót tą samą drogą do użytkownika 
(pod → service → ingress → przeglądarka)
```
Ingress bez service nie ma sensu, gdyż ingress wystawia połączenie do service na zewnątrz klastra, a service kieruje wewnątrz klastra ten ruch na odpowiednie pody.

dlaczego od razu nie stosujemy moth-platform zamiast zds-online?
* **Downloader**. W ss_downloaders.yaml jest pole envFrom, gdzie wskazuje się wcześniej zbudowaną lub zdefiniowaną configMapę (configMapRef). Persistent Volume Claims (PVC) służą do przechowywania danych poza życiem kontenera lub poda. Ephemeral to tymczasowe wolumeny, które istnieją tylko podczas życia poda. claimName odnosi się do PVC zdefiniowanego w pliku pvc.yaml. mountPath to ścieżka wewnątrz kontenera, do której Kubernetes podpina wskazany wolumen. Można wtedy z niej czytać jak z lokalnej ścieżki open('opt/lin-ucb/plik.txt'). imagePullPolicy określa kiedy Kubernetes powinien sprawdzić, czy w repozytorium znajduje się nowsza wersja obrazu kontenera i czy powinien tą wersję pobrać (dostępne Always, IfNotPresent, Never). Jeśli chodzi o resouces, to requests oznacza gwarantowane minimalne zasoby, a limits oznacza maksymalne zasoby, po przekroczeniu których może nastąpić odmowa zapisu (ephemeral-storage) lub OOM Error (memory). CPU podaje się w wartościach zmiennoprzecinkowych (1.25) lub miliCPU (600m). Jeśli requests == limits, to zasoby mają największy priorytet (guaranteed quality of service).
po co w downloaderze podpina się volumen rserver?

* **Deployment aplikacji**. W pliku deployment.yaml znajdują się główne ustawienia aplikacji lin-ucb-argo, która odpala JupyerLab. Ma ona zdefiniowany port wewnętrzny ui 8888, na którym odpalany jest JupyterLab. Aplikacja będzie korzystała z ServiceAccount linucb-deployer (serviceAccountName: linucb-deployer)
Czy wszystkie zasoby w tym kontenerze są przypisane wyłącznie dla JupyterLaba? Co z tym service account?

* **deployer-service-account.yaml**. W tym pliku zdefiniowane są trzy zasoby Kubernetesowe:
	* ServiceAccount linucb-deployer : konto serwisowe z tokenem uwierzytelniającym, pozwalające na komunikację z Kubernetes API
	* Role linucb-deployer-role : jakie akcje może wykonywać konto serwisowe w obrębie danego namespace
	* RoleBinding linucb-deployer-rolebinding : wskazuje jaki ServiceAccount dostaje jakie Role
o co chodzi z tym namespace oraz jak wytłumaczy różne apiVersion? czym dokładnie jest klaster Kubernetesa (jedna aplikacja, namespace, czy co) i jak można nazwać całą konfigurację argocd w tym projekcie? Czy dwie aplikacje na Argo mogą się ze sobą komunikować?

* **Aplikacja metryk**. 
	* deployment_metrics.yaml konfiguruje aplikację lin-ucb-metrics i jej zasoby, pusty wolumen ephemeral oraz port TCP 9000, na którym apka nasłuchuje.
	* service_metrics.yaml tworzy serwis lin-ucb-service-metrics, dzięki któremu możliwa jest komunikacja sieciowa z wyżej deployowaną aplikacją
	* servicemonitor.yaml tworzy obiekt typu ServiceMonitor jaki informuje Prometheusa (o nazwie lin-ucb-argo-monitor), tak by co 10 sekund zbierać metryki z aplikacji o labeli lin-ucb-metrics
Gdzie jest skonfigurowany sam Prometheus dla tej aplikacji?

Zależy mi aby przejść od A do Z po wszystkich logicznych strukturach w argo

Co z configmapą? Czy może wyjaśnić

Jakie zasoby w ogóle poleca na downloaders i processors oraz czy da się je jakoś lokować tylko dorywczo?

Czy potrzebuję w patchach folder resources?

Moja wizja apki devowej - config, pliki yaml, odpowiednie ścieżki i nazwy
Czy muszę prosić o jakąś dodatkową quotę na moth-platform


sprawdzanie DEBUG_MODE
echo $DEBUG_MODE

Po dodaniu nowego modelu należy odpalić Job monitoring i sprawdzić na tym DAGu czy w logach pojawia się nazwa nowego modelu
https://airflow-zds.grupawp.pl/dags/model_mesh_monitoring/grid?root=

Czyszczenie metryk deployerów
> [http://lin-ucb-metrics.moth-platform.k8s-gpu.dc-2.dcwp.pl/cleanup/lin_ucb_argo_deployer_](http://lin-ucb-metrics.moth-platform.k8s-gpu.dc-2.dcwp.pl/cleanup/lin_ucb_argo_deployer_ "http://lin-ucb-metrics.moth-platform.k8s-gpu.dc-2.dcwp.pl/cleanup/lin_ucb_argo_deployer_")X

gdzie za X podstawiamy numery wyłączanych deployerów