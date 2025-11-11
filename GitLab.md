To platforma DevOpsowa, która działa uruchomiona na serwerze, utrzymuje repozytoria Git, code review, issue tracking, CI/CD i inne funkcje. GitLab Runners to zewnętrzne agenty, gdzie uruchamiane są procesy CI/CD zdefiniowane w plikach .gitlab-ci.yml. Taka niezależność pozwala skalować joby, które nie obciążają serwera.
Innym sposobem odpalania procesów są kontenery Dockera, gdzie można uruchamiać np. testy. GitLab domyślnie używa obrazu dockerowego z Ruby, ale można podać inny z dockerhuba.

Konfiguracja
- stages - określają kolejne kroki wykonywania jobów w pipeline. Wszystkie kroki w danym stage uruchamiają się równolegle, a kolejny stage startuje dopiero po wykonaniu się poprawnie poprzedniego (z wyjątkiem allow_failure: true). Stages są wykonywane w kolejności z listy i mogą uruchamiać się automatycznie lub manualnie. 
```
stages:
- build_worker
- build_runner
- deploy
```
- services - dodatkowe kontenery, uruchamiane obok głównego kontenera joba, z zewnętrznymi zależnościami, tj. bazy danych, Redis, Seleniu, Docker-in-docker. Nie trzeba w takim razie mapować portów, gdyż komunikacja odbywa się po wewnętrznym porcie kontenera.
- docker-in-docker - jest to Docker uruchamiany wewnątrz kontenera aby móc wykonywać akcje build/run/push. Nie jest wymagany o ile Docker jest zainstalowany w Gitlab runnerze
```
Using Docker executor with image common.dockerhub.dcwp.pl/du/dind:latest ...
```
- DOCKER_TLS_CERTDIR - zmienna środowiskowa Dockera, która wskazuje na lokalizację certyfikatów TLS do komunikacji między klientem dockera a demonem dockerd. Zwykle "/certs", "" znaczy wyłączony.
- parallel: matrix: - służy do tworzenia wielu wariantów jednego joba, dzięki różniącym się wartościom w podawanych zmiennych. Zwykle służy do pushowania do wielu rejestrów z wieloma tagami, testowania kodu na różnych środowiskach, podawania różnych wariantów artefaktów.
- szablon joba - jest to stała formułka, która aplikowana jest do kolejnych stage poprzez extends. Ukryty zaczyna się od kropki i jest stosowany tylko gdy się dodany do innego stage, bez kropki to zwykły job.
```
.name_of_stage:
	variables:
		DOCKER_TLS_CERTDIR: ""
		DOCKER_HOST: "tcp://docker:2375"
		
		...
extends: .name_of_stage
```
- gitlab variables - udostępniane są one jobom jako zmienne środowiskowe podczas działania CI/CD. Szczególnie istotne są sekrety, w których mogą być klucze, tokeny. Mogą one być maskowane (niewidoczne w logach). Variable może być w postaci string lub file.
- default - ustawia domyślne wartości atrybutów dla wszystkich jobów, przykładowo image, service
- before_script - lista poleceń uruchamiana na początku joba. Polecenia są wykonywane w shell danego runnera. Jeśli ustawione globalne, to każdego, jeśli w jobie, tylko w tym. Można wyłączyć dla konkretnego joba
```
before_script:[]
```
- needs - jakie joby muszą się wcześniej wykonać aby ten był możliwy do wystartowania (nawet poza porządkiem stageów)
- only - filtrowanie kiedy job jest dodawany do pipeline; przykładowo tylko na pewnych branchach, tagach, przy zmianach w pewnych częściach projektu
- stage vs job - może być zasadniczo używane zamiennie, ale stage to przepis, procedura wykonywania czynności w pewnej fazie pipeline, a job to instancja runnera, która uruchamiana jest dla konkretnego stage. Jeden stage może być wykonywany w postaci kilku jobów przy parallel.