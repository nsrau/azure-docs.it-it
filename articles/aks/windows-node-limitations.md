---
title: Limitazioni per i pool di nodi di Windows Server nel servizio Azure Kubernetes (AKS)Limitations for Windows Server node pools in Azure Kubernetes Service (AKS)
description: Informazioni sulle limitazioni note quando si eseguono pool di nodi di Windows Server e carichi di lavoro delle applicazioni nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: f4e9f63d0da1797b92c123034e6775f5b07bd4b3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366413"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitazioni correnti per i pool di nodi di Windows Server e i carichi di lavoro delle applicazioni nel servizio Azure Kubernetes (AKS)Current limitations for Windows Server node pools and application workloads in Azure Kubernetes Service (AKS)

Nel servizio Azure Kubernetes (AKS) è possibile creare un pool di nodi che esegue Windows Server come sistema operativo guest nei nodi. Questi nodi possono eseguire applicazioni contenitore Windows native, ad esempio quelle create in .NET Framework.These nodes can run native Windows container applications, such as those built on the .NET Framework. Poiché esistono importanti differenze nel modo in cui il sistema operativo Linux e Windows fornisce il supporto dei contenitori, alcune Kubernete comuni e funzionalità correlate ai pod non sono attualmente disponibili per i pool di nodi di Windows.

In questo articolo vengono descritte alcune delle limitazioni e concetti del sistema operativo per i nodi di Windows Server in AKS. I pool di nodi per Windows Server sono attualmente in anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono l'opt-in self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto clienti in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Quali sistemi operativi Windows sono supportati?

AKS utilizza Windows Server 2019 come versione del sistema operativo host e supporta solo l'isolamento dei processi. Le immagini contenitore create con altre versioni di Windows Server non sono supportate. [Compatibilità delle versioni dei contenitori di Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes è diverso su Windows e Linux?

Il supporto del pool di nodi di Window Server include alcune limitazioni che fanno parte del progetto Upstream Windows Server in Kubernetes. Queste limitazioni non sono specifiche di AKS. Per ulteriori informazioni su questo supporto a monte per Windows Server in Kubernetes, vedere la sezione [Funzionalità e limitazioni supportate][upstream-limitations] del documento [Intro to Windows support in Kubernetes,][intro-windows] dal progetto Kubernetes.

Kubernetes è storicamente incentrato su Linux. Molti esempi utilizzati nel sito Web [a monte Kubernetes.io][kubernetes] sono destinati all'uso su nodi Linux. Quando si creano distribuzioni che utilizzano contenitori di Windows Server, si applicano le considerazioni seguenti a livello di sistema operativo:When you create deployments that use Windows Server containers, the following considerations at the OS-level apply:

- **Identità:** Linux identifica un utente tramite un identificatore utente (UID) intero. Un utente ha anche un nome utente alfanumerico per l'accesso, che Linux traduce in UID dell'utente. Analogamente Linux identifica un gruppo di utenti in base a un identificatore di gruppo integer (GID) e converte un nome di gruppo nel GID corrispondente.
    - Windows Server utilizza un identificatore di protezione binario (SID) più grande archiviato nel database di Windows Security Access Manager (SAM). Questo database non è condiviso tra l'host e i contenitori o tra i contenitori.
- Autorizzazioni per i **file-** Windows Server utilizza un elenco di controllo di accesso basato su SID, anziché una maschera di bit delle autorizzazioni e UID-GID
- **Percorsi di file** - convenzione su Windows Server è quello di utilizzare il numero di file anziché /.
    - Nelle specifiche di pod che montano i volumi, specificate correttamente il percorso per i contenitori di Windows Server. Ad esempio, anziché un punto di montaggio di */mnt/volume* in un contenitore Linux, specificare una lettera di unità e un percorso, ad esempio */K/Volume* da montare come unità *K:.*

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Che tipo di dischi sono supportati per Windows?

Dischi di Azure e File di Azure sono i tipi di volume supportati, accessibili come volumi NTFS nel contenitore di Windows Server.Azure Disks and Azure Files are the supported volume types, accessed as NTFS volumes in the Windows Server container.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>È possibile eseguire solo cluster Windows in AKS?

I nodi master (piano di controllo) in un cluster AKS sono ospitati dal servizio AKS, non saranno esposti al sistema operativo dei nodi che ospitano i componenti master. Tutti i cluster AKS vengono creati con un pool di primo nodo predefinito, basato su Linux.All AKS cluster are created with a default first node pool, which is Linux based. Questo pool di nodi contiene i servizi di sistema necessari per il funzionamento del cluster. È consigliabile eseguire almeno due nodi nel primo pool di nodi per garantire l'affidabilità del cluster e la possibilità di eseguire operazioni del cluster. Il primo pool di nodi basato su Linux non può essere eliminato a meno che non venga eliminato il cluster AKS stesso.

## <a name="what-network-plug-ins-are-supported"></a>Quali plug-in di rete sono supportati?

I cluster AKS con pool di nodi di Windows devono usare il modello di rete Di Azure CNI (avanzato). La rete Kubenet (di base) non è supportata. Per ulteriori informazioni sulle differenze nei modelli di rete, vedere Concetti relativi alla rete per le [applicazioni in AKS][azure-network-models]. - Il modello di rete CNI di Azure richiede pianificazione e considerazioni aggiuntive per la gestione degli indirizzi IP.- The Azure CNI network model requires additional planning and considerations for IP address management. Per altre informazioni su come pianificare e implementare Azure CNI, vedere Configurare la rete CNI di [Azure in AKS.][configure-azure-cni]

## <a name="can-i-change-the-max--of-pods-per-node"></a>Posso cambiare il massimo. Numero di baccelli per nodo?

Sì. Per le implicazioni e le opzioni disponibili, consultate [Numero massimo di pod.][maximum-number-of-pods]

## <a name="how-do-patch-my-windows-nodes"></a>Come si applicano patch ai nodi di Windows?

I nodi di Windows Server in AKS devono essere *aggiornati* per ottenere le correzioni e gli aggiornamenti delle patch più recenti. Gli aggiornamenti di Windows non sono abilitati nei nodi in AKS. AKS rilascia nuove immagini del pool di nodi non appena le patch sono disponibili, è responsabilità dei clienti aggiornare i pool di nodi per rimanere aggiornati su patch e hotfix. Questo vale anche per la versione Kubernetes in uso. Le note sulla versione di AKS indicheranno quando sono disponibili nuove versioni. Per ulteriori informazioni sull'aggiornamento di un pool di nodi di Windows Server, vedere Aggiornare un pool di [nodi in AKS][nodepool-upgrade].

> [!NOTE]
> L'immagine di Windows Server aggiornata verrà utilizzata solo se è stato eseguito un aggiornamento del cluster (aggiornamento del piano di controllo) prima dell'aggiornamento del pool di nodi
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Come si ruota l'entità servizio per il pool di nodi di Windows?

Durante l'anteprima, i pool di nodi di Windows non supportano la rotazione dell'entità servizio come limitazione dell'anteprima. Per aggiornare l'entità servizio, creare un nuovo pool di nodi di Windows ed eseguire la migrazione dei pod dal pool precedente a quello nuovo. Al termine, eliminare il pool di nodi precedente.

## <a name="how-many-node-pools-can-i-create"></a>Quanti pool di nodi è possibile creare?

Il cluster AKS può avere un massimo di 10 pool di nodi. È possibile avere un massimo di 1000 nodi tra i pool di nodi. [Limitazioni del pool di nodi][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Quali sono i nomi dei pool di nodi di Windows?

È necessario mantenere il nome a un massimo di 6 (sei) caratteri. Si tratta di una limitazione corrente di AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Tutte le funzionalità sono supportate con i nodi Windows?

I criteri di rete e il kubenet non sono attualmente supportati con i nodi Windows.Network policies and kubenet are currently not supported with Windows nodes. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>È possibile eseguire controller in ingresso su nodi Windows?

Sì, un controller di ingresso che supporta i contenitori di Windows Server può essere eseguito sui nodi Windows in AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>È possibile usare Azure Dev Spaces con i nodi Windows?

Azure Dev Spaces è attualmente disponibile solo per i pool di nodi basati su Linux.Azure Dev Spaces is currently only available for Linux-based node pools.

## <a name="can-my-windows-server-containers-use-gmsa"></a>I contenitori di Windows Server possono usare gMSA?

Il supporto degli account di servizio gestiti di gruppo (gMSA) non è attualmente disponibile in AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>È possibile usare Monitoraggio di Azure per i contenitori con nodi e contenitori di Windows?

Sì, è possibile, tuttavia Monitoraggio di Azure non raccoglie i log (stdout) dai contenitori di Windows.Yes you can, however Azure Monitor does not gather logs (stdout) from Windows containers. È comunque possibile connettersi al flusso live di log di stdout da un contenitore di Windows.You can still attach to the live stream of stdout logs from a Windows container.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Cosa succede se ho bisogno di una funzione che non è supportata?

Lavoriamo sodo per portare tutte le funzionalità necessarie a Windows in AKS, ma se si verificano lacune, il progetto open-source [aks aks-engine][aks-engine] fornisce un modo semplice e completamente personalizzabile di eseguire Kubernetes in Azure, incluso il supporto di Windows. Si prega di assicurarsi di controllare la nostra roadmap di funzionalità in arrivo [AKS roadmap][aks-roadmap].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i contenitori di Windows Server in AKS, creare un pool di nodi [che esegue Windows Server in AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
