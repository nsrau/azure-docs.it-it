---
title: Connettersi al database SQL tramite Java con JDBC in Windows | Documentazione Microsoft
description: "Presentazione di un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L&quot;esempio usa JDBC e viene eseguito su un computer client Windows."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: aafc89ca958e4bdc2ba52bf1a7d379e3a4cddf1c
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Connettersi al database SQL tramite Java con JDBC in Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento presenta un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio Java si basa su Java Development Kit (JDK) versione 1.8 e si connette a un database SQL di Azure tramite il driver JDBC.

## <a name="step-1--configure-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo
[Configurare l'ambiente di sviluppo Java](https://docs.microsoft.com/sql/connect/jdbc/step-1-configure-development-environment-for-java-development/)

## <a name="step-2-create-a-sql-database"></a>Passaggio 2: Creare un database SQL
Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database.  

## <a name="step-3-get-connection-string"></a>Passaggio 3: Ottenere la stringa di connessione
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> Se si usa il driver JDBC JTDS, sarà necessario aggiungere "ssl = require" all'URL della stringa di connessione ed è necessario impostare l'opzione seguente per JVM "-Djsse.enableCBCProtection=false". Questa opzione JVM disattiva la correzione di una vulnerabilità di sicurezza,quindi assicurarsi di comprendere i rischi coinvolti prima di impostare questa opzione.
> 
> 

## <a name="step-4-run-sample-code"></a>Passaggio 4: Eseguire il codice di esempio
* [Modello di verifica della connessione a SQL tramite Java](https://docs.microsoft.com/sql/connect/jdbc/step-3-proof-of-concept-connecting-to-sql-using-java/)

## <a name="next-steps"></a>Passaggi successivi
* Visitare il [Centro per sviluppatori Java](/develop/java/).
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni su [Microsoft JDBC Driver per SQL Server](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)


