---
title: Progettare il primo database SQL di Azure - C# | Microsoft Docs
description: Informazioni su come progettare il primo database SQL di Azure e connettersi a esso con un programma C# tramite ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727166"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Esercitazione: Progettare un database SQL di Azure e connettersi con C&#x23; e ADO.NET

Il database SQL di Azure è un database relazionale distribuito come servizio in Microsoft Cloud (Azure). In questa esercitazione viene illustrato come usare il portale di Azure e ADO.NET con Visual Studio per eseguire queste operazioni:

> [!div class="checklist"]
> * Creare un database nel portale di Azure
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Connettersi al database con ADO.NET e Visual Studio
> * Creare tabelle con ADO.NET
> * Inserire, aggiornare ed eliminare dati con ADO.NET
> * Eseguire query sui dati con ADO.NET

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Un'installazione di [Visual Studio 2017](https://www.visualstudio.com/downloads/)

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato le attività di base che è possibile eseguire con i database, ad esempio creare database e tabelle, connettersi al database, caricare dati ed eseguire query. Si è appreso come:

> [!div class="checklist"]
> * Creare un database
> * Configurare una regola del firewall
> * Connettersi al database con [Visual Studio e C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Creare tabelle
> * Inserire, aggiornare ed eliminare dati ed eseguire query su di essi

Passare all'esercitazione successiva per informazioni sulla migrazione di dati.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di SQL Server al database SQL di Azure offline con Servizio Migrazione del database](../dms/tutorial-sql-server-to-azure-sql.md)
