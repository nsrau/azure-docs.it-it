---
title: Risoluzione dei problemi relativi a Istanze di contenitore di Azure
description: Informazioni su come risolvere i problemi relativi a Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/03/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: bff594d86c7c34ebff4d7dad5be4e54cdb604baf
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---

# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Risolvere i problemi di distribuzione in Istanze di contenitore di Azure

Questo articolo mostra come risolvere i problemi durante la distribuzione di contenitori in Istanze di contenitore di Azure e illustra alcuni problemi comuni che è possibile incontrare.

## <a name="getting-diagnostic-events"></a>Recupero degli eventi di diagnostica

Per visualizzare i log generati dal codice dell'applicazione all'interno di un contenitore, è possibile usare il comando [az container logs](/cli/azure/container#logs). Se tuttavia il contenitore non viene distribuito correttamente, è necessario esaminare le informazioni di diagnostica fornite dal provider di risorse Istanze di contenitore di Azure. Per visualizzare gli eventi per il proprio contenitore, eseguire questo comando:

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

L'output include le proprietà principali del contenitore e gli eventi di distribuzione:

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Problemi di distribuzione comuni

La maggior parte degli errori riscontrati durante la distribuzione è dovuta ad alcuni problemi comuni.

### <a name="unable-to-pull-image"></a>Non è possibile eseguire il pull dell'immagine

Se Istanze di contenitore di Azure non è inizialmente in grado di eseguire il pull dell'immagine, ripete il tentativo per un certo periodo di tempo prima di generare un errore. Se il pull dell'immagine non può essere eseguito, vengono visualizzati eventi simili al seguente:

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Per risolvere il problema, eliminare il contenitore e ripetere il tentativo di distribuzione, facendo attenzione a digitare correttamente il nome dell'immagine.

### <a name="container-continually-exits-and-restarts"></a>Il contenitore viene continuamente chiuso e riavviato

Istanze di contenitore di Azure supporta attualmente solo servizi a esecuzione prolungata. Se il contenitore termina l'esecuzione e si chiude, viene riavviato in automatico ed eseguito nuovamente. In questo caso, vengono visualizzati eventi simili ai seguenti. Si noti che il contenitore viene avviato correttamente e quindi riavviato con rapidità. L'API di Istanze di contenitore include una proprietà `retryCount` che indica il numero di volte in cui un determinato contenitore è stato riavviato.

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> La maggior parte delle immagini di contenitore per le distribuzioni di Linux imposta una shell, ad esempio bash, come comando predefinito. Poiché una shell non è di per sé un servizio a esecuzione prolungata, questi contenitori vengono chiusi immediatamente e sono soggetti a un riavvio ciclico.

### <a name="container-takes-a-long-time-to-start"></a>L'avvio di un contenitore richiede molto tempo

Se per avviare il contenitore è necessario molto tempo, ma alla fine l'operazione ha esito positivo, esaminare innanzitutto la dimensione dell'immagine del contenitore. Poiché Istanze di contenitore di Azure esegue il pull dell'immagine del contenitore su richiesta, il tempo di avvio rilevato è direttamente correlato alla dimensione dell'immagine.

Per visualizzare la dimensione dell'immagine del contenitore è possibile usare l'interfaccia della riga di comando di Docker:

```bash
docker images
```

Output:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

Il modo migliore per limitare le dimensioni delle immagini è quello di evitare che l'immagine finale contenga dati che non sono necessari in fase di esecuzione. A tale scopo è possibile eseguire [compilazioni in più fasi](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Le compilazioni di questo tipo consentono di assicurarsi che l'immagine finale contenga solo gli elementi necessari per l'applicazione, escludendo altro contenuto richiesto in fase di compilazione.

L'altro modo per ridurre l'impatto del pull dell'immagine sul tempo di avvio del contenitore è quello di ospitare l'immagine del contenitore usando il Registro contenitori di Azure nella stessa area in cui si intende usare Istanze di contenitore di Azure. Ciò consente di ridurre il percorso dell'immagine del contenitore attraverso la rete e quindi di limitare notevolmente il tempo di download.
