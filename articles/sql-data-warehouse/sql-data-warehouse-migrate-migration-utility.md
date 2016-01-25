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
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


#Utilità di migrazione per data warehouse (anteprima)

> [AZURE.SELECTOR]
- [Download Migration Utility](https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip)

L'utilità di migrazione per data warehouse è uno strumento progettato per eseguire la migrazione dello schema e dei dati da SQL Server e dal database SQL di Azure ad Azure SQL Data Warehouse. Durante la migrazione dello schema, lo strumento mappa automaticamente lo schema corrispondente dall'origine alla destinazione. Una volta completata la migrazione dello schema, agli utenti viene presentata l'opzione per spostare i dati tramite script generati automaticamente.

Oltre alla migrazione dello schema e dei dati, lo strumento consente agli utenti di generare report di compatibilità che riepilogano le incompatibilità riscontrate tra le istanze di destinazione e di origine che possono impedire la corretta migrazione.

##Attività iniziali
Per scaricare l'utilità di migrazione per data warehouse, fare clic [qui][]. Come prerequisito per l'installazione, è necessario installare l'utilità della riga di comando bcp per l'esecuzione degli script di migrazione e Office per la visualizzazione del report di compatibilità. Dopo aver fatto clic sull'eseguibile scaricato, sarà necessario accettare le condizioni di licenza standard prima di procedere all'installazione dello strumento.

###Avvio dello strumento e connessione
Per avviare lo strumento, è sufficiente fare clic sull'icona che viene visualizzata sul desktop al termine dell'installazione. All'apertura dello strumento verrà visualizzata una pagina di connessione iniziale in cui sarà possibile scegliere l'origine e la destinazione per lo strumento di migrazione. Al momento, sono supportati SQL Server e il database SQL di Azure come origini e SQL Data Warehouse come destinazione. Una volta effettuata la selezione, verrà richiesto di connettersi al server di origine. A questo scopo, sarà necessario specificare il nome del server, eseguire l'autenticazione e quindi fare clic su 'Connect'.
 
Dopo l'autenticazione, lo strumento visualizzerà un elenco di database presenti nel server con cui è stata stabilita la connessione. Per avviare la migrazione, selezionare il database desiderato e fare clic su 'Migrate selected'.
 
##Report di migrazione
Se si seleziona 'Check Database Compatibility' nello strumento, verrà generato un report che riepiloga tutte le incompatibilità rilevate negli oggetti del database di cui si vuole eseguire la migrazione. Per un elenco più ampio di alcune funzionalità di SQL Server non presenti in SQL Data Warehouse, vedere la [documentazione per la migrazione][]. Una volta generato il report, sarà possibile salvarlo e aprirlo in Excel.

Si noti che durante la generazione dello schema della migrazione, la maggior parte dei problemi identificati come 'Object' verrà rettificata in modo da consentire la migrazione immediata di tali dati. Esaminare le modifiche per assicurarsi che non siano necessarie ulteriori rettifiche prima dell'applicazione dello schema.

##Migrazione dello schema

Dopo la connessione, se si seleziona 'Migrate Schema' verrà generato uno script di migrazione dello schema per le tabelle selezionate. Lo script importa la struttura della tabella, mappa i tipi di dati incompatibili a formati più compatibili e crea le credenziali di sicurezza e lo schema, se indicato dall'utente nelle impostazioni di migrazione. Il codice può essere eseguito nell'istanza di SQL Data Warehouse di destinazione, salvato in un file, copiato negli Appunti e persino modificato inline prima di procedere con altre operazioni.
 
Come indicato in precedenza, durante la migrazione dello schema esaminare le modifiche di migrazione apportate dallo strumento e assicurarsi di comprenderle completamente.

##Migrazione dei dati

Se si fa clic sull'opzione 'Migrate Data', è possibile generare script bcp che spostano i dati prima in file flat nel server e quindi direttamente nell'istanza di SQL Data Warehouse. Questo processo è consigliato per lo spostamento di quantità di dati ridotte, e, dal momento che non è disponibile una funzionalità incorporata per l'esecuzione automatica di ulteriori tentativi, tenere presente che possono verificarsi errori a causa delle interruzioni di rete. Per l'esecuzione del processo è necessario aver installato l'utilità della riga di comando bcp e aver creato lo schema per i dati.
 
Dopo aver specificato i parametri indicati sopra, è necessario fare clic su 'Run Migration'. Verrà generato un set di due pacchetti nel percorso specificato. Eseguire il file di esportazione per esportare i dati dall'origine della migrazione in file flat ed eseguire il file di importazione per importare i dati in SQL Data Warehouse.

## Passaggi successivi
Dopo aver eseguito la migrazione di alcuni dati, è possibile passare alle attività di [sviluppo][].

<!--Image references-->

<!--Article references-->
[documentazione per la migrazione]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-migrate/
[sviluppo]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-develop/
[qui]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=AcomDC_0114_2016-->