---
title: Panoramica di Cache HPC di Azure (anteprima)
description: Illustra Cache HPC di Azure, una soluzione che accelera l'accesso ai file per il calcolo ad alte prestazioni
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: bfbbcd2d7d1bb44e260bedda54ca38ed6860ea67
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254493"
---
# <a name="what-is-azure-hpc-cache-preview"></a>Che cos'è Cache HPC di Azure? (Anteprima)

Cache HPC di Azure accelera l'accesso ai dati per le attività di calcolo ad alte prestazioni (HPC, High Performance Computing). Memorizzando nella cache i file di Azure, Cache HPC di Azure aggiunge la scalabilità del cloud computing al flusso di lavoro esistente. Il servizio può essere usato anche per i flussi di lavoro in cui i dati vengono archiviati tramite collegamenti WAN, ad esempio nell'ambiente NAS (Network-Attached Storage) del data center locale.

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

Un istituto di ricerca che intende trasferire in Azure i propri flussi di lavoro di genomica può spostarli facilmente tramite Cache HPC di Azure. Poiché la cache fornisce l'accesso ai file POSIX, non è necessario apportare modifiche sul lato client per eseguire il relativo flusso di lavoro esistente nel cloud.

È possibile usare Cache HPC di Azure anche per aumentare l'efficienza in attività come l'analisi secondaria, la simulazione farmacologica o l'analisi delle immagini basata su IA.

### <a name="financial-services-analytics"></a>Analisi dei servizi finanziari

Una distribuzione di Cache HPC di Azure consente di velocizzare i calcoli dell'analisi quantitativa, i carichi di lavoro dell'analisi dei rischi e le simulazioni Monte Carlo per offrire alle società di servizi finanziari informazioni più dettagliate ai fini delle decisioni strategiche.

## <a name="region-availability"></a>Aree di disponibilità

Cache HPC di Azure è disponibile in queste aree di Azure:

* Stati Uniti orientali
* Stati Uniti orientali 2
* Europa settentrionale
* Europa occidentale
* Asia sud-orientale
* Stati Uniti occidentali 2

Per le informazioni più aggiornate sulla disponibilità, vedere la [pagina relativa al prodotto Cache HPC di Azure](https://azure.microsoft.com/services/hpc-cache).

## <a name="preview-availability"></a>Disponibilità dell'anteprima

La disponibilità dell'anteprima pubblica di Cache HPC di Azure è limitata per garantire la qualità del servizio. Per richiedere l'accesso, compilare [questo modulo](https://aka.ms/onboard-hpc-cache). Una volta aggiunta la sottoscrizione all'elenco di accesso, è possibile creare cache di test.

## <a name="next-steps"></a>Passaggi successivi

* Leggere la [pagina relativa al prodotto Cache HPC di Azure](https://azure.microsoft.com/services/hpc-cache) per avere altre informazioni sulle relative funzionalità
* Vedere le informazioni relative ai [prerequisiti](hpc-cache-prereqs.md) del prodotto
* [Creare una cache HPC di Azure](hpc-cache-create.md) dal portale di Azure
