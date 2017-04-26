---
title: Monitorare il cluster Kubernetes di Azure - Operations Management | Documentazione Microsoft
description: Monitoraggio di cluster Kubernetes nel servizio contenitore di Azure con Microsoft Operations Management Suite
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
ms.sourcegitcommit: 4e4a4f4e299dc2747eb48bbd2e064cd80783211c
ms.openlocfilehash: 46240f3dc99a8c8a103a1e7919ad4f5e7a8ea62a
ms.lasthandoff: 04/04/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Monitorare un cluster del servizio contenitore di Azure con Microsoft Operations Management Suite (OMS)

## <a name="prerequisites"></a>Prerequisiti
Si presume che questa procedura dettagliata abbia [creato un cluster Kubernetes mediante il servizio contenitore di Azure](container-service-kubernetes-walkthrough.md).

Si presume anche che gli strumenti dell'interfaccia della riga di comando di Azure `az` e `kubectl` siano installati.

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

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Monitoraggio dei contenitori con Operations Management Suite (OMS)

Microsoft Operations Management (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. La soluzione contenitore è una soluzione di Log Analytics di OMS che consente di visualizzare l'inventario, le prestazioni e i log del contenitore in un'unica posizione. È possibile controllare, risolvere i problemi relativi ai contenitori visualizzando i log in una posizione centralizzata e trovare un contenitore con un consumo eccessivo in un host.

![](media/container-service-monitoring-oms/image1.png)

Per altre informazioni sulla soluzione contenitore, consultare [Log Analytics della soluzione contenitore](../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Installazione di OMS in Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Per consentire la comunicazione dell'agente OMS con il servizio, è necessario configurare il rispettivo ID e la rispettiva chiave dell'area di lavoro. Per ottenere l'ID e la chiave dell'area di lavoro è necessario creare un account OMS nel sito <https://mms.microsoft.com>.
Attenersi alla procedura di creazione di un account. Al termine della creazione dell'account, è necessario ottenere l'ID e la chiave facendo clic su **Impostazioni**, quindi **Origini connesse** e infine **Server Linux**, come illustrato di seguito.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Installare l'agente OMS usando un oggetto DaemonSet
Gli elementi DaemonSet vengono usati da Kubernetes per eseguire una singola istanza di un contenitore in ogni host del cluster.
Sono ideali per l'esecuzione di agenti di monitoraggio.

Ecco il file [YAML di DaemonSet](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Salvarlo in un file denominato `oms-daemonset.yaml` e sostituire i valori segnaposto per `WSID` e `KEY` con l'ID e la chiave dell'area di lavoro disponibili nel file.

Dopo avere aggiunto l'ID e la chiave dell'area di lavoro alla configurazione di DaemonSet, è possibile installare l'agente OMS nel cluster con lo strumento da riga di comando `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>Conclusioni
L'operazione è terminata. Dopo qualche minuto dovrebbe essere visualizzato il flusso di dati verso il dashboard OMS.

