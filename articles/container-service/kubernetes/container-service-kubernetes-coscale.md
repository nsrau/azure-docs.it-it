---
title: Monitorare un cluster Azure Kubernetes con CoScale | Microsoft Docs
description: Monitorare un cluster Kubernetes nel servizio contenitore di Azure tramite CoScale
services: container-service
documentationcenter: 
author: fryckbos
manager: 
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f894191baced710fc0f5a8c8692df98033341a48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Monitorare un cluster Kubernetes del servizio contenitore di Azure con CoScale

Questo articolo illustra come distribuire l'agente [CoScale](https://www.coscale.com/) per monitorare tutti i nodi e tutti i contenitori del cluster Kubernetes nel servizio contenitore di Azure. Per questa configurazione, è necessario un account con CoScale. 


## <a name="about-coscale"></a>Informazioni su CoScale 

CoScale è una piattaforma di monitoraggio che raccoglie metriche ed eventi da tutti i contenitori in diverse piattaforme di orchestrazione. CoScale offre il monitoraggio dello stack completo per gli ambienti Kubernetes. Fornisce visualizzazioni e funzionalità di analisi per tutti i livelli dello stack, ovvero il sistema operativo, Kubernetes, Docker e le applicazioni in esecuzione all'interno dei contenitori. CoScale offre alcuni dashboard di monitoraggio incorporati e include il rilevamento delle anomalie per consentire a operatori e sviluppatori di individuare rapidamente i problemi relativi all'infrastruttura e alle applicazioni.

![Interfaccia utente di CoScale](./media/container-service-kubernetes-coscale/coscale.png)

Come illustrato in questo articolo, è possibile installare gli agenti in un cluster Kubernetes per eseguire CoScale come soluzione SaaS. Se si vogliono conservare i dati in sede, CoScale è disponibile anche per le installazioni locali.


## <a name="prerequisites"></a>Prerequisiti

È prima di tutto necessario [creare un account CoScale](https://www.coscale.com/free-trial).

Si presume che questa procedura dettagliata abbia [creato un cluster Kubernetes mediante il servizio contenitore di Azure](container-service-kubernetes-walkthrough.md).

Si presume anche che gli strumenti dell'interfaccia della riga di comando di Azure `az` e `kubectl` siano installati.

È possibile verificare se lo strumento `az` è installato eseguendo:

```azurecli
az --version
```

Se lo strumento `az` non è installato, le istruzioni sono disponibili [qui](/cli/azure/install-azure-cli).

È possibile verificare se lo strumento `kubectl` è installato eseguendo:

```bash
kubectl version
```

Se `kubectl` non è installato, è possibile eseguire:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Installazione dell'agente CoScale con DaemonSet
Gli elementi [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) vengono usati da Kubernetes per eseguire una singola istanza di un contenitore in ogni host del cluster.
Sono ideali per l'esecuzione di agenti di monitoraggio, ad esempio l'agente CoScale.

Dopo l'accesso a CoScale, passare alla [pagina degli agenti](https://app.coscale.com/) per installare gli agenti CoScale nel cluster tramite DaemonSet. L'interfaccia utente di CoScale fornisce una procedura guidata per la configurazione per creare un agente e iniziare a monitorare il cluster Kubernetes completo.

![Configurazione dell'agente CoScale](./media/container-service-kubernetes-coscale/installation.png)

Per avviare l'agente sul cluster, eseguire il comando specificato:

![Avviare l'agente CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

L'operazione è terminata. Con gli agenti operativi, i dati verranno visualizzati nella console entro alcuni minuti. Visitare la [pagina degli agenti](https://app.coscale.com/) per visualizzare un riepilogo del cluster, eseguire procedure aggiuntive per la configurazione e visualizzare i dashboard, ad esempio la **panoramica del cluster Kubernetes**.

![Panoramica del cluster Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

L'agente CoScale viene distribuito automaticamente nelle nuove macchine virtuali del cluster. L'agente viene aggiornato automaticamente al rilascio di una nuova versione.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle soluzioni di monitoraggio di CoScale, vedere la [documentazione di CoScale](http://docs.coscale.com/) e il [blog](https://www.coscale.com/blog). 

