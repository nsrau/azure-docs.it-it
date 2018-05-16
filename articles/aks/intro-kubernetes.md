---
title: Introduzione ad Azure Kubernetes Service
description: Azure Kubernetes Service semplifica la distribuzione e la gestione delle applicazioni basate su contenitore in Azure.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4936554465fbbed45000f43853a6a77567c3028f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="introduction-to-azure-kubernetes-service-aks-preview"></a>Introduzione ad Azure Kubernetes Service (AKS) - anteprima

Azure Kubernetes Service (AKS) semplifica la creazione, la configurazione e la gestione di un cluster di macchine virtuali preconfigurate per eseguire applicazioni in contenitori. Ciò consente di usare le competenze già acquisite o di attingere da un consistente e crescente bagaglio di competenze a livello di community per distribuire e gestire applicazioni basate sul contenitore in Microsoft Azure.

Il servizio contenitore di Azure consente di sfruttare i vantaggi delle funzionalità di livello aziendale di Azure mantenendo al tempo stesso la portabilità delle applicazioni tramite Kubernetes e il formato immagine Docker.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) è attualmente disponibile in **anteprima**. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes in Azure

AKS riduce la complessità e costi operativi di gestione di un cluster Kubernetes addossando gran parte di tale responsabilità ad Azure. Come servizio Kubernetes ospitato, Azure gestisce attività critiche quali il monitoraggio dell'integrità e la manutenzione per l'utente. Inoltre, si paga solo per i nodi dell'agente all'interno del cluster, non per i master. Come servizio Kubernetes gestito, AKS fornisce:

> [!div class="checklist"]
> * Aggiornamenti di versione Kubernetes automatizzati e l'applicazione di patch
> * Semplice scalabilità del cluster
> * Piano di controllo ospitato di riparazione automatica (master)
> * Risparmio sui costi - si paga solo per l'esecuzione dei nodi del pool di agenti

Con la gestione dei nodi da parte di Azure nel cluster AKS non è più necessario eseguire manualmente numerose attività, ad esempio gli aggiornamenti dei cluster. Poiché Azure gestisce queste attività di manutenzione critiche per l'utente, AKS non fornisce accesso diretto, come con SSH, al cluster.

## <a name="using-azure-kubernetes-service-aks"></a>Uso di Azure Kubernetes Service (AKS)
L'obiettivo del servizio contenitore di Azure è offrire un ambiente host per contenitori con le tecnologie e gli strumenti open source attualmente diffusi tra i clienti. A questo scopo vengono esposti gli endpoint API di Kubernetes standard. Usando questi endpoint standard è possibile usare qualsiasi software riesca a comunicare con un cluster Kubernetes. È ad esempio possibile scegliere [kubectl][kubectl-overview], [helm][helm] o [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-kubernetes-service-aks"></a>Creazione di un cluster Kubernetes tramite Azure Kubernetes Service (AKS)
Per iniziare a usare il servizio contenitore di Azure, distribuire un cluster del servizio contenitore di Azure con l'[interfaccia della riga di comando di Azure][aks-quickstart] o tramite il portale (cercare **Azure Kubernetes Service** nel Marketplace). Gli utenti avanzati che hanno bisogno di un maggiore controllo sui modelli di Azure Resource Manager possono usare il progetto open source [acs-engine][acs-engine] per compilare un cluster Kubernetes personalizzato e distribuirlo tramite l'interfaccia della riga di comando `az`.

### <a name="using-kubernetes"></a>Uso di Kubernetes
Kubernetes automatizza la distribuzione, il ridimensionamento e la gestione delle applicazioni nei contenitori. Dispone di un set completo di funzionalità tra cui:
* Bin packing automatico
* Riparazione automatica
* Scalabilità orizzontale
* Bilanciamento del carico e rilevamento del servizio
* Implementazioni e ripristini dello stato precedente automatizzati
* Gestione della configurazione e dei segreti
* Orchestrazione dell'archiviazione
* Esecuzione batch

## <a name="videos"></a>Video

Azure Kubernetes Service (AKS) - Azure Venerdì, ottobre 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Strumenti per lo sviluppo e la distribuzione di applicazioni in Kubernetes - Azure OpenDev, giugno 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Altre informazioni sulla distribuzione e la gestione del servizio contenitore di Azure sono disponibili nella relativa guida introduttiva.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio contenitore di Azure][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

