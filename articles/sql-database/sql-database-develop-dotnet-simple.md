---
title: Connettersi al database SQL tramite .NET (C#) | Documentazione Microsoft
description: Usare il codice di esempio in questa guida introduttiva per creare con C# un&quot;applicazione moderna, supportata da un database relazionale potente nel cloud con il database SQL di Azure.
services: sql-database
documentationcenter: 
author: tobbox
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/03/2017
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cc756c662b97c64ab30a7d5bf2cc325f9e74d905
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-net-c"></a>Connettersi al database SQL tramite .NET (C#)
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## <a name="step-1--configure-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo
[Configurare l'ambiente di sviluppo per lo sviluppo di ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-1-configure-development-environment-for-ado-net-development/)

## <a name="step-2-create-a-sql-database"></a>Passaggio 2: Creare un database SQL
Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.  

## <a name="step-3--get-connection-string"></a>Passaggio 3: Ottenere la stringa di connessione
[!INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Passaggio 4: Eseguire il codice di esempio
* [Modello di verifica della connessione a SQL tramite ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-3-proof-of-concept-connecting-to-sql-using-ado-net/)
* [Connettersi in modo resiliente a SQL tramite ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net/)

## <a name="next-steps"></a>Passaggi successivi
* [Creare un'app ASP.NET MVC con autenticazione e database SQL e distribuirla nel servizio app di Azure](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni su [Microsoft ADO.Net for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server/)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)


