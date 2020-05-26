---
title: Concetti - Sicurezza nei servizi Azure Kubernetes
description: Informazioni sulla sicurezza nel servizio Azure Kubernetes, inclusi comunicazione master e tra nodi, criteri di rete e segreti di Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: f3c4fd922ef0e4243344b34dd90f7e48f903abcd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981392"
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

Nel servizio Azure Kubernetes i componenti master di Kubernetes fanno parte del servizio gestito fornito da Microsoft. Ogni cluster del servizio Azure Kubernetes ha un proprio master di Kubernetes dedicato con tenant singolo per fornire il server dell'API, l'utilità di pianificazione e così via. Questo master è gestito da Microsoft.

Per impostazione predefinita, il server dell'API Kubernetes usa un indirizzo IP pubblico e un nome di dominio completo (FQDN). È possibile limitare l'accesso all'endpoint del server dell'API usando [intervalli IP autorizzati][authorized-ip-ranges]. È anche possibile creare un [cluster completamente privato][private-clusters] per limitare l'accesso del server dell'API alla rete virtuale.

È possibile controllare l'accesso al server dell'API usando i controlli degli accessi in base al ruolo di Kubernetes e Azure Active Directory. Per altre informazioni, vedere [Integrazione di Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

## <a name="node-security"></a>Sicurezza dei nodi

I nodi del servizio Azure Kubernetes sono macchine virtuali di Azure gestite dall'utente. I nodi di Linux eseguono una distribuzione di Ubuntu ottimizzata usando il runtime del contenitore Moby. I nodi di Windows Server eseguono una versione ottimizzata di Windows Server 2019 e usano anche il runtime del contenitore Moby. Quando un cluster del servizio Azure Kubernetes viene creato o fatto passare a un piano superiore, i nodi vengono distribuiti automaticamente con le configurazioni e gli aggiornamenti della sicurezza del sistema operativo più recenti.

La piattaforma Azure applica automaticamente le patch di sicurezza del sistema operativo ai nodi di Linux durante la notte. Se un aggiornamento della sicurezza del sistema operativo Linux richiede un riavvio dell'host, tale riavvio non viene eseguito automaticamente. È possibile riavviare i nodi di Linux manualmente. In alternativa, un approccio comune consiste nell'usare [Kured][kured], un daemon di riavvio open source per Kubernetes. Kured viene eseguito come [DaemonSet][aks-daemonsets] e monitora ogni nodo per verificare se è presente un file che indichi che è necessario un riavvio. I riavvii sono gestiti all'interno del cluster usando lo stesso [processo di blocco e svuotamento](#cordon-and-drain) come aggiornamento del cluster.

Per i nodi di Windows Server, Windows Update non viene eseguito automaticamente per applicare gli aggiornamenti più recenti. In base a una pianificazione regolare per il ciclo di rilascio di Windows Update e per il processo di convalida, è necessario eseguire un aggiornamento sui pool di nodi di Windows Server nel cluster del servizio Azure Kubernetes. Questo processo di aggiornamento crea nodi che eseguono la versione più recente dell'immagine e delle patch di Windows Server e quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [Aggiornare un pool di nodi nel servizio Azure Kubernetes][nodepool-upgrade].

I nodi vengono distribuiti in una subnet di rete privata virtuale, senza indirizzi IP pubblici assegnati. Per motivi di gestione e risoluzione dei problemi, SSH è abilitato per impostazione predefinita. Questo accesso SSH è disponibile solo tramite l'indirizzo IP interno.

Per fornire spazio di archiviazione, i nodi usano Azure Managed Disks. Per la maggior parte delle dimensioni dei nodi delle macchine virtuali, si tratta di dischi Premium supportati da unità SSD a prestazioni elevate. I dati inattivi archiviati nei dischi gestiti vengono automaticamente crittografati all'interno della piattaforma Azure. Per migliorare la ridondanza, questi dischi vengono anche replicati in modo sicuro nel data center di Azure.

Gli ambienti Kubernetes, nel servizio Azure Kubernetes o altrove, attualmente non sono totalmente sicuri per l'utilizzo di multi-tenant ostili. Funzionalità di sicurezza aggiuntive quali i *criteri di sicurezza pod* o altri controlli degli accessi in base al ruolo (RBAC) con granularità fine per i nodi rendono più difficili gli attacchi. Tuttavia, per una vera sicurezza durante l'esecuzione di carichi di lavoro multi-tenant ostili, un hypervisor è il solo livello di sicurezza da considerare attendibile. Il dominio di sicurezza per Kubernetes diventa l'intero cluster, non un singolo nodo. Per questi tipi di carichi di lavoro multi-tenant ostili è consigliabile usare cluster fisicamente isolati. Per altre informazioni sui modi per isolare i carichi di lavoro, vedere [Procedure consigliate per l'isolamento del cluster nel servizio Azure Kubernetes][cluster-isolation].

## <a name="cluster-upgrades"></a>Aggiornamenti dei cluster

Per la sicurezza e la conformità o per usare le funzionalità più recenti, Azure offre strumenti per orchestrare l'aggiornamento di un cluster e dei componenti del servizio Azure Kubernetes. Questa orchestrazione dell'aggiornamento include sia il master che i componenti agente di Kubernetes. È possibile visualizzare un [elenco delle versioni di Kubernetes disponibili](supported-kubernetes-versions.md) per il cluster del servizio Azure Kubernetes. Per avviare il processo di aggiornamento, si specifica una di queste versioni disponibili. Azure quindi blocca e svuota in modo sicuro ogni nodo del servizio Azure Kubernetes ed esegue l'aggiornamento.

### <a name="cordon-and-drain"></a>Blocco e svuotamento

Durante il processo di aggiornamento, i nodi del servizio Azure Kubernetes vengono bloccati singolarmente dal cluster per evitare che vi vengano pianificati nuovi pod. I nodi vengono quindi svuotati e aggiornati nel modo seguente:

- Un nuovo nodo viene distribuito nel pool di nodi. Il nodo esegue l'immagine e le patch più recenti del sistema operativo.
- Uno dei nodi esistenti viene identificato per l'aggiornamento. I pod in questo nodo vengono interrotti normalmente e pianificati negli altri nodi del pool.
- Il nodo esistente viene eliminato dal cluster del servizio Azure Kubernetes.
- Il nodo successivo nel cluster viene bloccato e svuotato con lo stesso processo finché tutti i nodi non vengono sostituiti come parte del processo di aggiornamento.

Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade-cluster].

## <a name="network-security"></a>Sicurezza di rete

Per la connettività e sicurezza con le reti locali, è possibile distribuire il cluster del servizio Azure Kubernetes nelle subnet di rete virtuale di Azure esistenti. Queste reti virtuali possono avere una connessione ExpressRoute o VPN da sito a sito di Azure con la rete locale. È possibile definire controller in ingresso di Kubernetes con indirizzi IP privati interni in modo che i servizi siano accessibili solo tramite questa connessione di rete interna.

### <a name="azure-network-security-groups"></a>Gruppi di sicurezza di rete di Azure

Per filtrare il flusso del traffico nelle reti virtuali, Azure usa le regole dei gruppi di sicurezza di rete. Queste regole definiscono gli intervalli IP, le porte e i protocolli di origine e di destinazione a cui è consentito o negato l'accesso alle risorse. Vengono create regole predefinite per consentire il traffico TLS al server dell'API Kubernetes. Quando si creano servizi con servizi di bilanciamento del carico, mapping delle porte o route in ingresso, il servizio Azure Kubernetes modifica automaticamente il gruppo di sicurezza di rete per trasmettere il traffico in modo appropriato.

### <a name="kubernetes-network-policy"></a>Criteri di rete di Kubernetes

Per limitare il traffico di rete tra i pod nel cluster, il servizio Azure Kubernetes offre supporto per i [criteri di rete Kubernetes][network-policy]. Con i criteri di rete è possibile scegliere di consentire o negare percorsi di rete specifici all'interno del cluster in base agli spazi dei nomi e ai selettori di etichette.

## <a name="kubernetes-secrets"></a>Segreti di Kubernetes

Un *segreto* di Kubernetes viene usato per inserire nei pod i dati sensibili, ad esempio chiavi o credenziali di accesso. Si crea prima di tutto un segreto usando l'API di Kubernetes. Quando si definisce il pod o la distribuzione, è possibile richiedere un segreto specifico. I segreti vengono forniti solo ai nodi che hanno un pod pianificato che li richiede, perché i segreti vengono archiviati in *tmpfs* e non scritti su disco. Quando viene eliminato l'ultimo pod in un nodo che richiede un segreto, il segreto viene eliminato da tmpfs del nodo. I segreti vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo dai pod all'interno dello stesso spazio dei nomi.

L'uso dei segreti riduce le informazioni riservate definite nel pod o nel manifesto YAML del servizio. Si richiede invece il segreto archiviato nel server dell'API di Kubernetes come parte del manifesto YAML. Questo approccio fornisce solo l'accesso del pod specifico al segreto. Nota: i file manifesto del segreto non elaborato contengono i dati del segreto in formato Base64. Per altri dettagli, vedere la [documentazione ufficiale][secret-risks]. Questo file deve pertanto essere trattato come informazioni riservate e non essere mai stato sottoposto a commit nel controllo del codice sorgente.

## <a name="next-steps"></a>Passaggi successivi

Per acquisire familiarità con la protezione dei cluster del servizio Azure Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade-cluster].

Per le procedure consigliate associate, vedere [Procedure consigliate per la sicurezza e gli aggiornamenti del cluster nel servizio Azure Kubernetes][operator-best-practices-cluster-security] e [Procedure consigliate per la sicurezza dei pod nel servizio Azure Kubernetes][developer-best-practices-pod-security].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes/Cluster e carichi di lavoro del servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Kubernetes/Identità del servizio Azure Kubernetes][aks-concepts-identity]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

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
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md