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
ms.date: 07/26/2019
ms.openlocfilehash: afafaa86988905329a0e4ff45f29bea9d1d57820
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501032"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Problemi noti/limitazioni della migrazione con migrazioni online al database SQL di Azure

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da SQL Server al database SQL di Azure.

> [!IMPORTANT]
> Con le migrazioni online di SQL Server al database SQL di Azure, la migrazione dei tipi di dati SQL_variant non è supportata.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrazione delle tabelle temporali non supportata

**Sintomo** Se il database di origine è costituito da una o più tabelle temporali, la migrazione del database ha esito negativo durante l'operazione di caricamento completo dei dati ed è possibile che venga visualizzato il messaggio seguente:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Esempio di errori di tabella temporale](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Soluzione alternativa** Seguire questa procedura.

1. Trovare le tabelle temporali nello schema di origine usando la query seguente.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Escludere queste tabelle dal pannello **Configura le impostazioni di migrazione** in cui si specificano le tabelle per la migrazione.

3. Eseguire di nuovo l'attività di migrazione.

**Risorse** di Per altre informazioni, vedere l'articolo [tabelle temporali](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>La migrazione delle tabelle include una o più colonne con il tipo di dati hierarchyid

**Sintomo** È possibile che venga visualizzata un'eccezione SQL che suggerisce che "ntext è incompatibile con hierarchyid" durante l'operazione di caricamento completo dei dati:

![Esempio di errori di hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Soluzione alternativa** Seguire questa procedura.

1. Trovare le tabelle utente che includono colonne con tipo di dati hierarchyid usando la query seguente.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Escludere queste tabelle dal pannello **Configura le impostazioni di migrazione** in cui si specificano le tabelle per la migrazione.

3. Eseguire di nuovo l'attività di migrazione.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Errori di migrazione con varie violazioni di integrità relativi ai trigger attivi nello schema durante il caricamento completo o la sincronizzazione incrementale dei dati

**Soluzione alternativa** Seguire questa procedura.

1. Trovare i trigger che sono attualmente attivi nel database di origine usando la query seguente:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Disabilitare i trigger nel database di origine eseguendo i passaggi illustrati nell'articolo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Eseguire di nuovo l'attività di migrazione.

### <a name="support-for-lob-data-types"></a>Supporto per i tipi di dati LOB

**Sintomo** Se la lunghezza della colonna Large Object (LOB) è maggiore di 32 KB, i dati potrebbero essere troncati nella destinazione. È possibile controllare la lunghezza della colonna LOB usando questa query:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Soluzione alternativa** Se si dispone di una colonna LOB di dimensioni maggiori di 32 KB, contattare il team di progettazione per [chiedere alle migrazioni del database di Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemi con le colonne di timestamp

**Sintomo** Il servizio migrazione del database di Azure non esegue la migrazione del valore del timestamp di origine. al contrario, il servizio migrazione del database di Azure genera un nuovo valore di timestamp nella tabella di destinazione.

**Soluzione alternativa**

Se è necessario il servizio migrazione del database di Azure per eseguire la migrazione del valore di timestamp esatto archiviato nella tabella di origine, contattare il team di progettazione per [chiedere alle migrazioni del database di Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Gli errori di migrazione dei dati non forniscono dettagli aggiuntivi sul pannello dello stato dettagliato del database

**Sintomo** Quando si verificano errori di migrazione nella visualizzazione stato Dettagli database, la selezione del collegamento **errori di migrazione dati** sulla barra multifunzione superiore potrebbe non fornire dettagli aggiuntivi specifici degli errori di migrazione.

![Esempio di errori di migrazione dei dati senza dettagli](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Soluzione alternativa** Per ottenere dettagli specifici sull'errore, attenersi alla procedura riportata di seguito.

1. Chiudere il pannello di informazioni dettagliate sullo stato del database per visualizzare la schermata dell'attività di migrazione.

     ![Schermata dell'attività di migrazione](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selezionare **Vedere i dettagli dell'errore** per visualizzare i messaggi specifici che consentono di risolvere gli errori di migrazione.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Tipo di dati geography non supportato nella migrazione online di SQLDB

**Sintomo** La migrazione ha esito negativo con un messaggio di errore contenente il testo seguente:

 "* * si è verificato un errore irreversibile", "errorEvents":<Table>.<Column> è di tipo ' GEOGRAPHY ', che non è supportato da' Full Load ' nella modalità di supporto ' Full LOB ' ".

**Soluzione alternativa** Sebbene il servizio migrazione del database di Azure supporti il tipo di dati geography per le migrazioni offline nel database SQL di Azure, per le migrazioni online, il tipo di dati geography non è supportato. Provare i metodi alternativi per modificare il tipo di dati nell'origine in un tipo supportato prima di provare a usare il servizio migrazione del database di Azure per una migrazione in linea di questo database.

### <a name="supported-editions"></a>Edizioni supportate

**Sintomo** La migrazione ha esito negativo con un messaggio di errore contenente il testo seguente:

 Errore di convalida delle impostazioni di migrazione: L'edizione del server [Business Intelligence Edition (64 bit)] non corrisponde alle edizioni supportate [Enterprise, standard, Developer].

**Soluzione alternativa** Il supporto per le migrazioni online al database SQL di Azure con il servizio migrazione del database di Azure si estende solo alle edizioni Enterprise, standard e Developer. Assicurarsi di usare un'edizione supportata prima di iniziare il processo di migrazione.
