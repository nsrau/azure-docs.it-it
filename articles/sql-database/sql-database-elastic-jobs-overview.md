<properties 
	pageTitle="Panoramica dei processi di database elastici" 
	description="Viene illustrato il servizio processo di database elastico" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# Panoramica dei processi di database elastici

I **processi di database elastici** (anteprima) consentono di eseguire script T-SQL (processi) su tutti i database di un [pool di database elastici (anteprima)](sql-database-elastic-pool.md). Ad esempio, è possibile aggiornare facilmente lo schema in ogni database per includere una nuova tabella. In genere, è necessario connettersi a ciascun database in modo indipendente per eseguire istruzioni T-SQL o effettuare altre attività amministrative. Un **processo di database elastici** consente di gestire l’attività di esecuzione dell’accesso e di eseguire in modo automatico e affidabile l'esecuzione dello script, registrando al contempo lo stato di esecuzione per ciascun database. Per istruzioni sull'installazione dell'anteprima, vedere [Installazione dei componenti del processo di database elastico](sql-database-elastic-jobs-service-installation.md).

![Servizio processo di database elastico][1]

## Vantaggi

* Definire, gestire e mantenere gli script T-SQL da eseguire in un pool di database elastici
* Eseguire gli script T-SQL in modo affidabile con la ripetizione automatica e su scala
* Tenere traccia dello stato di esecuzione dei processi

## Scenari

* Esecuzione di attività amministrative, ad esempio distribuire un nuovo schema
* Aggiornamento dei dati di riferimento, ad esempio informazioni di prodotto comuni tra tutti i database
* Ricompilazione degli indici per migliorare le prestazioni delle query

## Funzionamento del processo

1.	Installare i servizi utilizzati dai processi di database elastici. Vedere [Installazione dei processi di database elastici](sql-database-elastic-jobs-service-installation.md). Se l'installazione non riesce, vedere [come disinstallare](sql-database-elastic-jobs-uninstall.md).
2.	Configurare il pool di database elastico per l'esecuzione del processo mediante l’[aggiunta di un utente a ogni database](sql-database-elastic-jobs-add-logins-to-dbs.md).
3.	Creare uno script T-SQL itempotent che può essere eseguito su ogni database nel pool.
4.	Attenersi alla seguente procedura per eseguire lo script: [Creazione e gestione di processi elastici di database](sql-database-elastic-jobs-create-and-manage.md) 

## Componenti e prezzi 

I seguenti componenti interagiscono per creare un servizio Cloud di Azure che consente l'esecuzione ad hoc dei processi di amministrazione. I componenti vengono installati e configurati automaticamente durante la configurazione, nella sottoscrizione. È possibile identificare i servizi poiché hanno tutti lo stesso nome generato automaticamente. Il nome è univoco ed è costituito dal prefisso "edj" seguito da 21 caratteri generati casualmente.

* **Servizio cloud di Azure**: i processi di database elastica (anteprima) vengono recapitati come servizio cloud di Azure ospitato dal cliente per l'esecuzione delle attività richieste. Dal portale, il servizio viene distribuito e ospitato nella sottoscrizione Microsoft Azure. Il servizio predefinito distribuito viene eseguito con un numero minimo di due ruoli di lavoro per la disponibilità elevata. La dimensione predefinita di ogni ruolo di lavoro (ElasticDatabaseJobWorker) viene eseguita in un'istanza A0. Per informazioni sui prezzi, vedere [Servizi cloud Prezzi](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Database SQL di Azure**: il servizio usa un database SQL di Azure noto come **database di controllo** per archiviare tutti i metadati del processo. Il livello di servizio predefinito è S0. Per informazioni sui prezzi, vedere [Database SQL Prezzi](http://azure.microsoft.com/pricing/details/sql-database/).
* **Bus di servizio di Azure**: è destinato alla coordinazione del lavoro all'interno del servizio cloud di Azure. Vedere [Bus di servizio Prezzi](http://azure.microsoft.com/pricing/details/service-bus/).
* **Archiviazione di Azure**: un account di Archiviazione di Azure viene utilizzato per archiviare la registrazione dell’output nell’evento che un problema richieda un debugging ulteriore (una pratica comune per la [diagnostica di Azure](../cloud-services-dotnet-diagnostics.md)). Per informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](http://azure.microsoft.com/pricing/details/storage/).

## Passaggi successivi
[Installare i componenti](sql-database-elastic-jobs-service-installation.md), quindi [creare e aggiungere un log a ciascun database nel pool](sql-database-elastic-jobs-add-logins-to-dbs.md). Per comprendere la creazione e la gestione del processo, vedere [Creazione e gestione di processi elastici di database](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO4-->