<properties
   pageTitle="Gestione di contenitori ACS con l'API REST | Microsoft Azure"
   description="Distribuire contenitori in un cluster del servizio contenitore di Azure usando l'API REST di Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# Gestione di contenitori con l'API REST

Mesos offre un ambiente di distribuzione e ridimensionamento del carico di lavoro cluster con l'astrazione dell'hardware sottostante. Su Mesos, i framework gestiscono la pianificazione e l'esecuzione del carico di lavoro di calcolo. Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento fornisce informazioni dettagliate sulla creazione e il ridimensionamento della distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario avere a disposizione un cluster Mesos configurato in ACS e la connettività remota al cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito.

- [Distribuire un cluster del servizio contenitore di Azure](./container-service-deployment.md) 
- [Connessione a un cluster ACS](./container-service-connect.md)


Dopo aver configurato il tunnel SSH sarà possibile accedere alle API REST correlate a Mesos attraverso `http://localhost:LOCAL_PORT`. Negli esempi seguenti si presuppone l'uso della porta 80 per il tunnel. Ad esempio, `http://localhost/marathon/v2` sarà l'endpoint per l'API Marathon. Per altre informazioni sulle varie API disponibili, vedere la documentazione di Mesosphere per l'[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e l'[API Chronos](https://mesos.github.io/chronos/docs/api.html), nonché la documentazione di Apache per l'[API dell'utilità di pianificazione Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Raccogliere informazioni da Mesos e Marathon

Prima di distribuire contenitori nel cluster Mesos, è necessario raccogliere informazioni relative a tale cluster, ad esempio il nome e lo stato corrente degli agenti di Mesos. A tale scopo, eseguire una query sull'endpoint `master/slaves` in un master Mesos. Se tutto va bene, verrà visualizzato un elenco di agenti di Mesos e varie proprietà per ognuno.

```bash
curl http://localhost/master/slaves
```

A questo punto, usare l'endpoint `/apps` di Marathon per cercare distribuzioni di Marathon correnti nel cluster Mesos. Se si tratta di un nuovo cluster, verrà visualizzata una matrice vuota di app.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Distribuzione di un contenitore Docker

I contenitori Docker vengono distribuiti tramite Marathon usando un file JSON che descrive la distribuzione prevista. L'esempio seguente mostra come distribuire il contenitore nginx associando la porta 80 dell'agente di Mesos alla porta 80 del contenitore.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Per distribuire un contenitore Docker, creare un file JSON o usare l'esempio fornito nella [demo per ACS di Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) e archiviarlo in un percorso accessibile. Successivamente, eseguire questo comando per distribuire il contenitore, specificando il nome del file JSON.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

L'output sarà simile al seguente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Se ora si esegue una query per le applicazioni in esecuzione in Marathon, la nuova applicazione verrà visualizzata nell'output.

```
curl localhost/marathon/v2/apps
```

## Ridimensionare un contenitore Docker

L'API Marathon può anche essere usata per aumentare o ridurre il numero di istanze delle distribuzioni di applicazioni. Nell'esempio precedente è stata distribuita un'istanza di una applicazione. Ora il numero di istanze verrà aumentato a tre. A tale scopo, creare un file JSON con il testo JSON seguente e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire questo comando per aumentare il numero di istanze dell'applicazione.

> Nota: l'URI sarà http://localhost/marathon/v2/apps/ e si tratta dell'ID dell'applicazione da ridimensionare. Se si usa l'esempio di nginx fornito qui, l'URI sarà http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Infine, eseguire una query per l'istanza dell'applicazione nell'endpoint Marathon. Le istanze ora sono tre.

```
curl localhost/marathon/v2/apps
```

## API REST di Marathon con PowerShell

La stessa azione può essere eseguita usando PowerShell in un sistema Windows. Questo esercizio rapido illustra attività simili all'esercizio precedente, questa volta con i comandi di PowerShell.

Per raccogliere informazioni sul cluster Mesos, ad esempio il nome e lo stato dell'agente, eseguire questo comando.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

I contenitori Docker vengono distribuiti tramite Marathon usando un file JSON che descrive la distribuzione prevista. L'esempio seguente mostra come distribuire il contenitore nginx associando la porta 80 dell'agente di Mesos alla porta 80 del contenitore.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Creare un file JSON o usare l'esempio fornito nella [demo per ACS di Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) e archiviarlo in un percorso accessibile. Successivamente, eseguire questo comando per distribuire il contenitore, specificando il nome del file JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

L'API Marathon può anche essere usata per aumentare o ridurre il numero di istanze delle distribuzioni di applicazioni. Nell'esempio precedente è stata distribuita un'istanza di una applicazione. Ora il numero di istanze verrà aumentato a tre. A tale scopo, creare un file JSON con il testo JSON seguente e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire questo comando per aumentare il numero di istanze dell'applicazione.

> Nota: l'URI sarà http://loclahost/marathon/v2/apps/ e si tratta dell'ID dell'applicazione da ridimensionare. Se si usa l'esempio di nginx fornito qui, l'URI sarà http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0218_2016-->