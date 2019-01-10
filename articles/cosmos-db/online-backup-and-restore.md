---
title: Backup online automatico e ripristino dei dati su richiesta in Azure Cosmos DB
description: Questo articolo descrive come funzionano il backup online automatico e il ripristino dei dati su richiesta in Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b5b56e39e1b9830d5b998ace2a384d6878cd510
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041816"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup online e ripristino dei dati su richiesta in Azure Cosmos DB

Azure Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. I backup automatici vengono eseguiti senza impatto sulle prestazioni o sulla disponibilità delle operazioni del database. Tutti i backup sono archiviati separatamente in un servizio di archiviazione e replicati a livello globale per garantire la resilienza in caso di emergenze a livello di area. I backup automatici sono utili in caso di eliminazione accidentale o aggiornamento dell'account, di database o di contenitori Azure Cosmos, quando in seguito occorre ripristinare i dati.

## <a name="automatic-and-online-backups"></a>Backup automatici e online

Con Azure Cosmos DB, non solo i dati ma anche i relativi backup sono altamente ridondanti e resilienti in caso di emergenze a livello di area. I backup automatici vengono attualmente eseguiti ogni quattro ore e in qualsiasi momento, rimangono archiviati i due backup più recenti. In caso di eliminazione accidentale o danneggiamento dei dati, è necessario contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro otto ore in modo che il team di Azure Cosmos DB possa offrire supporto per il ripristino dei dati dai backup.

I backup vengono eseguiti senza effetti sulle prestazioni o la disponibilità dell'applicazione. Azure Cosmos DB esegue il backup dei dati in background senza utilizzare velocità effettiva di cui è stato effettuato il provisioning aggiuntiva (UR) o senza influire sulle prestazioni e la disponibilità del database.

Azure Cosmos DB archivia i backup automatici in Archiviazione BLOB di Azure, mentre i dati effettivi si trovano in locale all'interno di Azure Cosmos DB. Per garantire la bassa latenza, lo snapshot del backup viene archiviato in Archiviazione BLOB di Azure nella stessa area dell'area di scrittura corrente (o una delle aree di scrittura, nel caso di una configurazione multimaster) dell'account del database di Cosmos DB. Per garantire la resilienza in caso di emergenze a livello di area, ogni snapshot dei dati di backup nell'archivio BLOB di Azure viene a sua volta replicato in un'altra area tramite l'archiviazione con ridondanza geografica. L'area in cui viene replicato il backup dipende dall'area di origine e dalla coppia di aree associata all'area di origine. Per altre informazioni, vedere l'[elenco di coppie di aree di Azure con ridondanza geografica](../best-practices-availability-paired-regions.md). Non è possibile accedere direttamente a questo backup. Azure Cosmos DB userà questo backup solo se viene avviato un ripristino del backup.
L'immagine seguente mostra come viene eseguito il backup di un contenitore di Azure Cosmos con tutte le tre partizioni fisiche primarie nell'area Stati Uniti occidentali in un account di Archiviazione BLOB di Azure remoto negli Stati Uniti occidentali e quindi replicato negli Stati Uniti orientali:

![Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opzioni per la gestione dei backup

Con gli account API SQL di Azure Cosmos DB è anche possibile gestire backup propri adottando uno degli approcci seguenti:

* Usare [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare periodicamente i dati in una risorsa di archiviazione di propria scelta.

* Usare [feed di modifiche](change-feed.md) di Azure Cosmos DB per leggere periodicamente i dati per i backup completi, nonché per le modifiche incrementali e archiviarli in uno spazio di archiviazione proprio.

## <a name="backup-retention-period"></a>Periodo di conservazione dei backup

Azure Cosmos DB crea snapshot dei dati ogni quattro ore. In qualsiasi momento risultano disponibili solo gli ultimi due snapshot. Tuttavia, se il contenitore o il database viene eliminato, Azure Cosmos DB conserva gli snapshot esistenti di un determinato contenitore o database per 30 giorni.

## <a name="restoring-data-from-online-backups"></a>Ripristino dei dati dai backup online

L'eliminazione o la modifica dei dati accidentale può verificarsi in uno degli scenari seguenti:  

* Eliminazione dell'intero account Azure Cosmos

* Eliminazione di uno o più database di Azure Cosmos

* Eliminazione di uno o più contenitori di Azure Cosmos

* Eliminazione o modifica di elementi di Azure Cosmos (ad esempio, documenti) all'interno di un contenitore. Questo caso specifico viene in genere definito "danneggiamento dei dati".

* Eliminazione o danneggiamento del database di un'offerta condivisa oppure dei contenitori all'interno del database di un'offerta condivisa

Azure Cosmos DB può ripristinare i dati in tutti gli scenari precedenti. Il processo di ripristino crea sempre un nuovo account Azure Cosmos per contenere i dati ripristinati. Il nome del nuovo account, se non specificato, sarà nel formato `<Azure_Cosmos_account_original_name>-restored1`. L'ultima cifra viene incrementata se vengono tentati più ripristini. Non è possibile ripristinare i dati in un account Azure Cosmos creato in precedenza.

Quando viene eliminato un account Azure Cosmos, è possibile ripristinare i dati in un account con lo stesso nome, a condizione che il nome di account non sia in uso. In questi casi, è consigliabile non creare nuovamente l'account dopo l'eliminazione, perché non solo impedisce ai dati ripristinati di usare lo stesso nome, ma rende anche più difficile individuare l'account giusto per eseguire il ripristino. 

Quando viene eliminato un database di Azure Cosmos, è possibile ripristinare l'intero database o un subset dei contenitori all'interno del database. È anche possibile selezionare contenitori in database diversi e ripristinarli. Tutti i dati ripristinati vengono inseriti in un nuovo account Azure Cosmos.

Quando uno o più elementi all'interno di un contenitore vengono accidentalmente eliminati o modificati (caso di danneggiamento dei dati), sarà necessario specificare l'ora del ripristino. Il tempismo è fondamentale in questo caso. Dato che il contenitore è attivo, il backup è ancora in esecuzione, quindi se si attende oltre il periodo di conservazione (otto ore per impostazione predefinita) i backup verranno sovrascritti. Nel caso di eliminazioni, i dati non sono più archiviati perché non verranno sovrascritti dal ciclo di backup. I backup per i database o i contenitori eliminati vengono conservati per 30 giorni.

Se si esegue il provisioning della velocità effettiva a livello di database (vale a dire, un set di contenitori condivide la velocità effettiva di cui è stato effettuato il provisioning), il processo di backup e ripristino in questo caso avviene a livello dell'intero database e non a livello dei singoli contenitori. In questi casi, non è possibile selezionare un subset di contenitori da ripristinare.

## <a name="migrating-data-to-the-original-account"></a>Migrazione dei dati nell'account originale

L'obiettivo principale del ripristino dei dati è fornire un metodo per recuperare tutti i dati eliminati o modificati accidentalmente. Pertanto, è consigliabile esaminare prima di tutto il contenuto dei dati ripristinati per assicurarsi che siano quelli previsti. Procedere quindi alla migrazione dei dati nell'account primario. Sebbene sia possibile usare l'account ripristinato come account attivo, non è un'opzione consigliata in presenza di carichi di lavoro di produzione.  

Di seguito sono indicati i vari modi disponibili per eseguire la migrazione dei dati nell'account Azure Cosmos originale:

* Uso dell'[Utilità di migrazione dati di Cosmos DB](import-data.md)
* Uso di [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Uso del [feed di modifiche](change-feed.md) in Azure Cosmos DB 
* Scrivere codice personalizzato

Eliminare gli account ripristinati subito dopo aver completato la migrazione, per evitare addebiti.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile consultare informazioni su come ripristinare i dati da un account Azure Cosmos o su come eseguire la migrazione dei dati in un account Azure Cosmos

* Per creare una richiesta di ripristino, contattare il supporto tecnico di Azure e [aprire un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Come ripristinare i dati di Azure Cosmos DB da un backup](how-to-backup-and-restore.md)
* [Usare un feed di modifiche di Cosmos DB](change-feed.md) per spostare i dati in Azure Cosmos DB.
* [Usare Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare i dati in Azure Cosmos DB.

