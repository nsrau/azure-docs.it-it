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
ms.date: 06/20/2017
ms.author: genemi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 069656b495929d66f6a47b38141f56b6bf0ac92f
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Progettare un database SQL di Azure e connettersi con C&#x23; e ADO.NET

Il database SQL di Azure è un database relazionale come servizio (DBaaS, Database-As-A-Service) disponibile in Microsoft Cloud ("Azure"). In questa esercitazione si apprenderà come usare il portale di Azure e ADO.NET per: 

> [!div class="checklist"]
> * Creare un database nel portale di Azure
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Connettersi al database con ADO.NET
> * Creare tabelle con ADO.NET
> * Inserire dati con ADO.NET 
> * Eseguire query su tali dati con ADO.NET

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Passaggi successivi

Sono disponibili i passaggi successivi seguenti:

- [Usare LINQ nel programma C# per le query SQL](https://msdn.microsoft.com/library/bb425822.aspx)
- [Eseguire la migrazione di un database SQL Server a un database SQL di Azure](sql-database-migrate-your-sql-server-database.md)

