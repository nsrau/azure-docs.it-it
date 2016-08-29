<properties
	pageTitle="Identificare database e tabelle per Database Estensione eseguendo l'ottimizzazione guidata Database Estensione | Microsoft Azure"
	description="Informazioni su come identificare database e tabelle candidati per Database Estensione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Identificare database e tabelle per l'estensione database eseguendo Gestione estensione database

Per identificare i database e le tabelle per l'estensione database, scaricare Gestione spazio aggiornamenti di SQL Server 2016 ed eseguire Gestione estensione database. L'ottimizzazione guidata Estensione database consente anche di identificare i problemi di blocco.

## Scaricare e installare Preparazione aggiornamento
È possibile scaricare e installare Gestione spazio aggiornamenti da [qui](http://go.microsoft.com/fwlink/?LinkID=613421). Questo strumento non è incluso nei supporti di installazione di SQL Server.

## Eseguire l'ottimizzazione guidata Database Estensione

1.  Eseguire Preparazione aggiornamento.

2.  Selezionare **Scenari**, quindi selezionare **Esegui Gestione estensione database**.

3.  Nel pannello **Esegui Gestione estensione database** fare clic su **Seleziona database da analizzare**.

4.  Nel pannello **Seleziona database** immettere o selezionare il nome del server e le informazioni di autenticazione. Fare clic su **Connect**.

5.  Viene visualizzato un elenco dei database nel server selezionato. Selezionare i database che si desidera analizzare. Fare clic su **Seleziona**.

6.  Nel pannello **Esegui Gestione estensione database** fare clic su **Esegui**. Viene avviata l'analisi.

## Esaminare i risultati

1.  Al termine dell'analisi, nel pannello **Database analizzati** selezionare uno dei database analizzati per visualizzare il pannello **Risultati analisi**.

    Il pannello **Risultati analisi** elenca le tabelle consigliate nel database selezionato che soddisfano i criteri di raccomandazione predefiniti.

2.  Nell'elenco delle tabelle nel pannello **Risultati analisi** selezionare una delle tabelle consigliate per visualizzare il pannello **Risultati tabella**.

    Eventuali problemi di blocco della tabella selezionata sono elencati nel pannello **Risultati tabella**. Per informazioni sui problemi di blocco rilevati dall'ottimizzazione guidata dell'estensione database, vedere [Limitazioni di Estensione database](sql-server-stretch-database-limitations.md).

3.  Nell'elenco dei problemi di blocco del pannello **Risultati tabella** selezionare uno dei problemi per visualizzare ulteriori informazioni sul problema selezionato e alcune procedure di prevenzione consigliate. Implementare i passaggi suggeriti per configurare la tabella selezionata per Database Estensione.

## Passaggio successivo
Abilitare Database Estensione.

-   Per abilitare Database Estensione in un **database**, vedere [Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md).

-   Per abilitare Estensione database in un'altra **tabella** quando l'estensione è già abilitata nel database, vedere [Abilitare Database Estensione per una tabella](sql-server-stretch-database-enable-table.md).

## Vedere anche

[Limitazioni di Estensione database](sql-server-stretch-database-limitations.md)

[Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md)

[Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md)

[Tutti gli argomenti per il servizio Estensione database di SQL Server di Azure](sql-server-stretch-database-index-all-articles.md)

<!---HONumber=AcomDC_0817_2016-->