---
title: Panoramica di Cache HPC di Azure
description: Illustra Cache HPC di Azure, una soluzione che accelera l'accesso ai file per il calcolo ad alte prestazioni
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 77eca3ef58733a616705fdaac1d2880b5a990e8a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036877"
---
# <a name="what-is-azure-hpc-cache"></a>Che cos'è Cache HPC di Azure?

Cache HPC di Azure accelera l'accesso ai dati per le attività di calcolo ad alte prestazioni (HPC, High Performance Computing). Memorizzando i file nella cache in Azure, rende disponibile la scalabilità del cloud computing anche per i flussi di lavoro in cui i dati vengono archiviati tramite collegamenti WAN, ad esempio nell'ambiente NAS (Network-Attached Storage) del data center locale.

Cache HPC di Azure è facile da avviare e monitorare dal portale di Azure. Lo spazio di archiviazione NFS esistente o i nuovi contenitori BLOB possono diventare parte del relativo spazio dei nomi aggregato e questa integrazione semplifica l'accesso client anche se si cambia la destinazione di archiviazione back-end.

## <a name="use-cases"></a>Casi d'uso

Cache HPC di Azure aumenta in modo particolare la produttività per i flussi di lavoro simili ai seguenti:

* Flusso di lavoro di accesso ai file con intensa attività di lettura
* Dati archiviati nello spazio di archiviazione accessibile da NFS, in BLOB di Azure o in entrambi
* Farm di calcolo fino a un massimo di 75.000 core CPU

È possibile aggiungere Cache HPC di Azure a una vasta gamma di flussi di lavoro in molti settori. Trarrà vantaggio da questo servizio qualsiasi sistema in cui un numero elevato di computer deve accedere a un set di file su vasta scala e con una bassa latenza. Le sezioni seguenti forniscono esempi specifici.

### <a name="visual-effects-vfx-rendering"></a>Rendering degli effetti visivi (VFX)

Nel settore dei media e dell'intrattenimento Cache HPC di Azure può velocizzare l'accesso ai dati per i progetti di rendering che richiedono molto tempo. I flussi di lavoro di rendering VFX spesso richiedono l'elaborazione all'ultimo minuto da parte di un numero elevato di nodi di calcolo. I dati per questi flussi di lavoro si trovano in genere in un ambiente NAS locale. Cache HPC di Azure può memorizzare i dati dei file nella cache nel cloud per ridurre la latenza e aumentare la flessibilità per il rendering su richiesta.

### <a name="life-sciences"></a>Scienze biologiche

Molti flussi di lavoro delle scienze biologiche possono trarre vantaggio dalla memorizzazione dei file nella cache con scale-out.

Un istituto di ricerca che intende trasferire in Azure i propri flussi di lavoro di genomica può spostarli facilmente tramite Cache HPC di Azure. Poiché la cache fornisce l'accesso ai file POSIX, il flusso di lavoro esistente sul lato client può essere eseguito nel cloud senza alcuna modifica.

È possibile usare Cache HPC di Azure anche per aumentare l'efficienza in attività come l'analisi secondaria, la simulazione farmacologica o l'analisi delle immagini basata su IA.

### <a name="financial-services-analytics"></a>Analisi dei servizi finanziari

Una cache HPC di Azure può velocizzare i calcoli di analisi quantitativa, i carichi di lavoro di analisi dei rischi e le simulazioni Monte Carlo, offrendo alle aziende di servizi finanziari informazioni più dettagliate per prendere decisioni strategiche.

## <a name="region-availability"></a>Aree di disponibilità

Cache HPC di Azure è disponibile in queste aree di Azure:

* Stati Uniti orientali
* Stati Uniti orientali 2
* Europa settentrionale
* Europa occidentale
* Asia sud-orientale
* Stati Uniti occidentali 2

Per le informazioni più aggiornate sulla disponibilità, vedere la [pagina relativa al prodotto Cache HPC di Azure](https://azure.microsoft.com/services/hpc-cache).

## <a name="next-steps"></a>Passaggi successivi

* Leggere la [pagina relativa al prodotto Cache HPC di Azure](https://azure.microsoft.com/services/hpc-cache) per avere altre informazioni sulle relative funzionalità
* Vedere le informazioni relative ai [prerequisiti](hpc-cache-prereqs.md) del prodotto
* [Creare una cache HPC di Azure](hpc-cache-create.md) dal portale di Azure
