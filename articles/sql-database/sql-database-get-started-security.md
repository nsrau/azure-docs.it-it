<properties
	pageTitle="Esercitazione sul database SQL: Introduzione alla sicurezza"
	description="Informazioni su come creare account utente per accedere a un database e gestirlo."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="carlrab"/>

# Esercitazione sul database SQL: Creare account utente del database SQL per accedere a un database e gestirlo con il portale di Azure


> [AZURE.SELECTOR]
- [Esercitazione introduttiva](sql-database-get-started-security.md)
- [Concedere l'accesso](sql-database-manage-logins.md)

In questa esercitazione si apprenderà come usare il portale di Azure per:

- Accedere al database SQL con un accesso entità a livello di server
- Creare un account utente del database SQL
- Concedere a un account utente del database SQL le autorizzazioni dbo in un database utente
- Connettersi a un database SQL con un account utente diverso da un'entità a livello di server 

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Creare un server logico del database SQL](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Passaggi successivi
Dopo avere completato questa esercitazione sul database SQL e avere creato un account utente e concesso all'account utente le autorizzazioni dbo, è possibile saperne di più sulla [sicurezza del database SQL](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0615_2016-->