<properties
   pageTitle="Migrazione: utilità di migrazione per data warehouse | Microsoft Azure"
   description="Eseguire la migrazione a SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>  


# Utilità di migrazione per data warehouse (anteprima)

> [AZURE.SELECTOR]
- [Scaricare l'utilità di migrazione][]

L'utilità di migrazione per data warehouse è uno strumento progettato per eseguire la migrazione dello schema e dei dati da SQL Server e dal database SQL di Azure ad Azure SQL Data Warehouse. Durante la migrazione dello schema, lo strumento mappa automaticamente lo schema corrispondente dall'origine alla destinazione. Dopo la migrazione dello schema, lo strumento consente di spostare i dati con gli script generati automaticamente.

Oltre alla migrazione dello schema e dei dati, lo strumento consente di generare report di compatibilità che riepilogano le incompatibilità riscontrate tra le istanze di destinazione e di origine che possono impedire la corretta migrazione.

## Introduzione
Come prerequisito per l'installazione, è necessario installare l'utilità della riga di comando bcp per l'esecuzione degli script di migrazione e Office per la visualizzazione del report di compatibilità. Dopo aver fatto clic sull'eseguibile scaricato, sarà necessario accettare le condizioni di licenza standard prima di procedere all'installazione dello strumento.

Per eseguire l'utilità di migrazione sarà necessario disporre di una delle seguenti autorizzazioni nel database di cui si sta tentando di eseguire la migrazione: CREA DATABASE, MODIFICA QUALSIASI DATABASE o VISUALIZZAZIONE QUALSIASI DEFINIZIONE.

### Avvio dello strumento e connessione
Avviare lo strumento facendo clic sull'icona del desktop visualizzata dopo l'installazione. All'apertura dello strumento verrà visualizzata una pagina di connessione iniziale in cui sarà possibile scegliere l'origine e la destinazione per lo strumento di migrazione. Al momento, sono supportati SQL Server e il database SQL di Azure come origini e SQL Data Warehouse come destinazione. Una volta effettuata la selezione, verrà richiesto di connettersi al server di origine. A questo scopo, sarà necessario specificare il nome del server, eseguire l'autenticazione e quindi fare clic su 'Connect'.

Dopo l'autenticazione, lo strumento visualizzerà un elenco di database presenti nel server con cui è stata stabilita la connessione. Per avviare la migrazione, selezionare il database desiderato e fare clic su "Migrate selected" (Esegui la migrazione della selezione).

## Report di migrazione
Se si seleziona "Check Database Compatibility" (Verifica la compatibilità del database) nello strumento, verrà generato un report che riepiloga tutte le incompatibilità rilevate negli oggetti del database di cui si richiede la migrazione. Per un elenco più ampio di alcune funzionalità di SQL Server non presenti in SQL Data Warehouse, vedere la [documentazione per la migrazione][]. Dopo aver generato il report, sarà possibile salvarlo e aprirlo in Excel.

Si noti che durante la generazione dello schema della migrazione, la maggior parte dei problemi identificati come 'Object' verrà rettificata in modo da consentire la migrazione immediata di tali dati. Esaminare le modifiche per assicurarsi che non siano necessarie ulteriori rettifiche prima dell'applicazione dello schema.

## Eseguire la migrazione dello schema

Dopo la connessione, se si seleziona 'Migrate Schema' verrà generato uno script di migrazione dello schema per le tabelle selezionate. Lo script importa la struttura della tabella, mappa i tipi di dati incompatibili a formati più compatibili e crea le credenziali di sicurezza e lo schema, se indicato dall'utente nelle impostazioni di migrazione. Il codice può essere eseguito nell'istanza di SQL Data Warehouse di destinazione, salvato in un file, copiato negli Appunti e persino modificato inline prima di procedere con altre operazioni.

Come indicato in precedenza, durante la migrazione dello schema esaminare le modifiche di migrazione apportate dallo strumento e assicurarsi di comprenderle completamente.

## Eseguire la migrazione dei dati

Se si fa clic sull'opzione 'Migrate Data', è possibile generare script bcp che spostano i dati prima in file flat nel server e quindi direttamente nell'istanza di SQL Data Warehouse. Si consiglia di adottare questo processo per lo spostamento di piccole quantità di dati poiché i tentativi non sono incorporati e potrebbero verificarsi errori in caso di perdita della connessione di rete. Per l'esecuzione del processo è necessario aver installato l'utilità della riga di comando BCP e aver creato lo schema per i dati.

Dopo aver specificato i parametri indicati sopra, è necessario fare clic su 'Run Migration'. Verrà generato un set di due pacchetti nel percorso specificato. Eseguire il file di esportazione per esportare i dati dall'origine della migrazione in file flat ed eseguire il file di importazione per importare i dati in SQL Data Warehouse.

## Passaggi successivi
Dopo aver eseguito la migrazione di alcuni dati, è possibile passare alle attività di [sviluppo][].

<!--Image references-->

<!--Article references-->
[documentazione per la migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppo]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Scaricare l'utilità di migrazione]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=AcomDC_0810_2016-->
