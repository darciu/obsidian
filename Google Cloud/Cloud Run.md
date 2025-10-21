Cloud Run service - kod, który odpowiada na requesty sieciowe lub na eventy

Cloud Run job - kod wykonujący jakieś zadanie i kończy swoją pracę (np. skrypty)

Service lub Job wykonuje kod tylko poprzez kontener. Kontener musi spełniać warunek, że da się skompilować w architekturze Linuxa 64-bit.


**Service**
Cloud Run service zapewnia infrastrukturę, która jest w stanie obsłużyć requesty HTTPS. Należy jedynie upewnić się, że kod słucha odpowiedniego portu TCP. Cloud Run Service to w zasadzie proxy  (https://*.run.app), do którego można przypiąć własną domenę oraz instancja kontenera. Wystawiane są subdomeny \*.run.app/ jako endpointy.

Zapewniany jest certyfikat TLS.

Wspierane są:
WebSockets - 
HTTP/2 - 
gRPC -



**Job**

Wywoływanie jobów odbywa się poprzez workflow schedule lub poprzez gcloud CLI. Joby odpalają jeden lub wiele kontenerów równolegle. Wiele instancji tego samego kontenera nazywane jest Array Jobs. Każdy kontener to jeden task. Zakończenie pomyślne joba wymaga wykonania wszystkich tasków z sukcesem. Można podawać timeouty i retries dla tasków jeśli jakiś miałby się zakończyć failure'm.



**Regiony**

Region może mieć trzy lub więcej zon. Zony i regiony to logiczna abstrakcja, która pokrywa się z prawdziwymi data centrami (na jedną zonę może przypadać więcej niż jedno data center). Kontenery są rozdystrybuowane pomiędzy różnymi zonami w regionie, tak by były one odporne na faile.


**Rewizje, kontenery**

Revisions są niezmienialne (immutable) i są to kopie obrazu wraz z konfiguracją. Każda kolejna wersja wdrożonego obrazu to kolejna rewizja. Requesty wysyłane do aplikacji są kierowane do ostatniej sprawnej rewizji. To właśnie rewizje są skalowane do odpowiedniej liczby kontenerów w zależności od natężenia requestów. Rewizje skalują się niezależnie od siebie i dopóki nowa nie jest gotowa, ruch jest przekierowywany do starszej wersji. Można ustawić w ilu procentach ruch obsługują różne rewizje (gradual rollout). Nowa rewizja może też nie otrzymywać żadnego ruchu (parametr -no-traffic).

Obrazy kontenerów znajdują się w Artifact Registry. Jednak Cloud Run kopiuje sobie taki obraz do wewnętrznego storage (internal storage). Internal storage jest zoptymalizowany pod względem uruchamiania kontenerów. Jeśli przypadkowo usunie się obraz z Artifact Storage, to kontener będzie wciąż działał.

Kontener po 100 ms bez requestu przechodzi w stan idle. Taki stan trwa maksymalnie do 15 minut. Później, jeśli nie jest on używany, to kontener jest zatrzymywany. To zatrzymywanie trwa 10 sekund. W międzyczasie wysyłany jest sygnał SIGTERM, dzięki któremu aplikacja może w te 10 sekund zakończyć pewne procesy (np. pozamykać kontektory SQL, TCP, wysłać dane telemetryczne). Kontener może zakończyć życie, jeśli przekroczony zostanie limit pamięci (domyślnie to jest 512 MiB, max 32 GiB). W takim przypadku wszystkie procesy kończą się failem.

Autoskalowanie polega na zwiększaniu/zmniejszaniu liczby kontenerów w zależności od ruchu requestów (a także od zadanych parametrów). Jeśli wszystkie kontenery są zajęte, to Cloud Run dodaje kolejne instancje (maksymalnie do 100). Jeśli ruch spada i jakieś kontenery są bezczynne, to po jakimś czasie są one zatrzymywane. Kontenery mogą otrzymywać wiele requestów jednocześnie (parametr concurrency). Ruch sieciowy obsługuje wewnętrzny load balancer w danym revision.

Parametr concurrency określa jak szybko skalowane są kontenery. Jeśli wynosi on 1, to dzieje się to szybko. Aplikacja powinna móc obsługiwać wiele requestów jednocześnie. Jego domyślna wartość to 80, ale można ją zwiększyć do 1000.

**API**
W Google Cloud mamy do dyspozycji różne API:
- konsola w przeglądarce
- gcloud CLI
- Terraform jako osobna aplikacja, gdzie infrastruktura i konfiguracja jest postrzegana jako kod
- zewnętrzne biblioteki

IAM (Identity and Access Management) to usługa, która pozwala tworzyć i zarządzać uprawnieniami w zasobach Google Cloud na podstawie tożsamości osoby za sterami API. Posługuje się ona politykami (policies), które są zbiorem zależności użytkownik - jaką rolę może wykonywać. Polityki IAM są przypisane do zasobów.

Dokumentacja
cloud.google.com/iam/docs/roles-overview

**Ingress**

Oprócz IAM istnieje możliwość kontroli Ingressu sieciowego, czyli tego co wchodzi do aplikacji. Istnieją trzy ustawienia Ingressu:
- All - przechodzą wszystkie requesty z internetu do URLa aplikacji (lub do podanej domeny)
- Internal - requesty mogą trafić do usługi tylko poprzez
	- wewnętrzny load balancer HTTP(S)
	- przez VPC Service Controls
	- przez sieci VPC
	- usługi Google Cloud będące w tej samej sieci VPC lub w projekcie
- Internal and Cloud Load Balancing - tak jak Internal ale dodatkowo możliwe są zewnętrzne load balancery

**Execution environments**
Istnieją dwa środowiska wykonawcze:
- pierwszej generacji - domyślne środowisko usług, możliwe do zmiany, szybka skalowalność, krótki czas cold start, niejednostajny ruch, usługa zajmuje mniej niż 512 MiB pamięci, niedostępne dla jobów
- drugiej generacji - używane w jobach domyślnie, możliwość korzystania z network file system, jednostajny ruch sieciowy, większy czas cold start, duże zapotrzebowanie na CPU, pełna kompatybilność z Linuxem


**Artifact Registry**
Przechowalnia obrazów Dockera. Każdy obraz ma swój unikalny URL. Dopiero stamtąd obrazy są kopiowane i tworzone są na ich podstawie kontenery, które działają w Cloud Run.
