---
title: Gestire un cluster DC/OS di Azure con l&quot;API REST Marathon | Documentazione Microsoft
description: Distribuire contenitori in un cluster DC/OS del servizio contenitore di Azure usando l&quot;API REST di Marathon.
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: b2b969500d20d0c840f201ed2cf13a6f2ab38ee5
ms.openlocfilehash: 719f1ea6a6f51d4a787f0465a4bbadb1a6057a8b


---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Gestione dei contenitori DC/OS tramite l'API REST Marathon
DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo. Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. 

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire questi esempi, è necessario avere un cluster DC/OS configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

* [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md)
* [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Accedere alle API di DC/OS
Dopo essersi connessi al cluster del servizio contenitore di Azure, è possibile accedere a DC/OS e alle API REST correlate tramite http://localhost:local-port. Gli esempi riportati in questo documento presuppongono il tunneling sulla porta 80. Ad esempio, gli endpoint Marathon sono raggiungibili usando gli URI che iniziano con `http://localhost/marathon/v2/`. 

Per altre informazioni sulle varie API, vedere la documentazione di Mesosphere per l'[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e l'[API Chronos](https://mesos.github.io/chronos/docs/api.html), nonché la documentazione di Apache per l'[API dell'utilità di pianificazione Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Raccogliere informazioni da DC/OS e Marathon
Prima di distribuire contenitori nel cluster DC/OS, è necessario raccogliere informazioni relative a tale cluster, ad esempio il nome e lo stato corrente degli agenti di DC/OS. A tale scopo, eseguire una query sull'endpoint `master/slaves` dell'API REST di DC/OS. Se la query riesce, verrà restituito un elenco di agenti di DC/OS e diverse proprietà per ognuno.

```bash
curl http://localhost/mesos/master/slaves
```

A questo punto, usare l'endpoint `/apps` di DC/OS per cercare le distribuzioni correnti dell'applicazione nel cluster DC/OS. Se si tratta di un nuovo cluster, viene visualizzata una matrice vuota di app.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Distribuire un contenitore Docker formattato
I contenitori Docker formattati vengono distribuiti tramite Marathon usando un file JSON che descrive la distribuzione prevista. L'esempio seguente mostra come distribuire il contenitore Nginx associando la porta 80 dell'agente di DC/OS alla porta 80 del contenitore. Si noti anche che la proprietà `acceptedResourceRoles` è impostata su `slave_public`. Il contenitore viene distribuito a un agente nel set di scalabilità dell'agente pubblico.

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

Per distribuire un contenitore Docker formattato, archiviare il file JSON in un percorso accessibile. Successivamente, eseguire il comando riportato di seguito per distribuire il contenitore. Specificare il nome del file JSON (`marathon.json` in questo esempio).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

L'output è simile al seguente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Se ora si esegue una query per cercare applicazioni in Marathon, la nuova applicazione viene visualizzata nell'output.

```bash
curl localhost/marathon/v2/apps
```

È possibile verificare che Nginx sia in esecuzione eseguendo una richiesta HTTP al nome di dominio completo del pool di agenti, in `http://<containerServiceName>agents.<region>.cloudapp.azure.com`.

## <a name="scale-your-containers"></a>Ridimensionare i contenitori
L'API Marathon può essere usata per aumentare o ridurre il numero di istanze delle distribuzioni di applicazioni. Nell'esempio precedente è stata distribuita un'istanza di un'applicazione. Il numero di istanze dell'applicazione viene ora aumentato a tre. A tale scopo, creare un file JSON usando il testo JSON seguente e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire questo comando per aumentare il numero di istanze dell'applicazione.

> [!NOTE]
> L'URI è http://localhost/marathon/v2/apps/ seguito dall'ID dell'applicazione da ridimensionare. Se si usa l'esempio di nginx fornito qui, l'URI sarà http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Infine, eseguire una query per le istanze dell'applicazione nell'endpoint Marathon. Ora sono presenti tre contenitori Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Comandi di PowerShell equivalenti
È possibile eseguire queste stesse azioni usando i comandi di PowerShell in un sistema Windows.

Per raccogliere informazioni sul cluster DC/OS, ad esempio il nome e lo stato degli agenti, eseguire il comando seguente:

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

Per distribuire un contenitore Docker formattato, archiviare il file JSON in un percorso accessibile. Successivamente, eseguire il comando riportato di seguito per distribuire il contenitore. Specificare il nome del file JSON (`marathon.json` in questo esempio).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

L'API Marathon può anche essere usata per aumentare o ridurre il numero di istanze delle distribuzioni di applicazioni. Nell'esempio precedente è stata distribuita un'istanza di un'applicazione. Il numero di istanze dell'applicazione viene ora aumentato a tre. A tale scopo, creare un file JSON usando il testo JSON seguente e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire questo comando per aumentare il numero di istanze dell'applicazione:

> [!NOTE]
> L'URI è http://localhost/marathon/v2/apps/ seguito dall'ID dell'applicazione da ridimensionare. Se si usa l'esempio di nginx fornito qui, l'URI sarà http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sugli endpoint HTTP Mesos](http://mesos.apache.org/documentation/latest/endpoints/)
* [Altre informazioni sull'API REST di Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)




<!--HONumber=Feb17_HO1-->


