---
title: Limitazioni per il pool di nodi di Windows Server in Azure Kubernetes Service (AKS)
description: Informazioni sulle limitazioni note quando si esegue il pool di nodi di Windows Server e carichi di lavoro dell'applicazione in Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475412"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitazioni correnti per il pool di nodi di Windows Server e carichi di lavoro dell'applicazione in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS), è possibile creare un pool di nodi che esegue Windows Server come sistema operativo guest nei nodi. Questi nodi possono eseguire applicazioni contenitore Windows native, ad esempio quelle create in .NET Framework. Poiché sono presenti le principali differenze nella modalità di Linux e del sistema operativo Windows fornisce il supporto dei contenitori, alcuni Kubernetes comuni e funzionalità correlate a pod non sono attualmente disponibili per il pool di nodi di Windows.

Questo articolo illustra alcune delle limitazioni e i concetti del sistema operativo per i nodi di Windows Server nel servizio contenitore di AZURE. Pool di nodi per Windows Server sono attualmente in anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti sul supporto di Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitazioni per Server Windows in Kubernetes

Contenitori di Windows Server devono eseguire in un host contenitore basato su Windows. Per eseguire contenitori Windows Server nel servizio contenitore di AZURE, è possibile [creare un pool di nodi che esegue Windows Server] [ windows-node-cli] come il sistema operativo guest. Supporto del pool di nodi Server finestra include alcune limitazioni che fanno parte di Windows Server a monte nel progetto di Kubernetes. Queste limitazioni non sono specifiche di AKS. Per altre informazioni su questo supporto upstream per Windows Server in Kubernetes, vedere [i contenitori di Windows Server in Kubernetes limitazioni][upstream-limitations].

Le seguenti limitazioni upstream per i contenitori di Windows Server in Kubernetes sono pertinenti al servizio contenitore di AZURE:

- Contenitori di Windows Server possono usare solo Windows Server 2019, che corrisponde al nodo Windows Server sottostante del sistema operativo.
    - Immagini contenitore create usando Windows Server 2016 come sistema operativo di base non è supportato.
- I contenitori con privilegi non possono essere usati.
- Funzionalità specifiche di Linux, ad esempio RunAsUser, SELinux, AppArmor o POSIX funzionalità non sono disponibili nei contenitori di Windows Server.
    - Limitazioni del file system che sono specifici per Linux, ad esempio UUI/GUID, per le autorizzazioni utente anche non sono disponibili nei contenitori di Windows Server.
- File di Azure e dischi di Azure sono i tipi di volume supportati, accessibili come volumi NTFS nel contenitore di Windows Server.
    - Archiviazione basata su NFS / volumi non sono supportati.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitazioni del servizio contenitore di AZURE per i pool di nodi di Windows Server

Le seguenti limitazioni aggiuntive si applicano al supporto di pool di nodi di Windows Server nel servizio contenitore di AZURE:

- Un cluster AKS contiene sempre un pool di nodi di Linux come il primo pool di nodi. Impossibile eliminare questo primo pool di nodi basati su Linux, a meno che non viene eliminato il cluster di AKS.
- Servizio contenitore di AZURE supporta attualmente solo il servizio di bilanciamento del carico di base, che consente solo per il pool back-uno end, il pool di nodi di Linux predefinita. Di conseguenza, il traffico in uscita dai POD Windows sarà sempre [convertito in un indirizzo IP pubblico gestito da Azure][azure-outbound-traffic]. Poiché questo indirizzo IP non è configurabile, non è attualmente possibile per consentire il traffico proveniente da POD di Windows. 
- I cluster AKS devono utilizzare il modello di rete (avanzato) di Azure CNI.
    - Rete Kubenet (basic) non è supportata. È possibile creare un cluster servizio contenitore di AZURE che usa kubenet. Per altre informazioni sulle differenze nei modelli di rete, vedere [concetti per le applicazioni nel servizio contenitore di AZURE di rete][azure-network-models].
    - Il modello di rete di Azure CNI richiede ulteriori informazioni sulla pianificazione e considerazioni per la gestione degli indirizzi IP. Per altre informazioni su come pianificare e implementare CNI di Azure, vedere [networking configurare CNI Azure nel servizio contenitore di AZURE][configure-azure-cni].
- Nodi di Windows Server nel servizio contenitore di AZURE devono essere *aggiornato* a una versione più recente di Windows Server 2019 per mantenere la patch più recente correzioni e aggiornamenti apportati. Gli aggiornamenti di Windows non sono abilitati nell'immagine di nodo di livello base nel servizio contenitore di AZURE. A intervalli regolari tutto il ciclo di rilascio di Windows Update e il proprio processo di convalida, è consigliabile eseguire un aggiornamento sul pool di nodi di Windows Server nel cluster AKS. Per altre informazioni sull'aggiornamento di un pool di nodi di Windows Server, vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].
    - Questi aggiornamenti del nodo Windows Server utilizzano temporaneamente altri indirizzi IP nella subnet della rete virtuale perché viene distribuito un nuovo nodo, prima della rimozione del nodo precedente.
    - le quote di vCPU anche temporaneamente vengono utilizzate nella sottoscrizione mentre viene distribuito un nuovo nodo, quindi rimossa il nodo precedente.
    - Non è possibile automaticamente aggiornare e gestire i riavvii usando `kured` come nodi di Linux nel servizio contenitore di AZURE.
- Il cluster AKS può avere un massimo di otto pool di nodi.
    - Si può avere un massimo di 400 nodi tra tali pool di otto nodi.
- Il nome del pool di nodi Windows Server ha un limite di 6 caratteri.
- Anteprima funzionalità nel servizio contenitore di AZURE, ad esempio criteri di rete e scalabilità automatica di cluster, non vengono approvate per i nodi di Windows Server.
- Controller di ingresso deve essere pianificato solo su nodi Linux tramite un NodeSelector.
- Spazi di sviluppo Azure è attualmente disponibili solo per i pool di nodi basati su Linux.
- Raggruppare gli account del servizio gestito (gMSA) supporto quando i nodi di Windows Server non sono aggiunti a un dominio di Active Directory non è attualmente disponibile nel servizio contenitore di AZURE.
    - Open source, a monte [aks-engine] [ aks-engine] progetto fornisce attualmente supporto gMSA se è necessario usare questa funzionalità.

## <a name="os-concepts-that-are-different"></a>Concetti del sistema operativo che sono diversi

Kubernetes è sempre incentrato su Linux. Molti esempi usati nell'origine upstream [Kubernetes.io] [ kubernetes] sito Web sono destinati all'uso nei nodi Linux. Quando si crea le distribuzioni che usano contenitori di Windows Server, le considerazioni seguenti in si applicano le condizioni a livello del sistema operativo:

- **Identità** -Linux Usa ID utente (UID) e ID del gruppo (GID), rappresentati come tipi integer. Nomi utente e gruppo non sono canonici, ma sono semplicemente un alias nel */e così via o i gruppi* oppure *passwd/e cosìvia/* al UID + GID.
    - Windows Server utilizza un identificatore di sicurezza binario più grande (SID) che viene archiviato nel database di Windows Security Access Manager (SAM). Questo database non viene condivisa tra l'host e contenitori o tra i contenitori.
- **Le autorizzazioni file** -Windows Server utilizza un elenco di controllo di accesso basato su SID, anziché una maschera di bit delle autorizzazioni e UID + GID
- **Percorsi di file** -convenzione in Windows Server consiste nell'usare \ anziché /.
    - Nelle specifiche dei pod che montare i volumi, specificare il percorso corretto per i contenitori di Windows Server. Ad esempio, invece di un montaggio punti della */mnt/Retention/ volume* in un contenitore Linux, specificare una lettera di unità e il percorso, ad esempio *K/Volume* montare come il *k:* unità.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i contenitori di Windows Server nel servizio contenitore di AZURE, [creare un pool di nodi che esegue Windows Server in AKS][windows-node-cli].

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
