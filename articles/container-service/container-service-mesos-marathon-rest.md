<properties
   pageTitle="Gestione di contenitori del servizio contenitore di Azure tramite l'API REST | Microsoft Azure"
   description="Distribuire contenitori in un cluster Mesos del servizio contenitore di Azure usando l'API REST di Marathon."
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

# Gestione di contenitori tramite l'API REST

DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario avere un cluster DC/OS configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

- [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md)
- [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)

Dopo essersi connessi al cluster del servizio contenitore di Azure, è possibile accedere a DC/OS e alle API REST correlate tramite http://localhost:local-port. Gli esempi riportati in questo documento presuppongono il tunneling sulla porta 80. Ad esempio, l'endpoint Marathon è disponibile in `http://localhost/marathon/v2/`. Per altre informazioni sulle varie API, vedere la documentazione di Mesosphere per l'[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e l'[API Chronos](https://mesos.github.io/chronos/docs/api.html), nonché la documentazione di Apache per l'[API dell'utilità di pianificazione Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Raccogliere informazioni da DC/OS e Marathon

Prima di distribuire contenitori nel cluster DC/OS, è necessario raccogliere informazioni relative a tale cluster, ad esempio il nome e lo stato corrente degli agenti di DC/OS. A tale scopo, eseguire una query sull'endpoint `master/slaves` dell'API REST di DC/OS. Se la query riesce, verrà visualizzato un elenco di agenti di DC/OS e diverse proprietà per ognuno.

```bash
curl http://localhost/mesos/master/slaves
```

A questo punto, usare l'endpoint `/apps` di DC/OS per cercare le distribuzioni correnti dell'applicazione nel cluster DC/OS. Se si tratta di un nuovo cluster, verrà visualizzata una matrice vuota di app.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Distribuire un contenitore Docker formattato

I contenitori Docker formattati vengono distribuiti tramite Marathon usando un file JSON che descrive la distribuzione prevista. L'esempio seguente mostra come distribuire il contenitore Nginx associando la porta 80 dell'agente di DC/OS alla porta 80 del contenitore. Si noti anche che la proprietà 'acceptedResourceRoles' è impostata su 'slave\_public'. Il contenitore verrà distribuito a un agente nel set di scalabilità dell'agente pubblico.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
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

Per distribuire un contenitore Docker formattato, creare un file JSON o usare l'esempio fornito nella [demo del servizio contenitore di Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Archiviarlo in un percorso accessibile. Successivamente, eseguire il comando riportato di seguito per distribuire il contenitore. Specificare il nome del file JSON.

```
# deploy container

curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

L'output sarà simile al seguente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Se ora si esegue una query per cercare applicazioni in Marathon, la nuova applicazione verrà visualizzata nell'output.

```
curl localhost/marathon/v2/apps
```

## Ridimensionare i contenitori

L'API Marathon può anche essere usata per aumentare o ridurre il numero di istanze delle distribuzioni di applicazioni. Nell'esempio precedente è stata distribuita un'istanza di un'applicazione. Il numero di istanze dell'applicazione viene ora aumentato a tre. A tale scopo, creare un file JSON usando il testo JSON seguente e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire questo comando per aumentare il numero di istanze dell'applicazione.

>[AZURE.NOTE] L'URI sarà http://localhost/marathon/v2/apps/ seguito dall'ID dell'applicazione da ridimensionare. Se si usa l'esempio di nginx fornito qui, l'URI sarà http://localhost/marathon/v2/apps/nginx.

```json
# scale container

curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Infine, eseguire una query per le istanze dell'applicazione nell'endpoint Marathon. Ora sono presenti tre applicazioni del contenitore nginx.

```
curl localhost/marathon/v2/apps
```

## Usare PowerShell per questo esercizio: Interazione dell'API REST di Marathon con PowerShell

È possibile eseguire queste stesse azioni usando i comandi di PowerShell in un sistema Windows.

Per raccogliere informazioni sul cluster DC/OS, ad esempio il nome e lo stato dell'agente, eseguire il comando seguente.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

I contenitori Docker formattati vengono distribuiti tramite Marathon usando un file JSON che descrive la distribuzione prevista. L'esempio seguente mostra come distribuire il contenitore Nginx associando la porta 80 dell'agente di DC/OS alla porta 80 del contenitore.

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

Creare un file JSON o usare l'esempio fornito nella [demo del servizio contenitore di Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Archiviarlo in un percorso accessibile. Successivamente, eseguire il comando riportato di seguito per distribuire il contenitore. Specificare il nome del file JSON.

```powershell
# deploy container

Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

L'API Marathon può anche essere usata per aumentare o ridurre il numero di istanze delle distribuzioni di applicazioni. Nell'esempio precedente è stata distribuita un'istanza di un'applicazione. Il numero di istanze dell'applicazione viene ora aumentato a tre. A tale scopo, creare un file JSON usando il testo JSON seguente e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire questo comando per aumentare il numero di istanze dell'applicazione.

> [AZURE.NOTE] L'URI sarà http://localhost/marathon/v2/apps/ seguito dall'ID dell'applicazione da ridimensionare. Se si usa l'esempio di nginx fornito qui, l'URI sarà http://localhost/marathon/v2/apps/nginx.

```powershell
# scale container

Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## Passaggi successivi

[Altre informazioni sugli endpoint HTTP Mesos](http://mesos.apache.org/documentation/latest/endpoints/). [Altre informazioni sull'API REST di Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html).

<!---HONumber=AcomDC_0525_2016-->