---
title: Risoluzione dei problemi relativi a Istanze di contenitore di Azure
description: Informazioni su come risolvere i problemi relativi a Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 78bd45f7f71fd25e351d4e9b922a6a3f171437fd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Risolvere i problemi di distribuzione in Istanze di contenitore di Azure

Questo articolo mostra come risolvere i problemi durante la distribuzione di contenitori in Istanze di contenitore di Azure e illustra alcuni problemi comuni che è possibile incontrare.

## <a name="get-diagnostic-events"></a>Recuperare gli eventi di diagnostica

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

## <a name="unable-to-pull-image"></a>Non è possibile eseguire il pull dell'immagine

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

## <a name="container-continually-exits-and-restarts"></a>Il contenitore viene continuamente chiuso e riavviato

Se al completamento dell'esecuzione il contenitore viene riavviato automaticamente, potrebbe essere necessario impostare i [criteri di riavvio](container-instances-restart-policy.md) su **OnFailure** o **Never**. Se si specifica **OnFailure** e si riscontra una situazione di riavvio continuo, potrebbe essere presente un problema con l'applicazione o lo script eseguito nel contenitore.

L'API Istanze di contenitore include una proprietà `restartCount`. Per controllare il numero di riavvii di un contenitore, è possibile usare il comando [az container show](/cli/azure/container#az_container_show) nell'interfaccia della riga di comando di Azure 2.0. Nell'output di esempio seguente (troncato per brevità) la proprietà `restartCount` è visualizzata alla fine dell'output.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La maggior parte delle immagini di contenitore per le distribuzioni di Linux imposta una shell, ad esempio bash, come comando predefinito. Poiché una shell non è di per sé un servizio a esecuzione prolungata, questi contenitori vengono chiusi immediatamente e sono soggetti a un riavvio ciclico quando sono configurati in base al criterio di riavvio predefinito **Always**.

## <a name="container-takes-a-long-time-to-start"></a>L'avvio di un contenitore richiede molto tempo

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

## <a name="resource-not-available-error"></a>Errore di risorsa non disponibile

A causa del carico variabile delle risorse delle aree in Azure, quando si cerca di distribuire un'istanza di contenitore, potrebbe verificarsi l'errore seguente:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Questo errore indica che a causa di un carico elevato nell'area in cui si sta cercando di eseguire la distribuzione, le risorse specificate per il contenitore non possono essere al momento allocate. Usare uno o più dei passaggi seguenti per mitigare il problema.

* Verificare che le impostazioni di distribuzione del contenitore rientrino nei parametri definiti in [Disponibilità a livello di area per Istanze di contenitore di Azure](container-instances-region-availability.md)
* Specificare impostazioni di memoria e CPU inferiori per il contenitore
* Eseguire la distribuzione in un'area di Azure diversa
* Eseguire la distribuzione in un secondo momento
