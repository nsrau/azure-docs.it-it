<properties
   pageTitle="Usare Analisi di flusso di Azure con SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di Analisi di flusso di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/02/2015"
   ms.author="sahajs;twounder"/>

# Usare Analisi di flusso di Azure con SQL Data Warehouse

Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per informazioni di base, vedere [Introduzione ad Analisi di flusso di Azure][]. È possibile apprendere come creare una soluzione end-to-end con Analisi di flusso seguendo l’esercitazione [Introduzione all’uso di Analisi di flusso di Azure][].

In questo articolo si apprenderà come usare il database di Azure SQL Data Warehouse come un sink di output per i processi di Analisi di flusso.

## Prerequisiti

Innanzitutto, eseguire i passaggi seguenti nell’esercitazione [Introduzione all'utilizzo di Analisi di flusso di Azure][].

1. Creare un input dell'hub eventi
2. Configurare e avviare l'applicazione di generazione di eventi
3. Eseguire il provisioning di un processo di Analisi dei flussi
4. Specificare la query e l'input del processo

Creare quindi un database di Azure SQL Data Warehouse

## Specificare l'output del processo: database di Azure SQL Warehouse

### Passaggio 1
Nel processo di Analisi dei flussi fare clic su **OUTPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI OUTPUT**.

### Passaggio 2
Selezionare il Database SQL e fare clic su Avanti. ![][Add Output]

### Passaggio 3
Immettere i valori seguenti nella pagina successiva:

- *Alias di output*: immettere un nome descrittivo per l'output del processo.
- *Sottoscrizione*
	- Se il database SQL Data Warehouse esiste nella stessa sottoscrizione del processo di analisi di flusso, selezionare Usare il database SQL dalla sottoscrizione corrente.
	- Se il database è in una sottoscrizione diversa, selezionare Usare il database SQL da un'altra sottoscrizione.
- *Database*: specificare il nome di un database di destinazione.
- *Nome server*: specificare il nome del server per il database specificato. Per trovarlo, è possibile usare il portale di Azure.

![][Server Name]

- *Nome utente*: digitare il nome utente di un account con autorizzazioni di scrittura nel database.
- *Password*: fornire la password per l'account utente specificato.
- *Tabella*: specificare il nome della tabella di destinazione nel database.

![][Add Database]

### Passaggio 4
Fare clic sul pulsante con il segno di spunta per aggiungere l'output del processo e per verificare che Analisi di flusso possa connettersi al database.

![][Test Connection]

Quando la connessione al database riesce, verrà visualizzata una notifica nella parte inferiore del portale. È possibile scegliere Test connessione nella parte inferiore per testare la connessione al database.




## Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][].

Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Introduzione ad Analisi di flusso di Azure]: stream-analytics-introductiond.md
[Introduzione all'utilizzo di Analisi di flusso di Azure]: stream-analytics-get-started.md
[Introduzione all’uso di Analisi di flusso di Azure]: stream-analytics-get-started.md
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[Panoramica dell'integrazione di SQL Data Warehouse]: sql-data-warehouse-overview-integration.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=Sept15_HO2-->