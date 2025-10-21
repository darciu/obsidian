IaaS - Infrastructure as a Service. Dostęp do mocy obliczeniowej, pamięci, zasobów sieciowych. Klienci płacą z góry za dostęp do przydzielonych zasobów.

PaaS - Platform as a Service. Ponad to ma się dostęp do kodu, który ułatwia korzystanie aplikacji z infrastruktury. Można skupić się na logice aplikacji. Płaci się tylko za wykorzystywane zasoby.

SaaS - Software as a Service. Oprogramowanie działające jako usługa w chmurze sieciowej, do którego jest dostęp przez internet (Gmail, Dysk Google, Google Sheets itp.)

Model bezserwerowy - odpada konfigurowanie serwera, zarządzania infrastrukturą (dzieje się to automatycznie) co bardzo obniża poziom wejścia.

Technologie bezserwerowe w Google Cloud:
- Cloud Functions - kod wykonywany jest w przypadku eventów.
- Cloud Run - skonteneryzowane aplikacje oparte na mikroserwisach.


Content cashing nodes (węzły buforowania treści) - Google Cloud ma ich ponad 100 na świecie, co jest największą ilością dla usług tego typu. W tych węzłach buforowane są najbardziej popularne treści, co przyśpiesza do nich dostęp. Treści te pobierane są z najbliższego węzła do użytkownika.

Infrastruktura Google Cloud mieści się w 5 mega-regionach: 
- north_america
- south_america
- europe
- asia
- australia

Każdy taki mega-region dzieli się na regiony i zony. Regiony to lokalizacje geograficzne i składa się on z zon (stref). Można samodzielnie polepszać dostępność aplikacji lokując ją w odpowiednich regionach. Do automatycznego replikowania usług w regionach służy Cloud Spanner.

Informacje o regionach i zonach
cloud.google.com/about/locations

Google Compute Engine - jest to rozwiązanie typu IaaS. Można uruchamiać wirtualne maszyny na infrastrukturze Google. Każda taka wirtualna maszyna to pełnoprawny system operacyjny (Linux, Windows zoptymalizowane przez Google albo inne OS). W Cloud Marketplace można pobierać oprogramowanie i konfigurację na te VM (też bezpłatnie). Istnieje możliwość autoskalowania VMs w przypadku dużego ruchu sieciowego. W przypadku wielu instancji Compute Engine konieczny jest load balancer, który rozprowadza ruch po nich.