---
title: Domande frequenti sui pool di nodi di Windows Server
titleSuffix: Azure Kubernetes Service
description: Vedere le domande frequenti quando si eseguono i pool di nodi di Windows Server e i carichi di lavoro dell'applicazione in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: df9a4dd546ddc5944d9a282e74c2444a5161b862
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927556"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Domande frequenti sui pool di nodi di Windows Server in AKS

In Azure Kubernetes Service (AKS) è possibile creare un pool di nodi che esegue Windows Server come sistema operativo guest nei nodi. Questi nodi possono eseguire applicazioni contenitore Windows native, ad esempio quelle compilate in .NET Framework. Esistono differenze nel modo in cui il sistema operativo Linux e Windows fornisce il supporto dei contenitori. Alcune funzionalità comuni di Linux Kubernetes e Pod non sono attualmente disponibili per i pool di nodi di Windows.

Questo articolo illustra alcune delle domande frequenti e i concetti del sistema operativo per i nodi di Windows Server in AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Quali sistemi operativi Windows sono supportati?

AKS usa Windows Server 2019 come versione del sistema operativo host e supporta solo l'isolamento dei processi. Le immagini del contenitore compilate con altre versioni di Windows Server non sono supportate. Per ulteriori informazioni, vedere [compatibilità delle versioni dei contenitori di Windows][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes è diverso in Windows e Linux?

Il supporto del pool di nodi di Windows Server include alcune limitazioni che fanno parte dell'upstream di Windows Server nel progetto Kubernetes. Queste limitazioni non sono specifiche di AKS. Per ulteriori informazioni su questo supporto upstream per Windows Server in Kubernetes, vedere la sezione [funzionalità e limitazioni supportate][upstream-limitations] del documento [Introduzione al supporto Windows in Kubernetes][intro-windows] dal progetto Kubernetes.

Kubernetes è in passato basato su Linux. Molti esempi usati nel sito Web [Kubernetes.io][kubernetes] upstream sono destinati all'uso nei nodi Linux. Quando si creano distribuzioni che usano i contenitori di Windows Server, si applicano le considerazioni seguenti a livello di sistema operativo:

- **Identity** -Linux identifica un utente tramite un identificatore utente Integer (UID). Un utente dispone anche di un nome utente alfanumerico per l'accesso, che Linux converte nell'UID dell'utente. Analogamente, Linux identifica un gruppo di utenti in base a un identificatore di gruppo Integer (GID) e converte un nome di gruppo nel rispettivo GID corrispondente.
    - Windows Server utilizza un ID di sicurezza (SID) di dimensioni maggiori archiviato nel database di gestione accessi Windows (SAM). Questo database non è condiviso tra l'host e i contenitori o tra contenitori.
- **Autorizzazioni** per i file: Windows Server usa un elenco di controllo di accesso basato su Sid, anziché una maschera di bit di autorizzazioni e UID + GID
- **Percorsi file** : la convenzione in Windows Server prevede l'uso di \ anziché/.
    - In specifiche pod che montano volumi specificare il percorso corretto per i contenitori di Windows Server. Ad esempio, invece di un punto di montaggio di */mnt/volume* in un contenitore Linux, specificare una lettera di unità e un percorso come */K/volume* da montare come unità *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Quali tipi di dischi sono supportati per Windows?

I dischi di Azure e File di Azure sono i tipi di volume supportati. Questi sono accessibili come volumi NTFS nel contenitore di Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>È possibile eseguire cluster solo Windows in AKS?

I nodi master (il piano di controllo) in un cluster AKS sono ospitati da AKS il servizio, non verranno esposti al sistema operativo dei nodi che ospitano i componenti Master. Tutti i cluster AKS vengono creati con un primo pool di nodi predefinito, basato su Linux. Questo pool di nodi contiene i servizi di sistema, necessari per il funzionamento del cluster. È consigliabile eseguire almeno due nodi nel primo pool di nodi per garantire l'affidabilità del cluster e la possibilità di eseguire operazioni di clustering. Il primo pool di nodi basato su Linux non può essere eliminato a meno che non venga eliminato il cluster AKS.

## <a name="how-do-i-patch-my-windows-nodes"></a>Ricerca per categorie Patch My Windows nodes?

Per ottenere le patch più recenti per i nodi di Windows, è possibile [aggiornare il pool di nodi][nodepool-upgrade] o [aggiornare l'immagine del nodo][upgrade-node-image]. Gli aggiornamenti di Windows non sono abilitati nei nodi in AKS. AKS rilascia le nuove immagini del pool di nodi non appena sono disponibili le patch ed è responsabilità dell'utente aggiornare i pool di nodi per rimanere aggiornati su patch e hotfix. Questo vale anche per la versione di Kubernetes in uso. [Note sulla versione di AKS][aks-release-notes] indica quando sono disponibili nuove versioni. Per ulteriori informazioni sull'aggiornamento dell'intero pool di nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade]. Se si desidera solo aggiornare l'immagine del nodo, vedere [aggiornamenti dell'immagine del nodo AKS][upgrade-node-image].

> [!NOTE]
> L'immagine di Windows Server aggiornata verrà usata solo se è stato eseguito un aggiornamento del cluster (aggiornamento del piano di controllo) prima di aggiornare il pool di nodi.
>

## <a name="what-network-plug-ins-are-supported"></a>Quali plug-in di rete sono supportati?

I cluster AKS con pool di nodi di Windows devono usare il modello di rete Azure CNI (Advanced). La rete Kubenet (Basic) non è supportata. Per altre informazioni sulle differenze nei modelli di rete, vedere [concetti di rete per le applicazioni in AKS][azure-network-models]. Il modello di rete CNI di Azure richiede una pianificazione e considerazioni aggiuntive per la gestione degli indirizzi IP. Per altre informazioni su come pianificare e implementare Azure CNI, vedere [configurare la rete di Azure CNI in AKS][configure-azure-cni].

## <a name="is-preserving-the-client-source-ip-supported"></a>Si conserva l'IP di origine del client supportato?

A questo punto, la [conservazione dell'indirizzo IP di origine client][client-source-ip] non è supportata con i nodi di Windows.

## <a name="can-i-change-the-max--of-pods-per-node"></a>È possibile modificare il numero massimo di pod per nodo?

Sì. Per le implicazioni e le opzioni disponibili, vedere [numero massimo di Pod][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Perché viene visualizzato un errore quando si tenta di creare un nuovo pool di agenti Windows?

Se il cluster è stato creato prima del 2020 febbraio e non sono mai state eseguite operazioni di aggiornamento del cluster, il cluster usa ancora un'immagine Windows precedente. È possibile che sia stato visualizzato un errore simile al seguente:

"Impossibile trovare il seguente elenco di immagini a cui viene fatto riferimento nel modello di distribuzione: server di pubblicazione: MicrosoftWindowsServer, offerta: WindowsServer, SKU: 2019-datacenter-Core-smalldisk-2004, versione: più recente. https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimagePer istruzioni su come trovare le immagini disponibili, vedere.

Per correggere l'errore: 

1. Aggiornare il [piano di controllo cluster][upgrade-cluster-cp] per aggiornare l'offerta e l'autore dell'immagine.
1. Creare nuovi pool di agenti di Windows.
1. Spostare i pod di Windows dai pool di agenti di Windows esistenti ai nuovi pool di agenti di Windows.
1. Elimina i pool di agenti Windows precedenti.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Ricerca per categorie ruotare l'entità servizio per il pool di nodi Windows?

I pool di nodi di Windows non supportano la rotazione dell'entità servizio. Per aggiornare l'entità servizio, creare un nuovo pool di nodi Windows ed eseguire la migrazione dei Pod dal pool precedente a quello nuovo. Al termine dell'operazione, eliminare il pool di nodi precedente.

Usare invece identità gestite, che sono essenzialmente wrapper intorno alle entità servizio. Per altre informazioni, vedere [Usare le identità gestite nel servizio Azure Kubernetes][managed-identity].

## <a name="how-many-node-pools-can-i-create"></a>Quanti pool di nodi è possibile creare?

Il cluster del servizio Azure Kubernetes può avere un massimo di 10 pool di nodi. È possibile avere un massimo di 1000 nodi tra i pool di nodi. [Limitazioni del pool di nodi][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Cosa è possibile denominare i pool di nodi di Windows?

È necessario lasciare il nome a un massimo di 6 (sei) caratteri. Si tratta di una limitazione corrente di AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Tutte le funzionalità sono supportate con i nodi di Windows?

I criteri di rete e kubenet non sono attualmente supportati con i nodi di Windows.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>È possibile eseguire controller di ingresso nei nodi di Windows?

Sì, un controller di ingresso che supporta i contenitori di Windows Server può essere eseguito nei nodi Windows in AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>È possibile usare Azure Dev Spaces con I nodi di Windows?

Azure Dev Spaces è attualmente disponibile solo per i pool di nodi basati su Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>I contenitori di Windows Server possono usare gMSA?

Il supporto per gli account del servizio gestito del gruppo (gMSA) non è attualmente disponibile in AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>È possibile usare monitoraggio di Azure per i contenitori con i nodi e i contenitori di Windows?

Sì, è possibile, tuttavia monitoraggio di Azure è in anteprima pubblica per la raccolta di log (stdout, stderr) e le metriche dei contenitori di Windows. È anche possibile connettersi al flusso live dei log stdout da un contenitore di Windows.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Esistono limitazioni al numero di servizi in un cluster con nodi di Windows?

Un cluster con nodi Windows può avere circa 500 servizi prima che riscontri l'esaurimento delle porte.

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>È possibile usare il dashboard Web Kubernetes con i contenitori di Windows?

Sì, è possibile usare il [dashboard Web di Kubernetes][kubernetes-dashboard] per accedere alle informazioni sui contenitori di Windows, ma in questo momento non è possibile eseguire *kubectl Exec* in un contenitore Windows in esecuzione direttamente dal dashboard Web di Kubernetes. Per altri dettagli sulla connessione al contenitore Windows in esecuzione, vedere [connettere i nodi di Windows Server per la manutenzione o la risoluzione dei problemi nel cluster di Azure Kubernetes Service (AKS)][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Cosa accade se è necessaria una funzionalità non supportata?

Microsoft è impegnata a rendere disponibili tutte le funzionalità necessarie per Windows nel servizio contenitore di Azure, ma se si riscontrano Gap, il progetto di [motore AKS-][aks-engine] source a Monte è un metodo semplice e completamente personalizzabile per eseguire Kubernetes in Azure, incluso il supporto di Windows. Assicurarsi di consultare la roadmap delle funzionalità in arrivo [AKS roadmap][aks-roadmap].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i contenitori di Windows Server in AKS, [creare un pool di nodi che esegue Windows Server in AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
