---
title: Limitazioni per i pool di nodi di Windows Server in Azure Kubernetes Service (AKS)
description: Informazioni sulle limitazioni note quando si eseguono i pool di nodi di Windows Server e i carichi di lavoro delle applicazioni in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: a0fe65428a3329d4843ec913e934fb7a91b13759
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000227"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitazioni correnti per i pool di nodi di Windows Server e i carichi di lavoro delle applicazioni in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) è possibile creare un pool di nodi che esegue Windows Server come sistema operativo guest nei nodi. Questi nodi possono eseguire applicazioni contenitore Windows native, ad esempio quelle compilate in .NET Framework. Poiché esistono differenze sostanziali nel modo in cui il sistema operativo Linux e Windows fornisce il supporto per i contenitori, alcune funzionalità comuni relative a Kubernetes e Pod non sono attualmente disponibili per i pool di nodi di Windows.

Questo articolo illustra alcune limitazioni e concetti del sistema operativo per i nodi di Windows Server in AKS. I pool di nodi per Windows Server sono attualmente in anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, consultare gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Quali sistemi operativi Windows sono supportati?

AKS usa Windows Server 2019 come versione del sistema operativo host e supporta solo l'isolamento dei processi. Le immagini del contenitore compilate con altre versioni di Windows Server non sono supportate. [Compatibilità versione contenitore Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes è diverso in Windows e Linux?

Il supporto del pool di nodi di Windows Server include alcune limitazioni che fanno parte dell'upstream di Windows Server nel progetto Kubernetes. Queste limitazioni non sono specifiche di AKS. Per ulteriori informazioni su questo supporto upstream per Windows Server in Kubernetes, vedere la sezione [funzionalità e limitazioni supportate][upstream-limitations] del documento [Introduzione al supporto Windows in Kubernetes][intro-windows] dal progetto Kubernetes.

Kubernetes è in passato basato su Linux. Molti esempi usati nel sito Web [Kubernetes.io][kubernetes] upstream sono destinati all'uso nei nodi Linux. Quando si creano distribuzioni che usano i contenitori di Windows Server, si applicano le considerazioni seguenti a livello di sistema operativo:

- **Identity** -Linux identifica un utente tramite un identificatore utente Integer (UID). Un utente dispone anche di un nome utente alfanumerico per l'accesso, che Linux converte nell'UID dell'utente. Analogamente, Linux identifica un gruppo di utenti in base a un identificatore di gruppo Integer (GID) e converte un nome di gruppo nel rispettivo GID corrispondente.
    - Windows Server utilizza un ID di sicurezza (SID) di dimensioni maggiori archiviato nel database di gestione accessi Windows (SAM). Questo database non è condiviso tra l'host e i contenitori o tra contenitori.
- **Autorizzazioni** per i file: Windows Server usa un elenco di controllo di accesso basato su Sid, anziché una maschera di bit di autorizzazioni e UID + GID
- **Percorsi file** : la convenzione in Windows Server prevede l'uso di \ anziché/.
    - In specifiche pod che montano volumi specificare il percorso corretto per i contenitori di Windows Server. Ad esempio, invece di un punto di montaggio di */mnt/volume* in un contenitore Linux, specificare una lettera di unità e un percorso come */K/volume* da montare come unità *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Quali tipi di dischi sono supportati per Windows?

I dischi di Azure e File di Azure sono i tipi di volume supportati, a cui si accede come volumi NTFS nel contenitore di Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>È possibile eseguire cluster solo Windows in AKS?

I nodi master (il piano di controllo) in un cluster AKS sono ospitati da AKS il servizio, non verranno esposti al sistema operativo dei nodi che ospitano i componenti Master. Tutti i cluster AKS vengono creati con un primo pool di nodi predefinito, basato su Linux. Questo pool di nodi contiene i servizi di sistema, necessari per il funzionamento del cluster. È consigliabile eseguire almeno due nodi nel primo pool di nodi per garantire l'affidabilità del cluster e la possibilità di eseguire operazioni di clustering. Il primo pool di nodi basato su Linux non può essere eliminato a meno che non venga eliminato il cluster AKS.

## <a name="what-network-plug-ins-are-supported"></a>Quali plug-in di rete sono supportati?

I cluster AKS con pool di nodi di Windows devono usare il modello di rete Azure CNI (Advanced). La rete Kubenet (Basic) non è supportata. Per altre informazioni sulle differenze nei modelli di rete, vedere [concetti di rete per le applicazioni in AKS][azure-network-models]. -Il modello di rete CNI di Azure richiede una pianificazione e considerazioni aggiuntive per la gestione degli indirizzi IP. Per altre informazioni su come pianificare e implementare Azure CNI, vedere [configurare la rete di Azure CNI in AKS][configure-azure-cni].

## <a name="can-i-change-the-min--of-pods-per-node"></a>È possibile modificare il numero minimo di pod per nodo?

È attualmente necessario impostare un minimo di 30 POD per garantire l'affidabilità dei cluster.

## <a name="how-do-patch-my-windows-nodes"></a>Come si applica la patch ai nodi Windows?

È necessario *aggiornare* i nodi di Windows Server in AKS per ottenere gli aggiornamenti e le correzioni di patch più recenti. Gli aggiornamenti di Windows non sono abilitati nei nodi in AKS. AKS rilascia nuove immagini del pool di nodi non appena sono disponibili le patch, è responsabilità dei clienti aggiornare i pool di nodi per restare aggiornati sulle patch e sull'hotfix. Questo vale anche per la versione di Kubernetes in uso. Le note sulla versione di AKS indicheranno quando sono disponibili nuove versioni. Per ulteriori informazioni sull'aggiornamento di un pool di nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

> [!NOTE]
> L'immagine di Windows Server aggiornata verrà usata solo se è stato eseguito un aggiornamento del cluster (aggiornamento del piano di controllo) prima di aggiornare il pool di nodi
>

## <a name="how-many-node-pools-can-i-create"></a>Quanti pool di nodi è possibile creare?

Il cluster AKS può avere un massimo di otto (8) pool di nodi. È possibile avere un massimo di 400 nodi tra i pool di nodi. [Limitazioni del pool di nodi][nodepool-limitations].

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

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Cosa accade se è necessaria una funzionalità non supportata?

Microsoft è impegnata a rendere disponibili tutte le funzionalità necessarie per Windows nel servizio contenitore di Azure, ma se si riscontrano Gap, il progetto di [motore AKS-][aks-engine] source a Monte è un metodo semplice e completamente personalizzabile per eseguire Kubernetes in Azure, incluso il supporto di Windows. Assicurarsi di consultare la roadmap delle funzionalità in arrivo per la [Roadmap AKS][aks-roadmap].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i contenitori di Windows Server in AKS, [creare un pool di nodi che esegue Windows Server in AKS][windows-node-cli].

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
[windows-container-compat]: https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#windows-server-2019-host-os-compatibility
