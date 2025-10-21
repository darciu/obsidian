Aby uruchomić polyaxon, najlepiej:
- stworzyć wirtualne środowisko (python -m venv venv)
- aktywować je:
```
source venv/bin/activate
```
- zainstalować odpowiednią wersję polyaxon-cli (pip install polyaxon == 1.12.0)
- skonfigurować hosta (polyaxon config set --host=http://polyaxon-wpm.k8s-gpu.dc-2.dcwp.pl/)
- sprawdzić, czy widziane są projekty (polyaxon project ls)

**Polyaxon z GPU dla ZDS**
http://polyaxon-wpm.k8s-gpu.dc-2.dcwp.pl/

**Tworzenie nowego projektu**
```
polyaxon project create --name=dgiemza-pytorch-notebook --tags dariusz.giemza@grupawp.pl, ZDS
```
oraz zainicjować go
```
polyaxon init -p dgiemza-notebooks
```

**Ustawianie usera i grupy userów dla kontenera**
https://polyaxon.com/docs/setup/platform/common-reference/#security-context


**Uruchamianie polyaxonfile**
``` 
polyaxon run -f polyaxonfile.yaml -u
```

**Zasoby CPU i GPU**
http://grafana.k8s-gpu.dc-2.dcwp.pl/d/XreoINP7k/cluster-compute-resource-usage?orgId=1&refresh=1m

**Obrazy PyTorchowe**
```
https://git.dcwp.pl/jukowalik/polyaxon-notebooks/-/tree/main/pytorch
```

Nowszy obraz dla AMD
```
quay-proxy.grupawp.pl/docker.io/rocm/pytorch:rocm6.2.3_ubuntu22.04_py3.10_pytorch_release_2.3.0
```

**Obraz bez GPU**

```
jukowalik/python:3.8_jupyterlab
```

**Obowiązkowe adnotacje na Argo**
https://confluence.grupawp.pl/pages/viewpage.action?spaceKey=OPP&title=Obowiazkowe+adnotacje+i+priorytety


**Inne**
w connections aby mieć dostęp do folderu data należy podać argument
```
old-polyaxon-data
```
