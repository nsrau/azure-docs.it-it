---
title: Gestire un cluster Azure Kubernetes con l'interfaccia utente Web | Documentazione Microsoft
description: Uso dell'interfaccia utente Web Kubernetes nel servizio contenitore di Azure
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
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 15d423bff629650e205f70c61c592da91339c834
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Uso dell'interfaccia utente Web Kubernetes con il servizio contenitore di Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Prerequisiti
Si presume che questa procedura dettagliata abbia [creato un cluster Kubernetes mediante il servizio contenitore di Azure](container-service-kubernetes-walkthrough.md).


Si presume anche che l'interfaccia della riga di comando di Azure 2.0 e gli strumenti `kubectl` siano installati.

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
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Viene aperto un Web browser configurato per comunicare con un proxy sicuro che connette il computer locale all'interfaccia utente Web Kubernetes.

### <a name="create-and-expose-a-service"></a>Creare ed esporre un servizio
1. Nell'interfaccia utente Web Kubernetes, fare clic sul pulsante **Create** (Crea) nella finestra in alto a destra.

    ![Interfaccia utente di creazione Kubernetes](./media/container-service-kubernetes-ui/create.png)

    Viene visualizzata una finestra di dialogo in cui è possibile iniziare a creare l'applicazione.

2. Assegnare il nome `hello-nginx`. Utilizzare il contenitore [ `nginx` da Docker](https://hub.docker.com/_/nginx/) e distribuire tre repliche del servizio web.

    ![Finestra di dialogo Pod Create Kubernetes (Crea podcast Kubernetes)](./media/container-service-kubernetes-ui/nginx.png)

3. In **Service** (Servizio) selezionare **External** (Esterno) e inserire la porta 80.

    Questa impostazione bilancia il carico del traffico verso le tre repliche.

    ![Finestra di dialogo Kubernetes Service Create (Crea servizio Kubernetes)](./media/container-service-kubernetes-ui/service.png)

4. Fare clic su **Deploy** (Distribuisci) per distribuire questi contenitori e servizi.

    ![Distribuzione di Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Visualizzare i contenitori
Dopo aver fatto clic su **Deploy** (Distribuisci), l'interfaccia utente mostra una visualizzazione del servizio distribuito:

![Stato di Kubernetes](./media/container-service-kubernetes-ui/status.png)

È possibile visualizzare lo stato di ciascun oggetto Kubernetes nel cerchio sul lato sinistro dell'interfaccia utente, in **Pods** (Pod). Se il cerchio è parzialmente completo, l'oggetto è ancora in fase di distribuzione. Quando un oggetto è completamente distribuito, viene visualizzato un segno di spunta verde:

![Kubernetes distribuito](./media/container-service-kubernetes-ui/deployed.png)

Quanto tutto è in esecuzione, fare clic su uno dei pod per visualizzare i dettagli relativi al servizio Web in esecuzione.

![Podcast Kubernetes](./media/container-service-kubernetes-ui/pods.png)

Nella vista **Pods** (Pod), è possibile visualizzare le informazioni sui contenitori nel pod e le risorse di CPU e di memoria usate da questi contenitori:

![Risorse Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Se le risorse non vengono visualizzate, potrebbe essere necessario attendere alcuni minuti per propagare i dati di monitoraggio.

Per visualizzare i log relativi al contenitore, fare clic su **View logs** (Visualizza log).

![Log Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visualizzazione del servizio
Oltre a eseguire i contenitori, l'interfaccia utente di Kubernetes ha creato un riferimento esterno `Service` che esegue il provisioning di un bilanciamento del carico per portare il traffico ai contenitori all'interno del cluster.

Nel riquadro di spostamento a sinistra, fare clic su **Services** (Servizi) per visualizzare tutti i servizi (deve essere presente solo un servizio).

![Servizi Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

In tale visualizzazione, dovrebbe essere visualizzato un endpoint esterno (indirizzo IP) che è stato allocato al servizio.
Se si fa clic su tale indirizzo IP, si noterà il contenitore Nginx in esecuzione dietro il bilanciamento del carico.

![Visualizzazione di nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Ridimensionamento del servizio
Oltre a visualizzare gli oggetti nell'interfaccia utente, è possibile modificare e aggiornare gli oggetti API di Kubernetes.

Innanzitutto, fare clic su **Deployments** (Distribuzioni) nel riquadro di navigazione a sinistra per visualizzare la distribuzione relativa al servizio.

Una volta aperta la visualizzazione, fare clic sul set di repliche, quindi sul pulsante **Edit** (Modifica) nella barra di navigazione superiore:

![Modifica di Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Impostare il campo `spec.replicas` su `2` e fare clic su **Update** (Aggiorna).

In questo modo il numero di repliche si riduce a due eliminando uno dei pod.

 

