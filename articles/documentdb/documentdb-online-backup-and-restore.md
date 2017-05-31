---
title: Backup online e ripristino con Azure Cosmos DB | Microsoft Docs
description: Informazioni su come eseguire il backup automatico e il ripristino in un database Azure Cosmos DB.
keywords: backup e ripristino, backup online
services: cosmosdb
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/10/2017
ms.author: raprasa
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: aa5ea82b6d23c489e04e7683e7ab0f430666f99a
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Backup online automatico e ripristino con Azure Cosmos DB
Azure Cosmos DB esegue automaticamente il backup di tutti i dati a intervalli regolari. I backup automatici vengono eseguiti senza impatto sulle prestazioni o sulla disponibilità delle operazioni del database. Tutti i backup vengono archiviati separatamente in un altro servizio di archiviazione, oltre a essere replicati a livello globale per garantire la resilienza in caso di emergenze locali. I backup automatici sono destinati agli scenari in cui si elimina accidentalmente il contenitore Cosmos DB e in un secondo momento è necessario il ripristino dei dati o una soluzione di ripristino di emergenza.  

L'articolo offre un breve riepilogo iniziale della ridondanza e della disponibilità dei dati in Cosmos DB e quindi illustra i backup. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Disponibilità elevata con Cosmos DB: riepilogo
Cosmos DB è progettato per la [distribuzione globale](documentdb-distribute-data-globally.md) e consente di ridimensionare la velocità effettiva in più aree di Azure con failover basato su criteri e API multihosting trasparenti. Dato che si tratta di un sistema di database che offre [contratti di servizio con disponibilità del 99,99%](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/), tutte le operazioni di scrittura in Cosmos DB sono sottoposte a commit durevole su dischi locali da un quorum di repliche in un data center locale prima della conferma nel client. Si noti che la disponibilità elevata di Cosmos DB si basa sull'archiviazione locale e non dipende da tecnologie di archiviazione esterna. Inoltre, se l'account di database è associato a più di un'area di Azure, le operazioni di scrittura vengono replicate anche nelle altre aree. Per scalare la velocità effettiva e accedere ai dati a basse latenze, è possibile avere tutte le aree di lettura associate all'account di database che si vuole. In ogni area di lettura, i dati (replicati) vengono mantenuti in modo permanente in un set di repliche.  

Come illustrato nel diagramma seguente, un singolo contenitore Cosmos DB viene sottoposto a [partizionamento orizzontale](documentdb-partition-data.md). Una "partizione" è indicata da un cerchio nel diagramma sotto e ogni partizione viene resa altamente disponibile tramite un set di repliche. Si tratta della distribuzione locale all'interno di un'unica area di Azure (indicata dall'asse X). In più, ogni partizione (con il set di repliche corrispondente) viene successivamente distribuita a livello globale in più aree associate all'account di database (ad esempio, le tre aree geografiche presenti nell'illustrazione, ovvero Stati Uniti orientali, Stati Uniti occidentali e India centrale). Il "Set di partizioni" è un'entità distribuita a livello globale che comprende più copie dei dati in ogni area (indicata dall'asse Y). È possibile assegnare la priorità alle aree associate all'account di database e, in caso di emergenza, verrà effettuato il failover trasparente di Cosmos DB nell'area successiva. È anche possibile simulare manualmente il failover per testare la disponibilità end-to-end dell'applicazione.  

L'immagine seguente illustra l'elevato grado di ridondanza con Cosmos DB.

![Elevato grado di ridondanza con Cosmos DB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)

![Elevato grado di ridondanza con Cosmos DB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>Backup online, completi, automatici
È possibile che il contenitore o il database venga eliminato. Con Cosmos DB, non solo i dati ma anche i relativi backup vengono resi altamente ridondanti e resilienti in caso di emergenze locali. I backup automatici vengono attualmente eseguiti ogni quattro ore circa; ogni volta vengono archiviati i due backup più recenti. Se i dati vengono involontariamente eliminati o danneggiati, [contattare il supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore. 

I backup vengono eseguiti senza influenzare le prestazioni o la disponibilità delle operazioni del database. Cosmos DB esegue il backup in background senza utilizzare le UR di cui è stato effettuato il provisioning o influenzare le prestazioni e senza influire sulla disponibilità del database. 

A differenza dei dati archiviati all'interno di Cosmos DB, i backup automatici vengono memorizzati nel servizio di archiviazione BLOB di Azure. Per garantire il caricamento efficiente e a bassa latenza, lo snapshot del backup viene caricato in un'istanza dell'archivio BLOB di Azure nella stessa area dell'area di scrittura corrente dell'account di database Cosmos DB. Per garantire la resilienza in caso di emergenze locali, ogni snapshot dei dati di backup nell'archivio BLOB di Azure viene replicato nuovamente tramite l'archiviazione con ridondanza geografica (GRS) in un'altra area. Il diagramma seguente illustra il backup dell'intero contenitore Cosmos DB (in questo esempio con tutte le tre partizioni primarie negli Stati Uniti occidentali) in un account di archiviazione BLOB di Azure remoto negli Stati Uniti occidentali e quindi la replica con archiviazione con ridondanza geografica negli Stati Uniti orientali. 

L'immagine di seguito illustra i backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure.

![Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)

## <a name="retention-period-for-a-given-snapshot"></a>Periodo di conservazione di uno snapshot specifico
Come descritto sopra, periodicamente vengono creati degli snapshot dei dati che, in conformità alle normative, vengono conservati in archivio per un massimo di 90 giorni prima di essere eliminati. In caso di eliminazione di un contenitore o un account, Cosmos DB archivia l'ultimo backup per 90 giorni.


## <a name="restore-database-from-the-online-backup"></a>Ripristinare il database dal backup online
Nel caso di un'involontaria eliminazione dei dati, è possibile [creare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [contattare il supporto Azure](https://azure.microsoft.com/support/options/) per ripristinare i dati dall'ultimo backup automatico. Per il ripristino di uno snapshot specifico del backup, Cosmos DB richiede che i dati siano stati disponibili almeno per la durata del ciclo di backup per tale snapshot.

## <a name="next-steps"></a>Passaggi successivi

Per replicare il database in più data center, vedere l'articolo su come [distribuire i dati a livello globale con Cosmos DB](documentdb-distribute-data-globally.md). 

Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


