---
title: Pianificare una distribuzione di cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni sulla pianificazione e preparazione per una distribuzione di cluster di Service Fabric in Azure di produzione.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663239"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Pianificare e preparare una distribuzione cluster

Pianificazione e preparazione per una distribuzione di cluster di produzione è molto importante.  Esistono molti fattori da considerare.  Questo articolo illustra i passaggi per preparare la distribuzione del cluster.

## <a name="read-the-best-practices-information"></a>Leggere le informazioni sulle procedure ottimali
Per gestire correttamente i cluster e applicazioni di Azure Service Fabric, sono presenti operazioni che è altamente consigliabile che eseguire per ottimizzare l'affidabilità dell'ambiente di produzione.  Per altre informazioni, leggere [procedure consigliate per cluster e applicazione di Service Fabric](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Selezionare il sistema operativo per il cluster
Service Fabric permette di creare cluster di Service Fabric in qualsiasi VM o computer con Windows Server o Linux in esecuzione.  Prima di distribuire il cluster, è necessario scegliere il sistema operativo:  Windows o Linux.  Ogni nodo del cluster (macchina virtuale) viene eseguito lo stesso sistema operativo, è possibile combinare le macchine virtuali Linux e Windows nello stesso cluster.

## <a name="capacity-planning"></a>Pianificazione della capacità
La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di produzione. Di seguito sono elencati alcuni aspetti da considerare nell'ambito di questo processo.

* Il numero iniziale di tipi di nodo per il cluster 
* Le proprietà di ogni tipo di nodo (dimensione, numero di istanze, primarie, con connessione internet, numero di VM e così via).
* Caratteristiche di affidabilità e durabilità del cluster

### <a name="select-the-initial-number-of-node-types"></a>Selezionare il numero iniziale di tipi di nodo
Prima di tutto è necessario stabilire per che cosa verrà usato il cluster che si sta creando. Che tipo di applicazioni si prevede di distribuire nel cluster? L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet? I servizi (che costituiscono l'applicazione) hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Un cluster di Service Fabric può essere costituito da più di un tipo di nodo: un tipo di nodo primario e uno o più tipi di nodo non primario. Di ogni tipo di nodo viene eseguito il mapping a un set di scalabilità di macchine virtuali. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. [Proprietà dei nodi e i vincoli di posizionamento] [ placementconstraints] può essere impostato per vincolare i servizi specifici per i tipi di nodo specifico.  Per altre informazioni, leggere [il numero di tipi di nodo del cluster deve iniziare con](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selezionare le proprietà di nodo per ogni tipo di nodo
Tipi di nodo definiscono le SKU di VM, numero e le proprietà delle macchine virtuali nel set di scalabilità associato.

Le dimensioni minime delle macchine virtuali per ogni tipo di nodo sono determinata dal [livello di durabilità] [ durability] scelto per il tipo di nodo.

Il numero minimo di macchine virtuali per il tipo di nodo primario è determinato dal [livello di affidabilità] [ reliability] scelto.

Vedere le raccomandazioni per minimo [tipi di nodo primario](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [carichi di lavoro con stati in tipi di nodo non primario](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), e [carichi di lavoro senza stati nei tipi di nodo non primario](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Più rispetto al numero minimo di nodi deve essere basata sul numero di repliche dell'applicazione o dei servizi che si desidera eseguire in questo tipo di nodo.  [Pianificazione della capacità per le applicazioni di Service Fabric](service-fabric-capacity-planning.md) consente stimare le risorse necessarie per eseguire le applicazioni. È sempre possibile aumentare il cluster o verso il basso in un secondo momento per modificare per la modifica del carico di lavoro dell'applicazione. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selezionare i livelli di durabilità e affidabilità per il cluster
Il livello di durabilità viene usato per indicare al sistema i privilegi delle VM rispetto all'infrastruttura di Azure sottostante. Nel tipo di nodo primario questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio il riavvio di una VM, il re-imaging di una VM o la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi di sistema e i servizi con stato. Nei tipi di nodo non primari questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di macchina virtuale (ad esempio, il riavvio di una VM, il re-imaging di una VM e la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi con stato.  Per i vantaggi diversi livelli e i suggerimenti su quale livello da usare e quando, vedere [le caratteristiche di durabilità del cluster][durability].

Il livello di affidabilità viene usato per impostare il numero di repliche dei servizi di sistema che si vuole eseguire in questo cluster nel tipo di nodo primario. I servizi di sistema nel cluster sono tanto più affidabili quanto più elevato è il numero di repliche.  Per i vantaggi diversi livelli e i suggerimenti su quale livello da usare e quando, vedere [le caratteristiche di affidabilità del cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Abilitare il proxy inverso e/o DNS
I servizi che si connettono tra loro in un cluster possono in genere accedere direttamente agli endpoint di altri servizi perché i nodi di un cluster si trovano nella stessa rete locale. Per renderne più semplice per la connessione tra i servizi, Service Fabric offre servizi aggiuntivi: Oggetto [il servizio DNS](service-fabric-dnsservice.md) e una [servizio di proxy inverso](service-fabric-reverseproxy.md).  Entrambi i servizi possono essere abilitati quando si distribuisce un cluster.

Poiché molti servizi, specialmente in contenitori, possono avere un nome di URL esistente, la possibilità di risolverli usando il DNS standard protocol (anziché il protocollo Naming Service) è utile, specialmente negli scenari di applicazioni "lift- and -shift". Questo è esattamente ciò che fa il servizio DNS. Consente di eseguire il mapping di nomi DNS a nomi di servizi e di conseguenza di risolvere gli indirizzi IP degli endpoint.

Il proxy inverso indirizza i servizi del cluster che espongono endpoint HTTP (incluso HTTPS). Il proxy inverso semplifica notevolmente la chiamata di altri servizi, fornendo un formato URI specifico.  Il proxy inverso gestisce anche la risoluzione, connessione e ripetere i passaggi necessari per un servizio di comunicare con un altro.

## <a name="prepare-for-disaster-recovery"></a>Preparare l'ambiente per il ripristino di emergenza
Una parte fondamentale della distribuzione a disponibilità elevata consiste nel garantire la resistenza dei servizi a tutti i tipi di errori. Ciò è particolarmente importante per gli errori imprevisti e incontrollabili. [Preparare il ripristino di emergenza](service-fabric-disaster-recovery.md) descrive alcune modalità di errore comuni che possono essere situazioni di emergenza se non organizzate e gestite correttamente. Viene inoltre illustrato mitigazioni e le azioni da intraprendere se un'emergenza.

## <a name="production-readiness-checklist"></a>Elenco di controllo per l'idoneità per la produzione
L'applicazione e il cluster sono pronti ad accettare il traffico della produzione? Prima di distribuire il cluster nell'ambiente di produzione, eseguire la [elenco di controllo di produzione readiness](service-fabric-production-readiness-checklist.md). Mantenere l'applicazione e i cluster in esecuzione senza problemi da affrontare gli elementi nell'elenco di controllo. Si consiglia di tutti gli elementi da selezionare, prima di passare in produzione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un cluster di Service Fabric che esegue Windows](service-fabric-best-practices-overview.md)
* [Creare un cluster di Service Fabric che esegue Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster