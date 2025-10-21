VPC to wirtualna sieć, która ma być odwzorowaniem fizycznej sieci w Google Cloud. Requesty w sieci przesyłane są od i do na podstawie wewnętrznego DNS oraz wewnętrznego IP. Sieci VPC mają charakter globalny, ich podsieci mogą istnieć w każdym regionie. Podsieci połączone są siecią WAN. Wielkość podsieci można zmieniać przez rozszerzanie zakresu adresów IP do niej przydzielonej.

10.0.0.0/24  - 10.0.0.2, 10.0.0.3 itp


Sieci VPC mają tabele routingu. Służą one do przekierowywania ruchu z jednej instancji do drugiej wewnątrz sieci, dzięki czemu nie trzeba zewnętrznego adresu IP. VPC zapewniaj globalny rozproszony Firewall. Reguły Firewalla są definiowane przez tagi sieci.

Ruch pomiędzy sieciami VPC jest możliwy przez VPC peering.

Serverless VPC Access Connector umożliwia utrzymanie ruchu pomiędzy usługą Cloud Run i siecią VPC. Region konektora musi być taki sam jak usługi Cloud Run. Należy konfigurować konektor na podsieć /28. Po utworzeniu konektora należy skonfigurować usługę Cloud Run, aby używała go (można to zrobić w konsoli i w CLI). Egress usługi powinien w całości przechodzić przez konektor.