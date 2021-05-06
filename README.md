<h1 align="center">
<img src="http://home.apache.org/~fschumacher/jmeter2.svg" width="636" height="216">
</h1>

## Estação de trabalho com Jmeter e Kubernetes em Contêiners Docker 

<p align="justify">
Esse projeto visa estabelecer uma carga de trabalho usando Jmeter no Docker, por meio de comandos manipulando a carga e o stress do app. Entretando para conseguir se adequar o contexto dessa estrutura deve-se subir as imagens da maquina virtual e inicilizar a pilha no Jmeter, utilizando assim o luster Kubernetes, usando como gerenciador de pacotes Helm.
  
Será alterado conforme a minha utilização na estação de trabalho, se caso quise utilizar os commands lines me mande um [*E-mail*📧](mateusouza2014@live.com)
<p>


- Jmeter master
- Jmeter slaves
- InfluxDB instance with graphite interface as a jmeter backend
- Grafana instance
- Dashboard Create instance

## Installation
Using helm repo:
```
helm repo add k8s-jmeter https://MateusMaceedo.github.io/docker-kubernetes-jmeter/charts/
```

Old way: Using local copy of git repository:
```
git clone git@github.com:MateusMaceedo/docker-kubernetes-jmeter.git
cd kubernetes-jmeter/charts/jmeter
helm install -n test ./
```
When jmeter chart is installed from local folder `k8s-jmeter/jmeter` should be replace with `./`.

If you would like to provide custom values.yaml you can add `-f` flag.

```
helm install -n test k8s-jmeter/jmeter -f my_values.yaml
```

The command deploys Jmeter on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

If you change deployment name (`-n test`) please update grafana datasource influx `url` inside your custom values.yaml files.

If you already own grafan and influx stack, kuberentes-jmeter could be deployed without those two dependencies.

```
helm install -n test k8s-jmeter/jmeter --set grafana.enabled=false,influxdb.enabled=false
```

## Run sample test

### Manual run
Copy example test

```
kubectl cp examples/simple_test.jmx $(kubectl get pod -l "app=jmeter-master" -o jsonpath='{.items[0].metadata.name}'):/test/

```
Run tests

```
kubectl exec  -it $(kubectl get pod -l "app=jmeter-master" -o jsonpath='{.items[0].metadata.name}') -- sh -c 'ONE_SHOT=true; /run-test.sh'
```

### Run test via configmap

Upload test as configmap:

```
kubectl create configmap one-test --from-file=./examples/simple_test.jmx
```

Deploy test with auto run, if the `config.master.oneShotTest` would be skipped the test need to be trigger as in manual run step.

Creditos ao projeto base: https://github.com/kaarolch/kubernetes-jmeter/edit/master/README.md
