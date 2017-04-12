---
title: Backup online e ripristino con DocumentDB | Documentazione Microsoft
description: Informazioni su come eseguire il backup automatico e il ripristino di database NoSQL con Azure DocumentDB.
keywords: backup e ripristino, backup online
services: documentdb
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: raprasa
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e9b99a79adf445da8761ee399fb1e1a51f9224fc
ms.lasthandoff: 04/03/2017


---
# <a name="automatic-online-backup-and-restore-with-documentdb"></a>Backup online e ripristino automatico con DocumentDB
Azure DocumentDB esegue automaticamente il backup di tutti i dati a intervalli regolari. I backup automatici vengono eseguiti senza influenzare le prestazioni o la disponibilità delle operazioni del database NoSQL. Tutti i backup vengono archiviati separatamente in un altro servizio di archiviazione, oltre a essere replicati a livello globale per garantire la resilienza in caso di emergenze locali. I backup automatici sono destinati agli scenari in cui si elimina accidentalmente la propria raccolta di DocumentDB e in un secondo momento di richiede il ripristino dei dati o una soluzione di ripristino di emergenza.  

L'articolo inizia con un breve riepilogo sulla disponibilità e ridondanza dei dati in DocumentDB, per poi passare alla descrizione dei backup. 

## <a name="high-availability-with-documentdb---a-recap"></a>Disponibilità elevata con DocumentDB - riepilogo
DocumentDB è progettato per essere [distribuito globalmente](documentdb-distribute-data-globally.md) : consente di scalare la velocità effettiva in più aree di Azure insieme al failover basato sui criteri e alle API multihoming trasparenti. Dal momento che un sistema di database offre [SLA dalla disponibilità pari al 99,99%](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/), tutte le operazioni di scrittura in DocumentDB sono eseguite sui dischi locali da un quorum di repliche all'interno di un data center locale prima del riconoscimento sul client. La disponibilità elevata di DocumentDB si basa sull'archiviazione locale e non dipende da eventuali tecnologie di archiviazione esterna. Inoltre, se l'account di database è associato a più di un'area di Azure, le operazioni di scrittura vengono replicate anche nelle altre aree. Per scalare la velocità effettiva e accedere ai dati a basse latenze, è possibile avere tutte le aree di lettura associate all'account di database che si vuole. In ogni area di lettura, i dati (replicati) vengono mantenuti in modo permanente in un set di repliche.  

Come illustrato nel diagramma seguente, un'unica raccolta DocumentDB è [partizionata orizzontalmente](documentdb-partition-data.md). Una "partizione" è indicata da un cerchio nel diagramma sotto e ogni partizione viene resa altamente disponibile tramite un set di repliche. Si tratta della distribuzione locale all'interno di un'unica area di Azure (indicata dall'asse X). In più, ogni partizione (con il set di repliche corrispondente) viene successivamente distribuita a livello globale in più aree associate all'account di database (ad esempio, le tre aree geografiche presenti nell'illustrazione, ovvero Stati Uniti orientali, Stati Uniti occidentali e India centrale). Il "Set di partizioni" è un'entità distribuita a livello globale che comprende più copie dei dati in ogni area (indicata dall'asse Y). È possibile assegnare la priorità alle aree associate all'account di database e, in caso di emergenza, DocumentDB eseguirà il failover all'area successiva in modo trasparente. È anche possibile simulare manualmente il failover per testare la disponibilità end-to-end dell'applicazione.  

L'immagine seguente mostra l'alto livello di ridondanza con DocumentDB.

![Alto livello di ridondanza con DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)

![Alto livello di ridondanza con DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>Backup online, completi, automatici
Il database o la raccolta sono stati eliminati! Con DocumentDB non solo i dati ma anche i relativi backup vengono resi altamente ridondanti e resilienti alle emergenze locali. I backup automatici vengono attualmente eseguiti ogni quattro ore circa; ogni volta vengono archiviati i due backup più recenti. Se i dati vengono involontariamente eliminati o danneggiati, [contattare il supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore. 

I backup vengono eseguiti senza influenzare le prestazioni o la disponibilità delle operazioni del database. DocumentDB esegue il backup in background senza usare le unità richieste fornite, influire sulle prestazioni e inficiare la disponibilità del database NoSQL. 

A differenza dei dati archiviati all'interno di DocumentDB, i backup automatici vengono memorizzati nel servizio di archiviazione BLOB di Azure. Per garantire il caricamento a bassa latenza/efficiente, lo snapshot del backup viene caricato in un'istanza dell'archivio BLOB di Azure nella stessa area come area di scrittura corrente dell'account di database DocumentDB. Per garantire la resilienza in caso di emergenze locali, ogni snapshot dei dati di backup nell'archivio BLOB di Azure viene replicato nuovamente tramite l'archiviazione con ridondanza geografica (GRS) in un'altra area. Il diagramma seguente mostra la creazione di una copia di backup dell'intera raccolta di DocumentDB (in questo esempio con tutte le tre partizioni primarie negli Stati Uniti occidentali)che viene memorizzata in un account di archiviazione BLOB di Azure remoto negli Stati Uniti occidentali, per poi essere replicata con archiviazione con ridondanza geografica negli Stati Uniti orientali. 

L'immagine di seguito illustra i backup completi periodici di tutte le entità di DocumentDB nell'archiviazione di Azure con archiviazione con ridondanza geografica.

![Backup completi periodici di tutte le entità di DocumentDB nell'archiviazione di Azure con archiviazione con ridondanza geografica](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)

## <a name="retention-period-for-a-given-snapshot"></a>Periodo di conservazione di uno snapshot specifico
Come descritto sopra, periodicamente vengono creati degli snapshot dei dati che, in conformità alle normative, vengono conservati in archivio per un massimo di 90 giorni prima di essere eliminati. In caso di rimozione di una raccolta o di un account, DocumentDB conserva in archivio per 90 giorni la copia di backup più recente.

## <a name="restore-database-from-the-online-backup"></a>Ripristinare il database dal backup online
Nel caso di un'involontaria eliminazione dei dati, è possibile [creare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [contattare il supporto Azure](https://azure.microsoft.com/support/options/) per ripristinare i dati dall'ultimo backup automatico. Per ottenere lo snapshot specifico della copia di backup da ripristinare, DocumentDB richiede che i dati siano disponibili almeno per la durata del ciclo di backup per tale snapshot.

## <a name="next-steps"></a>Passaggi successivi
Per replicare il database NoSQL in più data center, vedere [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md). 

Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


