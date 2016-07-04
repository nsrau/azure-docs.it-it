<properties
	pageTitle="Connettersi al database SQL tramite PHP in Windows | Microsoft Azure"
	description="Presentazione di un programma PHP di esempio che si connette al database SQL di Azure da un client Windows e fornisce collegamenti ai componenti software necessari per il client."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="meetb"/>


# Connettersi al database SQL tramite PHP in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento illustra come connettersi al database SQL di Azure da un'applicazione client scritta in PHP che viene eseguita in Windows.

## Passaggio 1: Configurare l'ambiente di sviluppo

[Configurare l'ambiente di sviluppo PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## Passaggio 2: Creare un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Passaggio 3: Ottenere i dettagli di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Passaggio 4: Eseguire il codice di esempio

* [Modello di verifica della connessione a SQL tramite PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Connettersi in modo resiliente a SQL tramite PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## Passaggi successivi

* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni su [Microsoft PHP Driver per SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Per altre informazioni sull'installazione e l'uso di PHP, vedere [Accesso ai database di SQL Server con PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## Risorse aggiuntive 

* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->