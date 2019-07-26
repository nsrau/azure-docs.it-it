---
title: Problemi noti e limitazioni per le migrazioni online al database SQL di Azure | Microsoft Docs
description: Informazioni su problemi noti e limitazioni per le migrazioni online al database SQL di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/23/2019
ms.openlocfilehash: b95e37b4782920f25a16f0750211555d0bef1207
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383840"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Problemi noti e limitazioni per le migrazioni online al database SQL di Azure

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da SQL Server al database SQL di Azure.

> [!IMPORTANT]
> Con le migrazioni online di SQL Server al database SQL di Azure, la migrazione dei tipi di dati SQL_variant non è supportata.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrazione delle tabelle temporali non supportata

**Sintomo**

Se il database di origine è costituito da una o più tabelle temporali, si verifica un errore di migrazione del database durante l'operazione di caricamento completo dei dati ed è possibile che venga visualizzato il messaggio seguente:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Esempio di errori di tabella temporale](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Soluzione alternativa**

1. Trovare le tabelle temporali nello schema di origine usando la query seguente.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Escludere queste tabelle dal pannello **Configura le impostazioni di migrazione** in cui si specificano le tabelle per la migrazione.

3. Eseguire di nuovo l'attività di migrazione.

**Risorse**

Per altre informazioni, vedere l'articolo [Tabelle temporali](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>La migrazione delle tabelle include una o più colonne con il tipo di dati hierarchyid

**Sintomo**

Può verificarsi un'eccezione SQL in cui è indicato che "ntext non è compatibile con hierarchyid" durante l'operazione di caricamento completo dei dati:
     
![Esempio di errori di hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Soluzione alternativa**

1. Trovare le tabelle utente che includono colonne con tipo di dati hierarchyid usando la query seguente.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. Escludere queste tabelle dal pannello **Configura le impostazioni di migrazione** in cui si specificano le tabelle per la migrazione.

3. Eseguire di nuovo l'attività di migrazione.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Errori di migrazione con varie violazioni di integrità relativi ai trigger attivi nello schema durante il caricamento completo o la sincronizzazione incrementale dei dati

**Soluzione alternativa**

1. Trovare i trigger che sono attualmente attivi nel database di origine usando la query seguente:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Disabilitare i trigger nel database di origine eseguendo i passaggi illustrati nell'articolo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Eseguire di nuovo l'attività di migrazione.

### <a name="support-for-lob-data-types"></a>Supporto per i tipi di dati LOB

**Sintomo**

Se la lunghezza della colonna LOB (Large Object) è maggiore di 32 kB, è possibile che nella destinazione i dati vengano troncati. È possibile controllare la lunghezza della colonna LOB usando questa query: 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Soluzione alternativa**

Se si dispone di una colonna LOB di dimensioni maggiori di 32 KB, contattare il team di progettazione per [chiedere alle migrazioni del database di Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemi con le colonne di timestamp

**Sintomo**

Il Servizio Migrazione del database non esegue la migrazione del valore di timestamp, ma ne genera uno nuovo nella tabella di destinazione.

**Soluzione alternativa**

Se è necessario utilizzare DMS per eseguire la migrazione del valore di timestamp esatto archiviato nella tabella di origine, contattare il team di progettazione per [chiedere alle migrazioni del database di Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Gli errori di migrazione dei dati non forniscono ulteriori dettagli sul pannello dello stato dettagliato del database.

**Sintomo**

Quando si verificano errori di migrazione nella visualizzazione stato Dettagli database, la selezione del collegamento **errori di migrazione dati** sulla barra multifunzione superiore potrebbe non fornire dettagli aggiuntivi specifici degli errori di migrazione.

![Esempio di errori di migrazione dei dati senza dettagli](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Soluzione alternativa**

Per ottenere dettagli specifici sugli errori, eseguire i passaggi seguenti.

1. Chiudere il pannello di informazioni dettagliate sullo stato del database per visualizzare la schermata dell'attività di migrazione.

     ![Schermata dell'attività di migrazione](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selezionare **Vedere i dettagli dell'errore** per visualizzare i messaggi specifici che consentono di risolvere gli errori di migrazione.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Tipo di dati geography non supportato nella migrazione online di SQLDB

**Sintomo**

La migrazione ha esito negativo con un messaggio di errore contenente il testo seguente:

 "* * si è verificato un errore irreversibile", "errorEvents":<Table>.<Column> è di tipo ' GEOGRAPHY ', che non è supportato da' Full Load ' nella modalità di supporto ' Full LOB ' ".

**Soluzione alternativa** Sebbene il servizio migrazione del database di Azure supporti il tipo di dati geography per le migrazioni offline nel database SQL di Azure, per le migrazioni online, il tipo di dati geography non è supportato. Provare i metodi alternativi per modificare il tipo di dati nell'origine in un tipo supportato prima di provare a usare il servizio migrazione del database di Azure per una migrazione in linea di questo database. 
