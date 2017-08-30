---
title: Progettare il primo database SQL di Azure - C# | Microsoft Docs
description: Informazioni su come progettare il primo database SQL di Azure e connettersi a esso con un programma C# tramite ADO.NET.
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 08/25/2017
ms.author: genemi
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: d9731cf5399cce6f103129ccda521f2867bd8da6
ms.contentlocale: it-it
ms.lasthandoff: 08/02/2017

---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Progettare un database SQL di Azure e connettersi con C&#x23; e ADO.NET

Il database SQL di Azure è un database relazionale come servizio (DBaaS, Database-As-A-Service) disponibile in Microsoft Cloud ("Azure"). In questa esercitazione viene illustrato come usare il portale di Azure e ADO.NET con Visual Studio per eseguire queste operazioni: 

> [!div class="checklist"]
> * Creare un database nel portale di Azure
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Connettersi al database con ADO.NET e Visual Studio
> * Creare tabelle con ADO.NET
> * Inserire, aggiornare ed eliminare dati con ADO.NET 
> * Eseguire query sui dati con ADO.NET

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Un'installazione di [Visual Studio Community 2017, Visual Studio Professional 2017 o Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato le attività di base che è possibile eseguire con i database, come creare database e tabelle, caricare dati, eseguire query sui dati e ripristinare un database a un momento precedente. Si è appreso come:
> [!div class="checklist"]
> * Creare un database
> * Configurare una regola del firewall
> * Connettersi al database con [Visual Studio e C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Creare tabelle
> * Inserire, aggiornare ed eliminare i dati
> * Eseguire query sui dati

Passare all'esercitazione successiva per ottenere informazioni sulla migrazione dei dati.

> [!div class="nextstepaction"]
>[Eseguire la migrazione di un database SQL Server a un database SQL di Azure](sql-database-migrate-your-sql-server-database.md)


