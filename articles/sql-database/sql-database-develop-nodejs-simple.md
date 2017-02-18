---
title: Connettersi al database SQL tramite Node.js | Documentazione Microsoft
description: "Presentazione di un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: 6c3f6bc00d147bc498a859560ffe4719ecf888bf


---
# <a name="connect-to-sql-database-by-using-nodejs"></a>Connettersi al Database SQL tramite Node.js
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

In questo argomento viene illustrato come connettersi a un database SQL di Azure ed eseguire query tramite Node.js. È possibile eseguire questo esempio da piattaforme Windows, Ubuntu Linux o Mac.

## <a name="step-1-configure-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo
[Prerequisiti per l'uso del driver Tedious Node.js per SQL Server](https://docs.microsoft.com/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/)

## <a name="step-2-create-a-sql-database"></a>Passaggio 2: Creare un database SQL
Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Passaggio 3: Ottenere i dettagli di connessione
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Passaggio 4: Eseguire il codice di esempio
[Modello di verifica della connessione a SQL tramite Node.js](https://docs.microsoft.com/sql/connect/node-js/step-3-proof-of-concept-connecting-to-sql-using-node-js/)

## <a name="next-steps"></a>Passaggi successivi
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni sui [driver Microsoft Node.js per SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


