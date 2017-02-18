---
title: Connettersi al database SQL tramite PHP in Windows | Documentazione Microsoft
description: Presentazione di un programma PHP di esempio che si connette al database SQL di Azure da un client Windows e fornisce collegamenti ai componenti software necessari per il client.
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: e1c3e7e0f6ca097e3ee41995defe5c1df666d39e


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Connettersi al database SQL tramite PHP in Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento illustra come connettersi al database SQL di Azure da un'applicazione client scritta in PHP che viene eseguita in Windows.

## <a name="step-1--configure-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo
[Configurare l'ambiente di sviluppo PHP](https://docs.microsoft.com/sql/connect/php/step-1-configure-development-environment-for-php-development/)

## <a name="step-2-create-a-sql-database"></a>Passaggio 2: Creare un database SQL
Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Passaggio 3: Ottenere i dettagli di connessione
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Passaggio 4: Eseguire il codice di esempio
* [Modello di verifica della connessione a SQL tramite PHP](https://docs.microsoft.com/sql/connect/php/step-3-proof-of-concept-connecting-to-sql-using-php/)
* [Connettersi in modo resiliente a SQL tramite PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php/)

## <a name="next-steps"></a>Passaggi successivi
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni su [Microsoft PHP Driver per SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server/)
* Per altre informazioni sull'installazione e l'uso di PHP, vedere [Accesso ai database di SQL Server con PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


