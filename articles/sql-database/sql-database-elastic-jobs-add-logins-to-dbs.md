<properties 
	title="How to add a users to an elastic database pool" 
	pageTitle="Come aggiungere un utente a un pool elastico di database" 
	description="È necessario aggiungere un utente con privilegi a ogni database del pool" 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh" />

# Come aggiungere utenti a un pool elastico di database

I processi elastici di database consentono di eseguire lo stesso script su ogni database di un [pool elastico di database](sql-database-elastic-pool.md). Per eseguire lo script, è necessario aggiungere un utente con le autorizzazioni appropriate a ogni database del pool. Per altre informazioni, vedere [Gestione di database, account di accesso e utenti in database SQL di Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) o l'articolo relativo all'[aggiunta di utenti al database SQL di Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Prerequisiti
* [Creare un pool elastico di database \(anteprima\)](sql-database-elastic-pool-portal.md)
* Installare i [componenti del processo elastico](sql-database-elastic-jobs-service-installation.md). 

## Come aggiungere utenti ai database

1.	È prima necessario connettersi al **master** del database SQL di Azure in cui si trovano i database del pool elastico e creare un nuovo account di accesso usando le stesse credenziali fornite durante l'installazione dei **processi elastici di database**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Accedere a ogni database del pool e creare un utente usando lo stesso nome e la stessa password. L'utente deve disporre di autorizzazioni sufficienti per eseguire il processo. È necessario eseguire il codice su ciascun database.

		CREATE USER admin1 FROM LOGIN login1;
		
3. L'utente deve disporre anche di autorizzazioni sufficienti per l'esecuzione dello script specificato per il processo. Seguire la procedura **sp\_addrolemember** per fornire all'utente le autorizzazioni minime necessarie per eseguire correttamente lo script.

## Passaggi successivi

Eseguire un processo sul pool di database. Vedere [Creazione e gestione di processi elastici di database](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=58_postMigration-->