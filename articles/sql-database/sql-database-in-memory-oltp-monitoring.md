<properties
	pageTitle="Monitorare l'archiviazione in memoria XTP | Microsoft Azure"
	description="Stimare e monitorare l'uso e la capacità delle risorse di archiviazione in memoria XTP e risolvere l'errore di capacità 41805"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Monitorare l'archiviazione in memoria XTP

Per informazioni sul livello di servizio e sull'archiviazione, vedere l'articolo [Livelli di servizio del database SQL](sql-database-service-tiers.md).

Quando si usano le tecnologie [in memoria](sql-database-in-memory.md), i dati nelle tabelle con ottimizzazione per la memoria e le variabili tabella si trovano in file XTP nelle risorse di archiviazione in memoria. Ogni livello di servizio Premium ha una dimensione massima delle risorse di archiviazione in memoria. Dopo il superamento di questo limite, è possibile che le operazioni di inserimento e aggiornamento abbiano esito negativo con errore 41805. Sarà quindi necessario eliminare dati per recuperare memoria oppure aggiornare il livello di prestazioni del database.

## Determinare se i dati rientreranno nel limite di archiviazione in memoria

La stima dei requisiti di memoria per una tabella con ottimizzazione per la memoria in SQL Server è analoga alla stima eseguita nel database SQL di Azure. È consigliabile rivedere l'argomento corrispondente su [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Si noti che la tabella e le righe di variabili tabella, oltre agli indici, vengono incluse nel calcolo delle dimensioni massime dei dati utente. ALTER TABLE, inoltre, necessita di spazio sufficiente per creare una nuova versione dell'intera tabella e dei relativi indici.

##### Dimensioni massime dell'archiviazione in memoria XTP per livelli di servizio Premium:



È possibile monitorare l'uso dell'archiviazione in memoria nel [portale](http://portal.azure.com/) di Azure:

- Nel pannello Database individuare la casella Utilizzo risorse e fare clic su Modifica.
- Selezionare quindi la metrica relativa alla percentuale di archiviazione in memoria XTP.

In alternativa, usare la query seguente per visualizzare l'utilizzo delle risorse di archiviazione in memoria:

    select xtp_storage_percent from sys.dm_db_resource_stats

## Correggere le situazioni di memoria insufficiente - Errore 41805

Se la memoria è insufficiente, le operazioni INSERT, UPDATE e CREATE avranno esito negativo con messaggio di errore 41805.

Il messaggio di errore 41805 indica che le tabelle con ottimizzazione per la memoria e le variabili tabella hanno superato le dimensioni massime.

Per risolvere l'errore:


- Eliminare i dati dalle tabelle con ottimizzazione per la memoria, eseguendo potenzialmente l'offload dei dati in tabelle tradizionali basate su disco oppure
- Aggiornare il livello di servizio selezionandone uno con risorse di archiviazione in memoria sufficienti per i dati da mantenere nelle tabelle con ottimizzazione con memoria.

## Passaggi successivi
Altre informazioni sul [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->