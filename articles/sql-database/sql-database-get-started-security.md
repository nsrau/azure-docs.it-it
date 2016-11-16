---
title: 'Esercitazione sul database SQL: Introduzione alla sicurezza'
description: Informazioni su come creare account utente per accedere a un database e gestirlo.
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3bd8ac5466ae67df0a1865509c8fb897d1374ec


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Esercitazione sul database SQL: Creare account utente del database SQL per accedere a un database e gestirlo
> [!div class="op_single_selector"]
> * [Esercitazione introduttiva](sql-database-get-started-security.md)
> * [Concedere l'accesso](sql-database-manage-logins.md)
> 
> 

Questa esercitazione illustra come usare SQL Server Management Studio (SSMS) per le operazioni seguenti:

* Accedere al database SQL con un accesso entità a livello di server.
* Creare un account utente del database SQL.
* Concedere [autorizzazioni db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) a un utente del database SQL.
* Connettersi a un database SQL con un account utente diverso da un'entità a livello di server.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere completato questa esercitazione sul database SQL e avere creato un account utente e concesso all'account utente le autorizzazioni dbo, è possibile saperne di più sulla [sicurezza del database SQL](sql-database-manage-logins.md).




<!--HONumber=Nov16_HO2-->


