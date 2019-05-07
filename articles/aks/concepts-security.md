---
title: Concetti - Sicurezza nei servizi Kubernetes di Azure
description: Informazioni sulla sicurezza nel servizio Azure Kubernetes, inclusi comunicazione master e tra nodi, criteri di rete e segreti di Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 2e655627267546d88f76a2487817bca3153ee91d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074027"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes

Per proteggere i dati dei clienti durante l'esecuzione di carichi di lavoro dell'applicazione nel servizio Azure Kubernetes, la sicurezza del cluster è un fattore fondamentale. Kubernetes include componenti di sicurezza, ad esempio *criteri di rete* e *segreti*. Azure aggiunge quindi componenti come i gruppi di sicurezza di rete e gli aggiornamenti del cluster orchestrati. Questi componenti di sicurezza vengono combinati in modo che il cluster del servizio Azure Kubernetes esegua sempre gli aggiornamenti della sicurezza del sistema operativo e le versioni di Kubernetes più recenti, con il traffico di pod e l'accesso sicuri alle credenziali sensibili.

Questo articolo introduce i principali concetti per proteggere le applicazioni nel servizio Azure Kubernetes:

- [Sicurezza dei componenti master](#master-security)
- [Sicurezza dei nodi](#node-security)
- [Aggiornare un cluster di Service Fabric](#cluster-upgrades)
- [Sicurezza di rete](#network-security)
- [Segreti di Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Sicurezza master

Nel servizio Azure Kubernetes i componenti master di Kubernetes fanno parte del servizio gestito fornito da Microsoft. Ogni cluster del servizio Azure Kubernetes ha un proprio master di Kubernetes dedicato con tenant singolo per fornire il server dell'API, l'utilità di pianificazione e così via. Questo schema è gestito e gestito da Microsoft.

Per impostazione predefinita, il server dell'API Kubernetes usa un indirizzo IP pubblico con nome di dominio completo (FQDN). È possibile controllare l'accesso al server dell'API usando i controlli degli accessi in base al ruolo di Kubernetes e Azure Active Directory. Per altre informazioni, vedere [Integrazione di Azure AD con il servizio Azure Kubernetes][aks-aad].

## <a name="node-security"></a>Sicurezza dei nodi

I nodi del servizio Azure Kubernetes sono macchine virtuali di Azure gestite dall'utente. Eseguire una distribuzione Ubuntu ottimizzata tramite il runtime del contenitore Moby i nodi Linux. Nodi di Windows Server, attualmente in anteprima nel servizio contenitore di AZURE, Esegui un 2019 Server Windows con ottimizzazione per la versione e anche usano il runtime del contenitore Moby. Quando un cluster del servizio Azure Kubernetes viene creato o fatto passare a un piano superiore, i nodi vengono distribuiti automaticamente con le configurazioni e gli aggiornamenti della sicurezza del sistema operativo più recenti.

La piattaforma Azure applica automaticamente le patch di sicurezza del sistema operativo a nodi Linux ogni notte. Se un aggiornamento della sicurezza del sistema operativo Linux è necessario riavviare il computer host, tale riavvio non viene eseguito automaticamente. È possibile riavviare manualmente i nodi Linux, o un approccio comune consiste nell'usare [Kured][kured], un daemon di riavvio open source per Kubernetes. Kured, eseguito come [DaemonSet][aks-daemonsets], esegue il monitoraggio di ogni nodo per verificare la presenza di un file che indica che è necessario un riavvio. I riavvii sono gestiti all'interno del cluster usando lo stesso [processo di blocco e svuotamento](#cordon-and-drain) come aggiornamento del cluster.

Per i nodi Windows Server (attualmente in anteprima nel servizio contenitore di AZURE), Windows Update automaticamente eseguire e applicare gli aggiornamenti più recenti. A intervalli regolari tutto il ciclo di rilascio di Windows Update e il proprio processo di convalida, è consigliabile eseguire un aggiornamento sul pool di nodi di Windows Server nel cluster AKS. Questo processo di aggiornamento crea i nodi che eseguono l'immagine più recente di Windows Server e le patch, quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].

I nodi vengono distribuiti in una subnet di rete privata virtuale, senza indirizzi IP pubblici assegnati. Per motivi di gestione e risoluzione dei problemi, SSH è abilitato per impostazione predefinita. Questo accesso SSH è disponibile solo tramite l'indirizzo IP interno.

Per fornire spazio di archiviazione, i nodi usano Azure Managed Disks. Per la maggior parte delle dimensioni dei nodi delle macchine virtuali, si tratta di dischi Premium supportati da unità SSD a prestazioni elevate. I dati inattivi archiviati nei dischi gestiti vengono automaticamente crittografati all'interno della piattaforma Azure. Per migliorare la ridondanza, questi dischi vengono anche replicati in modo sicuro nel data center di Azure.

Gli ambienti Kubernetes, nel servizio Azure Kubernetes o altrove, attualmente non sono totalmente sicuri per l'utilizzo di multi-tenant ostili. Funzionalità di sicurezza aggiuntive quali i *criteri di sicurezza pod* o altri controlli degli accessi in base al ruolo (RBAC) con granularità fine per i nodi rendono più difficili gli attacchi. Tuttavia, per una vera sicurezza durante l'esecuzione di carichi di lavoro multi-tenant ostili, un hypervisor è il solo livello di sicurezza da considerare attendibile. Il dominio di sicurezza per Kubernetes diventa l'intero cluster, non un singolo nodo. Per questi tipi di carichi di lavoro multi-tenant ostili è consigliabile usare cluster fisicamente isolati. Per altre informazioni sui modi per isolare i carichi di lavoro, consultare [Procedure consigliate per l'isolamento del cluster nel servizio Azure Kubernetes][cluster-isolation].

## <a name="cluster-upgrades"></a>Aggiornamenti dei cluster

Per la sicurezza e la conformità o per usare le funzionalità più recenti, Azure offre strumenti per orchestrare l'aggiornamento di un cluster e dei componenti del servizio Kubernetes di Azure. Questa orchestrazione dell'aggiornamento include sia il master che i componenti agente di Kubernetes. È possibile visualizzare un [elenco delle versioni di Kubernetes disponibili](supported-kubernetes-versions.md) per il cluster del servizio Azure Kubernetes. Per avviare il processo di aggiornamento, si specifica una di queste versioni disponibili. Azure quindi blocca e svuota in modo sicuro ogni nodo del servizio Azure Kubernetes ed esegue l'aggiornamento.

### <a name="cordon-and-drain"></a>Blocco e svuotamento

Durante il processo di aggiornamento, i nodi AKS vengono singolarmente contrassegnati come non pianificabili dal cluster in modo che non sono pianificati nuovi POD su di essi. I nodi vengono quindi svuotati e aggiornati nel modo seguente:

- Un nuovo nodo viene distribuito nel pool di nodi. Questo nodo viene eseguito l'immagine del sistema operativo più recente e le patch.
- Uno dei nodi esistenti specificato per l'aggiornamento. I POD in questo nodo sono normalmente terminati e pianificati su altri nodi nel pool di nodi.
- Questo nodo esistente viene eliminato dal cluster AKS.
- Il nodo successivo nel cluster è contrassegnati come non pianificabili e svuotati usando lo stesso processo fino a quando tutti i nodi sono stati sostituiti correttamente come parte del processo di aggiornamento.

Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade-cluster].

## <a name="network-security"></a>Sicurezza di rete

Per la connettività e sicurezza con le reti locali, è possibile distribuire il cluster del servizio Kubernetes di Azure nelle subnet di rete virtuale di Azure esistenti. Queste reti virtuali possono avere una connessione ExpressRoute o VPN da sito a sito di Azure con la rete locale. È possibile definire controller in ingresso di Kubernetes con indirizzi IP privati interni in modo che i servizi siano accessibili solo tramite questa connessione di rete interna.

### <a name="azure-network-security-groups"></a>Gruppi di sicurezza di rete di Azure

Per filtrare il flusso del traffico nelle reti virtuali, Azure usa le regole dei gruppi di sicurezza di rete. Queste regole definiscono gli intervalli IP, le porte e i protocolli di origine e di destinazione a cui è consentito o negato l'accesso alle risorse. Per consentire il traffico TLS per il server API Kubernetes vengono create regole predefinite. Quando si creano servizi con servizi di bilanciamento del carico, mapping delle porte o route in ingresso, il servizio Azure Kubernetes modifica automaticamente il gruppo di sicurezza di rete per trasmettere il traffico in modo appropriato.

## <a name="kubernetes-secrets"></a>Segreti di Kubernetes

Un *segreto* di Kubernetes viene usato per inserire nei pod i dati sensibili, ad esempio chiavi o credenziali di accesso. Si crea prima di tutto un segreto usando l'API di Kubernetes. Quando si definisce il pod o la distribuzione, è possibile richiedere un segreto specifico. I segreti vengono forniti solo ai nodi che hanno un pod pianificato che li richiede, perché i segreti vengono archiviati in *tmpfs* e non scritti su disco. Quando viene eliminato l'ultimo pod in un nodo che richiede un segreto, il segreto viene eliminato da tmpfs del nodo. I segreti vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo dai pod all'interno dello stesso spazio dei nomi.

L'uso dei segreti riduce le informazioni riservate definite nel pod o nel manifesto YAML del servizio. Si richiede invece il segreto archiviato nel server dell'API di Kubernetes come parte del manifesto YAML. Questo approccio fornisce solo l'accesso del pod specifico al segreto.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a proteggere i cluster del servizio Azure Kubernetes, vedere [Aggiornare un cluster del servizio Azure Container][aks-upgrade-cluster].

Per procedure consigliate associati, vedere [procedure consigliate per la sicurezza del cluster e aggiornamenti nel servizio contenitore di AZURE][operator-best-practices-cluster-security].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes / Cluster servizio Azure Kubernetes e carichi di lavoro][aks-concepts-clusters-workloads]
- [Kubernetes / Identità di servizio Azure Kubernetes][aks-concepts-identity]
- [Kubernetes / Reti virtuali in servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes / Archiviazione in servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes / Ridimensionamento in AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
