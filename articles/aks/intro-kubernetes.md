---
title: Introduzione al servizio Azure Kubernetes
description: Informazioni sulle funzioni e i vantaggi del servizio Azure Kubernetes nella distribuzione e gestione delle applicazioni basate su contenitore in Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: overview
ms.date: 09/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: db6a02db3a154193a9326e2957038e5daa2faae7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992369"
---
# <a name="azure-kubernetes-service-aks"></a>Servizio Azure Kubernetes

Il servizio Azure Kubernetes semplifica la distribuzione di un cluster Kubernetes gestito in Azure. AKS riduce la complessità e i costi operativi di gestione di Kubernetes, affidando la maggior parte di queste attività ad Azure. Come servizio Kubernetes ospitato, Azure gestisce attività critiche quali il monitoraggio dell'integrità e la manutenzione per l'utente. I master di Kubernetes sono gestiti da Azure. È necessario solo gestire e mantenere i nodi agente. AKS è gratuito in quanto servizio Kubernetes gestito: si paga solo per i nodi dell'agente all'interno dei cluster e non per i master.

È possibile creare un cluster AKS nel portale di Azure, con l'interfaccia della riga di comando di Azure o mediante opzioni di distribuzione basate su modello come i modelli di Gestione risorse e Terraform. Quando si distribuisce un cluster AKS, il master Kubernetes e tutti i nodi vengono distribuiti e configurati automaticamente. Durante il processo di distribuzione è anche possibile configurare caratteristiche aggiuntive come funzionalità di rete avanzate, integrazione di Azure Active Directory e monitoraggio.

Per iniziare, completare la guida introduttiva di AKS [nel portale di Azure][aks-portal] o [con l'interfaccia della riga di comando di Azure][aks-cli].

## <a name="access-security-and-monitoring"></a>Accesso, sicurezza e monitoraggio

Per migliorare la sicurezza e la gestione, AKS consente l'integrazione con Azure Active Directory e l'uso dei controlli di accesso basato sui ruoli di Kubernetes. È anche possibile monitorare l'integrità del cluster e delle risorse.

### <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

Per limitare l'accesso alle risorse del cluster, AKS supporta il [controllo degli accessi in base al ruolo di Kubernetes][kubernetes-rbac]. Il controllo degli accessi in base al ruolo consente di controllare l'accesso alle risorse e agli spazi dei nomi di Kubernetes, nonché le autorizzazioni per tali risorse. Si può anche configurare un cluster AKS per l'integrazione con Azure Active Directory. Con l'integrazione di Azure Active Directory è possibile configurare l'accesso a Kubernetes in base all'identità e all'appartenenza ai gruppi esistenti. Gli utenti di Azure Active Directory e i gruppi esistenti possono ottenere l'accesso ad AKS con un'esperienza di accessi integrata.

Per proteggere i cluster AKS, vedere [Integrare Azure Active Directory con AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Registrazione e monitoraggio integrati

Per capire come sono le prestazioni del cluster AKS e delle applicazioni distribuite, Monitoraggio di Azure per l'integrità dei contenitori raccoglie le metriche di memoria e processore da contenitori, nodi e controller. Sono disponibili i log dei contenitori ed è anche possibile [esaminare i log master di Kubernetes][aks-master-logs]. Questi dati di monitoraggio sono archiviati in un'area di lavoro di Azure Log Analytics e sono disponibili tramite il portale di Azure, l'interfaccia della riga di comando di Azure o un endpoint REST.

Per altre informazioni, vedere [Monitorare l'integrità dei contenitori del servizio Azure Kubernetes][container-health].

## <a name="cluster-and-node"></a>Cluster e nodo

I nodi di AKS sono eseguiti in macchine virtuali di Azure. È possibile connettere una risorsa di archiviazione a nodi e pod, aggiornare i componenti cluster e usare GPU.

### <a name="cluster-node-and-pod-scaling"></a>Ridimensionamento dei nodi del cluster e dei pod

Al variare della richiesta di risorse, il numero di nodi del cluster o di pod che eseguono i servizi aumenta o diminuisce automaticamente. È possibile usare sia la scalabilità automatica orizzontale dei pod che la scalabilità automatica del cluster. Questo approccio al ridimensionamento consente al cluster AKS di adattarsi automaticamente alle richieste eseguendo solo le risorse necessarie.

Per altre informazioni, vedere [Ridimensionare un cluster del servizio Azure Kubernetes][aks-scale].

### <a name="cluster-node-upgrades"></a>Aggiornamenti dei nodi del cluster

Il servizio Azure Kubernetes offre più versioni di Kubernetes. Non appena sono disponibili nuove versioni in AKS, il cluster può essere aggiornato tramite il portale di Azure o l'interfaccia della riga di comando di Azure. Durante il processo di aggiornamento, i nodi vengono accuratamente contrassegnati come non pianificabili e svuotati per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.

Per altre informazioni sulle versioni del ciclo di vita, vedere [Supported Kubernetes versions in AKS][aks-supported versions] (Versioni di Kubernetes supportate in AKS). Per la procedura di aggiornamento, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nodi abilitati per la GPU

Il servizio contenitore di Azure supporta la creazione di pool di nodi abilitati per la GPU. Azure offre attualmente macchine virtuali abilitate per una GPU singola o più GPU. Le macchine virtuali abilitate per la GPU sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica.

Per altre informazioni, vedere [Uso di GPU nel servizio contenitore di Azure][aks-gpu].

### <a name="storage-volume-support"></a>Supporto di volumi di archiviazione

Per supportare carichi di lavoro applicativi, possono essere montati volumi di archiviazione per dati persistenti. È possibile usare volumi sia statici che dinamici. A seconda di quanti pod connessi devono condividere la risorsa di archiviazione, è possibile usare risorse di archiviazione basate su dischi di Azure per l'accesso da un singolo pod o File di Azure per l'accesso da parte di più pod simultaneamente.

Introduzione ai volumi permanenti dinamici con [dischi di Azure][azure-disk] o [File di Azure][azure-files].

## <a name="virtual-networks-and-ingress"></a>Reti virtuali e ingresso

Un cluster AKS può essere distribuito in una rete virtuale esistente. In questa configurazione ogni pod del cluster ottiene un indirizzo IP nella rete virtuale e può comunicare direttamente con altri pod nel cluster e con altri nodi della rete virtuale. I pod possono inoltre connettersi ad altri servizi in una rete virtuale con peering e a reti locali tramite ExpressRoute o connessioni VPN da sito a sito (S2S).

Per altre informazioni, vedere [AKS networking overview][aks-networking] (Panoramica di rete AKS).

### <a name="ingress-with-http-application-routing"></a>Ingresso con routing di applicazioni HTTP

Il componente aggiuntivo per il routing di applicazioni HTTP semplifica l'accesso alle applicazioni distribuite nel cluster AKS. Quando abilitata, la soluzione Routing di applicazioni HTTP configura un controller di ingresso nel cluster AKS. Al momento della distribuzione delle applicazioni, i nomi DNS accessibili pubblicamente vengono configurati automaticamente. Il routing di applicazioni HTTP consente di configurare una zona DNS e la integra con il cluster AKS. È quindi possibile distribuire le risorse Kubernetes in ingresso come di consueto.

Per iniziare a usare il traffico in ingresso, vedere [Routing di applicazioni HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrazione di strumenti di sviluppo

Kubernetes dispone di un ricco ecosistema di strumenti di sviluppo e di gestione, come Helm, Draft e l'estensione di Kubernetes per Visual Studio Code. Questi strumenti funzionano perfettamente con AKS.

Inoltre, Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team. Con una configurazione minima è possibile eseguire i contenitori ed effettuarne il debug direttamente in AKS. Per iniziare, vedere[Azure Dev Spaces][azure-dev-spaces].

Il progetto DevOps di Azure offre una soluzione semplice per trasferire il codice esistente e il repository Git in Azure. Il progetto DevOps crea automaticamente le risorse di Azure come AKS, una pipeline di versione in Azure DevOps Services che include una pipeline di compilazione per l'integrazione continua, configura una pipeline di versione per la distribuzione continua e quindi crea una risorsa di Application Insights di Azure per il monitoraggio.

Per altre informazioni, vedere [Progetto DevOps di Azure][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Supporto per le immagini Docker e il registro contenitori privato

AKS supporta il formato di immagine Docker. Per l'archiviazione privata delle immagini Docker è possibile integrare AKS con il Registro contenitori di Azure (ACR).

Per creare l'archivio immagini privato, vedere [Registro contenitori di Azure][acr-docs].

## <a name="kubernetes-certification"></a>Certificazione Kubernetes

Il servizio Azure Kubernetes ha ottenuto la certificazione CNCF per la conformità a Kubernetes.

## <a name="regulatory-compliance"></a>Conformità alle normative

Il servizio Azure Kubernetes è conforme agli standard SOC, ISO, PCI DSS e HIPAA.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione di AKS sono disponibili nella guida introduttiva sull'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Guida introduttiva ad AKS][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
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
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
