---
title: Gestire un cluster Azure Kubernetes con l&quot;interfaccia utente Web | Documentazione Microsoft
description: Uso dell&quot;interfaccia utente Web Kubernetes nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 4192369f2e7758200131aa85c60d07436f7cbbdc


---

# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Uso dell'interfaccia utente Web Kubernetes con il servizio contenitore di Azure

## <a name="prerequisites"></a>Prerequisiti
Si presume che questa procedura dettagliata abbia [creato un cluster Kubernetes mediante il servizio contenitore di Azure](container-service-kubernetes-walkthrough.md).

> [!NOTE]
> Il supporto per Kubernetes nel servizio contenitore di Azure è attualmente disponibile in anteprima.
>

Si presume inoltre che gli strumenti azure cli e kubectl siano installati.

È possibile verificare se lo strumento `az` è installato eseguendo:

```console
$ az --version
```

Se lo strumento `az` non è installato, le istruzioni sono disponibili [qui](https://github.com/azure/azure-cli#installation).

È possibile verificare se lo strumento `kubectl` è installato eseguendo:

```console
$ kubectl version
```

Se `kubectl` non è installato, è possibile eseguire:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Panoramica

### <a name="connect-to-the-web-ui"></a>Connettersi all'interfaccia utente Web
È possibile avviare l'interfaccia utente Web Kubernetes eseguendo:

```console
$ az acs kubernetes browse
```

Viene aperto un browser web configurato per comunicare con un proxy sicuro che collega il computer locale all'interfaccia utente Web Kubernetes.

### <a name="create-and-expose-a-service"></a>Creare ed esporre un servizio
Nell'interfaccia utente Web Kubernetes, viene visualizzato un pulsante "Crea" nel riquadro superiore destro.

![Interfaccia utente di creazione Kubernetes](media/k8s/create.png)

Viene visualizzata una finestra di dialogo in cui è possibile iniziare a creare l'applicazione.
Assegnare il nome `hello-nginx`. Utilizzare il contenitore [ `nginx` da Docker](https://hub.docker.com/_/nginx/) e distribuire tre repliche del servizio web.

![Finestra di dialogo Pod Create Kubernetes (Crea podcast Kubernetes)](media/k8s/nginx.png)

Procedere creando un servizio Kubernetes "Esterno" per il bilanciamento del traffico sulle tre repliche.  Selezionare "Esterno" e immettere la porta 80.

![Finestra di dialogo Kubernetes Service Create (Crea servizio Kubernetes)](media/k8s/service.png)

Infine, premere il pulsante "Distribuisci" per distribuire questi contenitori e servizi.

![Distribuzione di Kubernetes](media/k8s/deploy.png)

### <a name="view-your-containers"></a>Visualizzare i contenitori
Dopo aver premuto "Distribuisci", l'interfaccia utente mostra una visualizzazione del servizio distribuito:

![Stato di Kubernetes](media/k8s/status.png)

È possibile visualizzare lo stato di ciascun oggetto Kubernetes nel cerchio sul lato sinistro dell'interfaccia utente. Se il cerchio è parzialmente completo, l'oggetto è ancora in fase di distribuzione. Quando un oggetto è completamente distribuito, viene visualizzato un segno di spunta verde:

![Kubernetes distribuito](media/k8s/deployed.png)

Quanto tutto è in esecuzione, è possibile fare clic su uno dei podcast per visualizzare i dettagli relativi al servizio web in esecuzione

![Podcast Kubernetes](media/k8s/pods.png)

Nella visualizzazione specifica del podcast, è possibile visualizzare le informazioni sui contenitori nel podcast e le risorse di CPU e di memoria utilizzate da questi contenitori:

![Risorse Kubernetes](media/k8s/resources.png)

Se le risorse non vengono visualizzate, potrebbe essere necessario attendere alcuni minuti per propagare i dati di monitoraggio.

È inoltre possibile fare clic sul collegamento "Log" per visualizzare i log per il contenitore:

![Log Kubernetes](media/k8s/logs.png)

### <a name="viewing-your-service"></a>Visualizzazione del servizio
Oltre a eseguire i contenitori, l'interfaccia utente di Kubernetes ha creato un riferimento esterno `Service` che esegue il provisioning di un bilanciamento del carico per portare il traffico ai contenitori all'interno del cluster.

È possibile fare clic su "Servizi" nel riquadro di navigazione sinistroa per visualizzare tutti i servizi (al momento deve essere solo uno)

![Servizi Kubernetes](media/k8s/service-deployed.png)

In tale visualizzazione, dovrebbe essere possibile visualizzare un indirizzo IP esterno che è stato allocato al servizio.
Se si fa clic su tale indirizzo IP, si noterà il contenitore nginx in esecuzione dietro il bilanciamento del carico.

![Visualizzazione di nginx](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>Ridimensionamento del servizio
Oltre a visualizzare gli oggetti nell'interfaccia utente, è possibile modificare e aggiornare gli oggetti API di Kubernetes.

Innanzitutto, vedere `Deployment` per il servizio facendo clic su "Distribuzioni" nel riquadro di navigazione a sinistra.

Una volta aperta la visualizzazione, fare clic su ReplicaSet, quindi sul pulsante "Modifica" nella barra di navigazione superiore:

![Modifica di Kubernetes](media/k8s/edit.png)

Modificare il campo `spec.replicas` in "2" e premere "Aggiorna".

In questo modo il numero di repliche si riduce a due eliminando uno dei podcast.

 




<!--HONumber=Jan17_HO4-->


