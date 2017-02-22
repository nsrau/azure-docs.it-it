---
title: 'Azure DocumentDB come archivio di valori chiave: Panoramica dei costi | Documentazione Microsoft'
description: Informazioni sui costi ridotti correlati all&quot;uso di Azure DocumentDB come archivio di valori chiave.
keywords: archivio di valori chiave
services: documentdb
author: ArnoMicrosoft
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: acomet
translationtype: Human Translation
ms.sourcegitcommit: faf363eb5848752b27faacd971867391b6393337
ms.openlocfilehash: 1a693477a51a05fb28e7c4772aeee77fd0c4e1dd

---

# <a name="documentdb-as-a-key-value-store--cost-overview"></a>DocumentDB come archivio di valori chiave: Panoramica dei costi

Azure DocumentDB è un servizio di database NoSQL completamente gestito e distribuito a livello globale per compilare con facilità applicazioni [distribuite globalmente](documentdb-distribute-data-globally.md), su larga scala e a disponibilità elevata. Per impostazione predefinita, DocumentDB indicizza automaticamente tutti i dati che inserisce, in modo efficiente. In questo modo è possibile abilitare query [SQL](documentdb-sql-query.md) (e [JavaScript](documentdb-programming.md)) rapide e coerenti su qualsiasi tipo di dati. 

L'articolo descrive il costo di DocumentDB per semplici operazioni di lettura e scrittura, quando usato come archivio di chiavi/valori. Le operazioni di scrittura includono inserimenti, sostituzioni, eliminazioni e upsert di documenti. Oltre a una disponibilità elevata del 99,99%, DocumentDB garantisce rispettivamente una latenza pari a <10 ms per le letture e a <15 ms per le scritture (indicizzate), al 99° percentile. 

## <a name="why-we-use-request-units-rus"></a>Perché usare le unità richiesta (UR)

Le prestazioni di DocumentDB si basano sulla quantità di [unità richiesta](documentdb-programming.md) (UR) con provisioning per la partizione. Il provisioning è a una granularità in secondi e viene acquistato in UR/sec ([da non confondere con la fatturazione oraria](https://azure.microsoft.com/pricing/details/documentdb/)). Le UR devono essere considerate come una valuta che semplifica il provisioning della velocità effettiva necessaria per l'applicazione. I clienti non devono pensare a differenziare le unità di capacità tra lettura e scrittura. Il modello singolo di valuta delle UR crea efficienza per condividere la capacità di provisioning tra le letture e le scritture. Questo modello di capacità con provisioning consente al servizio fornire una velocità effettiva prevedibile e coerente, con bassa latenza e disponibilità elevata garantite. Infine, si usano le UR per generare un modello di velocità effettiva, ma ogni UR con provisioning presenta inoltre una quantità di risorse definita (memoria, memoria centrale). UR/sec non si riferisce solo alle operazioni di I/O al secondo (IOPS).

Come un sistema di database distribuito a livello globale, DocumentDB è il solo servizio di Azure che fornisce un contratto di servizio basato su latenza, velocità effettiva e coerenza oltre che sulla disponibilità elevata. La velocità effettiva con cui viene effettuato il provisioning viene applicata a ognuna delle aree associate all'account di database DocumentDB. Per le letture, DocumentDB offre più [livelli di coerenza](documentdb-consistency-levels.md) ben definiti tra cui scegliere. 

La tabella seguente mostra il numero di UR necessarie per eseguire transazioni di lettura e scrittura con dimensioni di documento pari a 1 KB e 100 KB.

|Dimensioni del documento|1 Lettura|1 Scrittura|
|-------------|------|-------|
|1 KB|1 UR|5 UR|
|100 KB|10 UR|50 UR|

## <a name="cost-of-reads-and-writes"></a>Costo delle letture e scritture

Se si esegue il provisioning di 1.000 UR al secondo, si avrà un totale di 3,6 milioni di UR all'ora e il costo sarà di 0,08 dollari l'ora (negli Stati Uniti e in Europa). Per un documento di 1 KB, ciò significa che è possibile utilizzare 3,6 milioni di letture o 0,72 milioni di scritture (3,6 m UR/5) utilizzando la velocità effettiva con provisioning. Normalizzate in milioni di letture e scritture, il costo sarebbe di 0,022 dollari/milione di letture (0,08 dollari/3,6) e 0,111 dollari/milioni di scritture (0,08 dollari/0,72). Il costo al milione diventa minimo come illustrato nella tabella seguente.

|Dimensioni del documento|1 milione di letture|1 milione di scritture|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|

La maggior parte dei servizi di archivi di oggetti o BLOB di base addebita&0;,40 dollari per milione di transazioni di lettura e&5; dollari per milione transazioni di scrittura. Se utilizzato in modo ottimale, DocumentDB è fino al 98% più conveniente di queste altre soluzioni (per le transazioni di 1KB).

## <a name="next-steps"></a>Passaggi successivi

Saranno presto disponibili nuovi articoli sull'ottimizzazione del provisioning delle risorse di DocumentDB. Nel frattempo, è possibile usare il nostro [calcolatore UR](https://www.documentdb.com/capacityplanner).



<!--HONumber=Feb17_HO1-->


