<properties 
	pageTitle="Come aggiungere un utente a un pool elastico di database" 
	description="È necessario aggiungere un utente con privilegi a ogni database del pool" 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# Come aggiungere utenti a un pool di database elastici

La funzione **processi di database elastici** (anteprima) consente di eseguire uno script Transact-SQL in un gruppo di database, compresa un insieme personalizzato di database, un **pool di database elastici**o un **insieme di partizioni di database elastici **nel database SQL di Azure. Per eseguire lo script, è necessario aggiungere un utente con le autorizzazioni appropriate a ogni database in cui si eseguirà il processo. Per altre informazioni, vedere [Gestione di database, account di accesso e utenti in database SQL di Azure](sql-database-manage-logins.md) o l'articolo relativo all'[aggiunta di utenti al database SQL di Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Prerequisiti
* Installare i [componenti del processo elastico](sql-database-elastic-jobs-service-installation.md). 

## Come aggiungere utenti ai database

1.	È prima necessario connettersi al **master** del database SQL di Azure in cui si trovano i database del pool elastico e creare un nuovo account di accesso usando le stesse credenziali fornite durante l'installazione dei **processi elastici di database**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Accedere a ogni database del pool e creare un utente usando lo stesso nome e la stessa password. L'utente deve disporre di autorizzazioni sufficienti per eseguire il processo. È necessario eseguire il codice su ciascun database.

		CREATE USER admin1 FROM LOGIN login1;
		
3. L'utente deve disporre anche di autorizzazioni sufficienti per l'esecuzione dello script specificato per il processo. Usare l’elemento [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) per fornire all'utente le autorizzazioni minime necessarie per eseguire correttamente lo script.

## Passaggi successivi

Per creare e gestire i processi con il portale di Azure, vedere [Creazione e gestione dei processi di database elastici](sql-database-elastic-jobs-create-and-manage.md). Per creare processi con PowerShell, vedere: [Creare e gestire processi di database elastici del database SQL tramite PowerShell (anteprima)](sql-database-elastic-jobs-powershell.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->