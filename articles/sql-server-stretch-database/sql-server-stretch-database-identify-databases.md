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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Identificare database e tabelle per Database Estensione eseguendo l'ottimizzazione guidata Database Estensione

Per identificare i database e le tabelle che sono candidati per il Database Estensione, scaricare Preparazione aggiornamento di SQL Server 2016 ed eseguire l'ottimizzazione guidata Database Estensione. L'ottimizzazione guidata Database Estensione consente anche di identificare i problemi di blocco.

## Scaricare e installare Preparazione aggiornamento
È possibile scaricare e installare Preparazione aggiornamento da [qui](http://go.microsoft.com/fwlink/?LinkID=613421). Questo strumento non è incluso nei supporti di installazione di SQL Server.

## Eseguire l'ottimizzazione guidata Database Estensione

1.  Eseguire Preparazione aggiornamento.

2.  Selezionare **Scenari**, quindi selezionare **ESEGUI OTTIMIZZAZIONE GUIDATA DATABASE ESTENSIONE**.

3.  Nel pannello **Esegui ottimizzazione guidata Database Estensione** fare clic su **SELEZIONA DATABASE DA ANALIZZARE**.

4.  Nel pannello **Seleziona database** fare clic su **ISTANZA SQL**.

5.  Nel pannello **Connetti a istanza SQL** immettere il nome dell'istanza di SQL Server. Fare clic su **Connetti**.

6.  Nel pannello **Seleziona database** selezionare i database da analizzare. Quindi fare clic su **Seleziona**.

7.  Nel pannello **Esegui ottimizzazione guidata Database Estensione** fare clic su **Esegui**. Viene avviata l'analisi.

## Esaminare i risultati

1.  Al termine dell'analisi, nel pannello **Ottimizzazione guidata Database Estensione** selezionare uno dei database analizzati per visualizzare il pannello **Risultati analisi**.

    Il pannello **Risultati analisi** elenca le tabelle consigliate nel database selezionato che soddisfano i criteri di raccomandazione predefiniti. Se si desidera, modificare la dimensione minima e il numero di righe per espandere o comprimere l'elenco delle tabelle consigliate.

2.  Nell'elenco delle tabelle consigliate nel pannello **Risultati analisi** selezionare una delle tabelle consigliate per visualizzare il pannello **Risultati tabella**.

    Nel pannello **Risultati tabella** sono elencati i problemi di blocco della tabella selezionata. Per informazioni sui problemi di blocco rilevati dall'ottimizzazione guidata Database Estensione, vedere [Limitazioni della superficie di attacco e problemi di blocco per Database Estensione](sql-server-stretch-database-limitations.md).

3.  Nell'elenco dei problemi di blocco nel pannello **Risultati tabella** selezionare uno dei problemi per visualizzare il pannello **Risultati regola**.

    Il pannello **Risultati regola** descrive il problema selezionato e propone procedure di prevenzione. Implementare i passaggi suggeriti per configurare la tabella selezionata per Database Estensione.

## Passaggio successivo
Abilitare Database Estensione.

-   Per abilitare Database Estensione in un **database**, vedere [Abilitazione di Database Estensione per un database](sql-server-stretch-database-enable-database.md).

-   Per abilitare Database Estensione in un'altra **tabella** quando l'estensione è già abilitata nel database, vedere [Abilitazione di Database Estensione per una tabella](sql-server-stretch-database-enable-table.md).

## Vedere anche
[Limitazioni della superficie di attacco e problemi di blocco per Database Estensione](sql-server-stretch-database-limitations.md) [Abilitazione di Database Estensione per un database](sql-server-stretch-database-enable-database.md) [Abilitazione di Database Estensione per una tabella](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0309_2016-->