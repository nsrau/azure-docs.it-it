---
title: Limitazioni per i pool di nodi di Windows Server in Azure Kubernetes Service (AKS)
description: Informazioni sulle limitazioni note quando si eseguono i pool di nodi di Windows Server e i carichi di lavoro delle applicazioni in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 501aeb60eba1d94b4c5882a7c6cbfa8d0359e44d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033902"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitazioni correnti per i pool di nodi di Windows Server e i carichi di lavoro delle applicazioni in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) è possibile creare un pool di nodi che esegue Windows Server come sistema operativo guest nei nodi. Questi nodi possono eseguire applicazioni contenitore Windows native, ad esempio quelle compilate in .NET Framework. Poiché esistono differenze sostanziali nel modo in cui il sistema operativo Linux e Windows fornisce il supporto per i contenitori, alcune funzionalità comuni relative a Kubernetes e Pod non sono attualmente disponibili per i pool di nodi di Windows.

Questo articolo illustra alcune limitazioni e concetti del sistema operativo per i nodi di Windows Server in AKS. I pool di nodi per Windows Server sono attualmente in anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitazioni per Windows Server in Kubernetes

I contenitori di Windows Server devono essere eseguiti in un host contenitore basato su Windows. Per eseguire i contenitori di Windows Server in AKS, è possibile [creare un pool di nodi che esegue Windows Server][windows-node-cli] come sistema operativo guest. Il supporto del pool di nodi di Windows Server include alcune limitazioni che fanno parte dell'upstream di Windows Server nel progetto Kubernetes. Queste limitazioni non sono specifiche di AKS. Per ulteriori informazioni su questo supporto upstream per Windows Server in Kubernetes, vedere la pagina relativa ai [contenitori di Windows Server in limitazioni di Kubernetes](https://docs.microsoft.com/azure/aks/windows-node-limitations).

Le seguenti limitazioni upstream per i contenitori di Windows Server in Kubernetes sono rilevanti per AKS:

- I contenitori di Windows Server possono usare solo Windows Server 2019, che corrisponde al sistema operativo nodo Windows Server sottostante.
    - Le immagini del contenitore compilate con Windows Server 2016 come sistema operativo di base non sono supportate.
- Non è possibile usare i contenitori con privilegi.
- Funzionalità specifiche di Linux, ad esempio le funzionalità RunAsUser, SELinux, AppArmor o POSIX, non sono disponibili nei contenitori di Windows Server.
    - Le limitazioni del file System specifiche di Linux, ad esempio UUI/GUID, non sono disponibili anche per le autorizzazioni per utente nei contenitori di Windows Server.
- I dischi di Azure e File di Azure sono i tipi di volume supportati, a cui si accede come volumi NTFS nel contenitore di Windows Server.
    - I volumi e l'archiviazione basati su NFS non sono supportati.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitazioni di AKS per i pool di nodi di Windows Server

Le limitazioni aggiuntive seguenti si applicano al supporto del pool di nodi di Windows Server in AKS:

- Un cluster AKS contiene sempre un pool di nodi Linux come primo pool di nodi. Il primo pool di nodi basato su Linux non può essere eliminato a meno che non venga eliminato il cluster AKS.
- I cluster AKS devono usare il modello di rete CNI (Advanced) di Azure.
    - La rete Kubenet (Basic) non è supportata. Non è possibile creare un cluster AKS che usa kubenet. Per altre informazioni sulle differenze nei modelli di rete, vedere [concetti di rete per le applicazioni in AKS][azure-network-models].
    - Il modello di rete CNI di Azure richiede una pianificazione e considerazioni aggiuntive per la gestione degli indirizzi IP. Per altre informazioni su come pianificare e implementare Azure CNI, vedere [configurare la rete di Azure CNI in AKS][configure-azure-cni].
- I nodi di Windows Server in AKS devono essere *aggiornati* a una versione più recente di windows server 2019 per gestire gli aggiornamenti e le correzioni di patch più recenti. Gli aggiornamenti di Windows non sono abilitati nell'immagine del nodo di base in AKS. In base a una pianificazione regolare per il ciclo di rilascio Windows Update e per il processo di convalida, è necessario eseguire un aggiornamento sui pool di nodi di Windows Server nel cluster AKS. Per ulteriori informazioni sull'aggiornamento di un pool di nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].
    - Questi aggiornamenti dei nodi di Windows Server utilizzano temporaneamente indirizzi IP aggiuntivi nella subnet della rete virtuale quando viene distribuito un nuovo nodo, prima della rimozione del nodo precedente.
    - anche le quote vCPU vengono utilizzate temporaneamente nella sottoscrizione quando viene distribuito un nuovo nodo, quindi il nodo precedente è stato rimosso.
    - Non è possibile aggiornare e gestire automaticamente i riavvii usando `kured` come per i nodi Linux in AKS.
- Il cluster AKS può avere un massimo di otto pool di nodi.
    - È possibile avere un massimo di 400 nodi tra gli otto pool di nodi.
- Il nome del pool di nodi di Windows Server ha un limite di 6 caratteri.
- Le funzionalità in anteprima in AKS, ad esempio i criteri di rete e il ridimensionatore automatico cluster, non sono approvate per i nodi di Windows Server.
- I controller in ingresso devono essere pianificati solo nei nodi Linux usando un NodeSelector.
- Azure Dev Spaces è attualmente disponibile solo per i pool di nodi basati su Linux.
- Il supporto per gli account del servizio gestito del gruppo (gMSA) quando i nodi di Windows Server non sono aggiunti a un dominio Active Directory non è attualmente disponibile in AKS.
    - Il progetto open source e upstream del [motore AKS][aks-engine] fornisce attualmente il supporto per gMSA se è necessario usare questa funzionalità.

## <a name="os-concepts-that-are-different"></a>Concetti del sistema operativo diversi

Kubernetes è in passato basato su Linux. Molti esempi usati nel sito Web [Kubernetes.io][kubernetes] upstream sono destinati all'uso nei nodi Linux. Quando si creano distribuzioni che usano i contenitori di Windows Server, si applicano le considerazioni seguenti a livello di sistema operativo:

- **Identity** -Linux usa UserID (UID) e GROUPID (GID), rappresentati come tipi Integer. I nomi di utenti e gruppi non sono canonici. si tratta semplicemente di un alias in */etc/groups* o */etc/passwd* di nuovo a UID + GID.
    - Windows Server utilizza un ID di sicurezza (SID) di dimensioni maggiori archiviato nel database di gestione accessi Windows (SAM). Questo database non è condiviso tra l'host e i contenitori o tra contenitori.
- **Autorizzazioni** per i file: Windows Server usa un elenco di controllo di accesso basato su Sid, anziché una maschera di bit di autorizzazioni e UID + GID
- **Percorsi file** : la convenzione in Windows Server prevede l'uso di \ anziché/.
    - In specifiche pod che montano volumi specificare il percorso corretto per i contenitori di Windows Server. Ad esempio, invece di un punto di montaggio di */mnt/volume* in un contenitore Linux, specificare una lettera di unità e un percorso come */K/volume* da montare come unità *K:* .

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i contenitori di Windows Server in AKS, [creare un pool di nodi che esegue Windows Server in AKS][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
