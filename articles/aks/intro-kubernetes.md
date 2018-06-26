---
title: Introduzione ad Azure Kubernetes Service
description: Azure Kubernetes Service semplifica la distribuzione e la gestione delle applicazioni basate su contenitore in Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4b2294d7816a92dccb14caaadc09a2797edeafe6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36237436"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Il servizio Kubernetes di Azure (AKS) semplifica la distribuzione di un cluster Kubernetes gestito in Azure. AKS riduce la complessità e i costi operativi di gestione di Kubernetes, affidando la maggior parte di queste attività ad Azure. Come servizio Kubernetes ospitato, Azure gestisce attività critiche quali il monitoraggio dell'integrità e la manutenzione per l'utente. Inoltre, il servizio è gratuito: si paga solo per i nodi dell'agente all'interno dei cluster, non per i master.

Questo documento fornisce una panoramica delle funzionalità del servizio Kubernetes di Azure.

## <a name="flexible-deployment-options"></a>Opzioni di distribuzione flessibili

Il servizio Kubernetes di Azure offre un portale, una riga di comando e opzioni di distribuzione basate su modelli (modelli di Resource Manager e Terraform). Quando si distribuisce un cluster del servizio Kubernetes di Azure, il master Kubernetes e tutti i nodi vengono distribuiti e configurati automaticamente. Durante il processo di distribuzione è anche possibile configurare caratteristiche aggiuntive come funzionalità di rete avanzate, integrazione di Azure Active Directory e monitoraggio.

Per altre informazioni, vedere la [guida introduttiva al portale AKS][aks-portal] o la [guida introduttiva all'interfaccia della riga di comando di AKS][aks-cli].

## <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

I cluster AKS supportano il [controllo degli accessi in base al ruolo (RBAC)][kubernetes-rbac]. Un cluster AKS può anche essere configurato per l'integrazione con Azure Active Directory. In questa configurazione, l'accesso a Kubernetes può essere configurato in base all'identità di Azure Active Directory e all'appartenenza ai gruppi.

Per altre informazioni, vedere [Integrare Azure Active Directory con AKS][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Registrazione e monitoraggio integrati

L'integrità dei contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da contenitori, nodi e controller. Vengono raccolti anche i log dei contenitori. Questi dati sono archiviati nell'area di lavoro di Log Analytics e sono disponibili tramite il portale di Azure, l'interfaccia della riga di comando di Azure o un endpoint REST.

Per altre informazioni, vedere [Monitor Azure Kubernetes Service container health][container-health] (Monitorare l'integrità del contenitore del servizio Kubernetes di Azure).

## <a name="cluster-node-scaling"></a>Ridimensionamento dei nodi del cluster

Quando aumenta la richiesta di risorse, i nodi del cluster AKS possono essere incrementati in modo corrispondente. Se la richiesta di risorse si riduce, è possibile rimuovere i nodi per ridurre il cluster. Le operazioni di ridimensionamento di AKS possono essere eseguite tramite il portale di Azure o l'interfaccia della riga di comando di Azure.

Per altre informazioni, vedere [Ridimensionare un cluster del servizio Kubernetes di Azure][aks-scale].

## <a name="cluster-node-upgrades"></a>Aggiornamenti dei nodi del cluster

Il servizio Kubernetes di Azure offre più versioni di Kubernetes. Non appena sono disponibili nuove versioni in AKS, il cluster può essere aggiornato tramite il portale di Azure o l'interfaccia della riga di comando di Azure. Durante il processo di aggiornamento, i nodi vengono accuratamente contrassegnati come non pianificabili e svuotati per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.

Per altre informazioni, vedere [Aggiornare un cluster del servizio Kubernetes di Azure][aks-upgrade].

## <a name="http-application-routing"></a>Routing di applicazioni HTTP

La soluzione Routing di applicazioni HTTP semplifica l'accesso alle applicazioni distribuite nel cluster AKS. Quando abilitata, la soluzione Routing di applicazioni HTTP configura un controller di ingresso nel cluster AKS. Al momento della distribuzione delle applicazioni, i nomi DNS accessibili pubblicamente vengono configurati automaticamente.

Per altre informazioni, vedere [Routing di applicazioni HTTP][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>Nodi abilitati per la GPU

Il servizio contenitore di Azure supporta la creazione di pool di nodi abilitati per la GPU. Azure offre attualmente macchine virtuali abilitate per una GPU singola o più GPU. Le macchine virtuali abilitate per la GPU sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica.

Per altre informazioni, vedere [Uso di GPU nel servizio contenitore di Azure][aks-gpu].

## <a name="development-tooling-integration"></a>Integrazione di strumenti di sviluppo

Kubernetes dispone di un ricco ecosistema di strumenti di sviluppo e di gestione, come Helm, Draft e l'estensione di Kubernetes per Visual Studio Code. Questi strumenti si integrano in modo trasparente con il servizio Kubernetes di Azure.

Inoltre, Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team. Con una configurazione minima, è possibile eseguire i contenitori ed effettuarne il debug direttamente nel servizio Kubernetes di Azure.

Per altre informazioni, vedere [Azure Dev Spaces][azure-dev-spaces].

Il progetto DevOps di Azure offre una soluzione semplice per trasferire il codice esistente e il repository Git in Azure. Il progetto DevOps crea automaticamente le risorse di Azure come AKS, crea una pipeline di versione in Visual Studio Team Services che include una definizione di compilazione per l'integrazione continua, imposta una definizione di versione per la distribuzione continua e quindi crea una risorsa di Azure Application Insights per il monitoraggio.

Per altre informazioni, vedere [Progetto DevOps di Azure][azure-devops].

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

Un cluster AKS può essere distribuito in una rete virtuale esistente. In questa configurazione, ogni pod del cluster ottiene un indirizzo IP nella rete virtuale e può comunicare direttamente con altri pod nel cluster e con altri nodi della rete virtuale. I pod possono inoltre connettersi ad altri servizi in una rete virtuale con peering e a reti locali tramite ExpressRoute e connessioni VPN da sito a sito.

Per altre informazioni, vedere [AKS networking overview][aks-networking] (Panoramica di rete AKS).

## <a name="private-container-registry"></a>Registro contenitori privato

Eseguire l'integrazione con Registro contenitori di Azure (ACR) per l'archiviazione privata delle immagini Docker.

Per altre informazioni, vedere [Registro contenitori di Azure (ACR)][acr-docs].

## <a name="storage-volume-support"></a>Supporto di volumi di archiviazione

Il servizio Kubernetes di Azure supporta il montaggio di volumi di archiviazione per i dati persistenti. I cluster AKS vengono creati con il supporto per File di Azure e Dischi di Azure.

Per altre informazioni, vedere [File di Azure][azure-files] e [Dischi di Azure][azure-disk].

## <a name="docker-image-support"></a>Supporto delle immagini Docker

Il servizio Kubernetes di Azure supporta il formato di immagine Docker.

## <a name="kubernetes-certification"></a>Certificazione Kubernetes

Il servizio Kubernetes di Azure ha ottenuto la certificazione CNCF per la conformità a Kubernetes.

## <a name="regulatory-compliance"></a>Conformità alle normative

Il servizio Kubernetes di Azure è conforme agli standard SOC e ISO/HIPPA/HITRUST.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione del servizio contenitore di Azure sono disponibili nella relativa guida introduttiva.

> [!div class="nextstepaction"]
> [Guida introduttiva ad AKS][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/vsts/pipelines/actions/azure-devops-project-aks?view=vsts
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

