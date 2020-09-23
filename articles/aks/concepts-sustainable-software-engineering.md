---
title: Concetti-ingegneria del software sostenibile nei servizi Kubernetes di Azure (AKS)
description: Scopri di più sulla progettazione software sostenibile in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 2457de7bdaa94a6e2269515fafe6689d44960625
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984981"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Principi di progettazione software sostenibili in Azure Kubernetes Service (AKS)

I principi di progettazione software sostenibili sono un insieme di competenze che consentono di definire, compilare ed eseguire applicazioni sostenibili. L'obiettivo complessivo è quello di ridurre l'impronta di carbonio di ogni aspetto dell'applicazione. Il [progetto principis. Green][principles-green] offre una panoramica dei principi della progettazione software sostenibile.

Un'idea importante da comprendere sull'ingegneria del software sostenibile è che si tratta di un cambiamento delle priorità e dello stato attivo. In molti casi, il software viene progettato ed eseguito in modo da incentrarsi sulle prestazioni veloci e sulla bassa latenza. L'ingegneria del software sostenibile è incentrata sulla riduzione del minor consumo possibile di emissioni di CO2. In alcuni casi, l'applicazione di principi di progettazione software sostenibili può offrire prestazioni più elevate o una minore latenza, ad esempio abbassando il totale dei viaggi di rete. In altri casi, la riduzione delle emissioni di CO2 può causare un rallentamento delle prestazioni o una maggiore latenza, ad esempio ritardare i carichi di lavoro con priorità bassa. Prima di prendere in considerazione l'applicazione dei principi di progettazione software sostenibili alla propria applicazione, rivedere le priorità, le esigenze e i compromessi dell'applicazione.

## <a name="measure-and-optimize"></a>Misura e ottimizza

Per ridurre il footprint di carbonio dei cluster AKS, è necessario comprendere come vengono usate le risorse del cluster. [Monitoraggio di Azure][azure-monitor] fornisce informazioni dettagliate sull'utilizzo delle risorse del cluster, ad esempio l'utilizzo della memoria e della CPU. Questi dati possono aiutare a prendere decisioni per ridurre l'impronta di carbonio del cluster e osservare l'effetto delle modifiche. È anche possibile installare [Microsoft Sustainability Calculator][sustainability-calculator] per visualizzare l'impronta di carbonio di tutte le risorse di Azure.

## <a name="increase-resource-utilization"></a>Aumentare l'utilizzo delle risorse

Un approccio per abbassare il footprint di CO2 consiste nel ridurre la quantità di tempo di inattività per le risorse di calcolo. La riduzione del tempo di inattività comporta l'aumento dell'utilizzo delle risorse di calcolo. Se, ad esempio, si dispone di quattro nodi nel cluster, ognuno in esecuzione alla capacità del 50%, tutti e quattro i nodi hanno una capacità inutilizzata del 50% inattiva. Se il cluster è stato ridotto a tre nodi, lo stesso carico di lavoro provocherebbe l'esecuzione dei tre nodi alla capacità del 67%, riducendo la capacità inutilizzata al 33% in ogni nodo e aumentando l'utilizzo.

> [!IMPORTANT]
> Quando si considerano le modifiche apportate alle risorse nel cluster, verificare che i [pool di sistema][system-pools] dispongano di risorse sufficienti per mantenere la stabilità dei componenti di sistema principali del cluster. Non ridurre mai le risorse del cluster fino al punto in cui il cluster potrebbe diventare instabile.

Dopo aver esaminato l'utilizzo del cluster, provare a usare le funzionalità offerte da [più pool di nodi][multiple-node-pools]. È possibile usare il [ridimensionamento dei nodi][node-sizing] per definire pool di nodi con profili di CPU e memoria specifici, consentendo di personalizzare i nodi in base alle esigenze del carico di lavoro. Il dimensionamento dei nodi in base alle esigenze del carico di lavoro può essere utile per eseguire pochi nodi a un utilizzo superiore. È anche possibile configurare [la scalabilità del cluster e usare][scale] il ridimensionamento automatico del [Pod orizzontale][scale-horizontal] e il [ridimensionamento][scale-auto] automatico del cluster per ridimensionare automaticamente il cluster in base alla configurazione. Il controllo della scalabilità del cluster consente di mantenere tutti i nodi in esecuzione a un utilizzo elevato, mantenendo al tempo stesso le modifiche apportate al carico di lavoro del cluster. Per i casi in cui un carico di lavoro è tollerante a interruzioni o interruzioni improvvise, è possibile usare i [pool di spot][spot-pools] per sfruttare la capacità inattiva in Azure. Ad esempio, i pool di spot possono funzionare correttamente per i processi batch o gli ambienti di sviluppo.

L'aumento dell'utilizzo può anche ridurre i nodi in eccesso, riducendo così l'energia utilizzata dalle [prenotazioni di risorse in ogni nodo][resource-reservations].

Esaminare inoltre le *richieste* e i *limiti* di CPU e memoria nei manifesti Kubernetes delle applicazioni. Quando si abbassano i valori per la memoria e la CPU, per il cluster sono disponibili più memoria e CPU per eseguire altri carichi di lavoro. Quando si eseguono più carichi di lavoro con CPU e memoria inferiori, il cluster viene allocato in modo più denso che aumenta l'utilizzo. Quando si riducono le risorse di CPU e memoria per le applicazioni, il comportamento delle applicazioni potrebbe risultare ridotto o instabile se si impostano questi valori su un valore troppo basso. Prima di modificare le *richieste* e i *limiti*della CPU e della memoria, provare a eseguire alcuni test di benchmarking per comprendere se tali valori sono impostati in modo appropriato. Inoltre, non ridurre mai questi valori fino al momento in cui l'applicazione diventa instabile.

## <a name="reduce-network-travel"></a>Ridurre i viaggi di rete

Riducendo le richieste di distanza e le risposte da e verso il cluster, è in genere possibile ridurre il consumo di energia elettrica mediante i dispositivi di rete e ridurre le emissioni di CO2. Dopo aver esaminato il traffico di rete, è consigliabile creare cluster [in aree][regions] più vicine all'origine del traffico di rete. È anche possibile usare [Gestione traffico di Azure][azure-traffic-manager] per consentire il routing del traffico al cluster più vicino e ai [gruppi di posizionamento di prossimità][proiximity-placement-groups] per ridurre la distanza tra le risorse di Azure.

> [!IMPORTANT]
> Quando si considerano le modifiche apportate alla rete del cluster, non ridurre mai i viaggi di rete al costo di soddisfare i requisiti del carico di lavoro. Ad esempio, l'uso delle [zone di disponibilità][availability-zones] causa un maggiore viaggio di rete nel cluster, ma potrebbe essere necessario usare tale funzionalità per gestire i requisiti del carico di lavoro.

## <a name="demand-shaping"></a>Data Shaping

Laddove possibile, provare a spostare la richiesta per le risorse del cluster in orari o aree in cui è possibile usare la capacità in eccesso. Si consideri, ad esempio, la modifica dell'ora o dell'area per un processo batch per l'esecuzione o l'utilizzo di [pool di spot][spot-pools]. Prendere in considerazione anche il refactoring dell'applicazione per usare una coda per rinviare i carichi di lavoro in esecuzione che non necessitano di elaborazione immediata.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzionalità di AKS citate in questo articolo:

* [Pool di più nodi][multiple-node-pools]
* [Dimensionamento nodo][node-sizing]
* [Scalabilità di un cluster][scale]
* [Utilità di scalabilità automatica orizzontale dei pod][scale-horizontal]
* [Utilità di scalabilità automatica dei cluster][scale-auto]
* [Pool di spot][spot-pools]
* [Pool di sistema][system-pools]
* [Prenotazioni di risorse][resource-reservations]
* [Gruppi di selezione host di prossimità][proiximity-placement-groups]
* [Zone di disponibilità][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-green]: https://principles.green/