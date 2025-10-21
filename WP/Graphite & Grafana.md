# Graphite

**Wysyłanie danych**
Można korzystać z socket
```
sock = socket.socket()
graphite_host = "graphite-1.dc-2.tools.dcwp.pl"
graphite_port = 2003
sock.connect((graphite_host, graphite_port))

for _, row in df.iterrows():
    time_field, mobileView, isAcUser, deliveries, deliveries_total, users_ac = row
    timestamp = int(time.mktime(time_field.timetuple()))
    d = {'mobileView':mobileView
            ,'isAcUser':isAcUser
            ,'deliveries':deliveries
            ,'deliveries_total':deliveries_total
            ,'users_ac':users_ac}
    for key, val in d.items():
        metric_path = f"aip.is_ac_users.higlogger.{key}"
        metric = f"{metric_path} {val} {timestamp}\n"
        print(metric)
        sock.send(metric.encode('utf-8'))
```

albo biblioteki statsd
https://statsd.readthedocs.io/en/stable/index.html


Sprawdzanie danych:
ssh rnd-5.dc-2.ac.dcwp.pl

```
curl "http://graphite-1.dc-2.tools.dcwp.pl/render?target=aip.is_ac_user.malogger.deliveries&format=json&from=-10h"
```

Lub można podejrzeć je na odpowiedniej grafanie
https://grafana.grupawp.pl/
Dashboards -> New -> 