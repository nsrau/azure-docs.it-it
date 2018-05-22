---
title: Guide introduttive sulla connessione e l'esecuzione di query sui database SQL di Azure | Microsoft Docs
description: Guide introduttive sui database SQL di Azure che illustrano come connettersi a un database SQL di Azure ed eseguire query su di esso.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Guide introduttive sulla connessione e l'esecuzione di query sui database SQL di Azure

Il documento seguente include collegamenti ad esempi di Azure che mostrano come connettersi a un database SQL di Azure ed eseguire query su di esso. Contiene anche alcune indicazioni per la sicurezza a livello di trasporto.

## <a name="quickstarts"></a>Guide introduttive

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Questa guida introduttiva illustra come usare SSMS per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query e inserire, eliminare e aggiornare dati nel database.|
|[SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Questa guida introduttiva illustra come usare SQL Operations Studio (anteprima) per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL (T-SQL) per creare il database TutorialDB usato nelle esercitazioni di SQL Operations Studio (anteprima).|
|[Portale di Azure](sql-database-connect-query-portal.md)|Questa guida introduttiva illustra come usare l'editor di query per connettersi a un database SQL e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Questa guida introduttiva illustra come usare Visual Studio Code per connettersi a un database SQL di Azure e quindi come usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[.NET con Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Questa guida introduttiva illustra come usare .NET Framework per creare un programma C# con Visual Studio per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Questa guida introduttiva illustra come usare .NET Core in Windows/Linux/macOS per creare un programma C# per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Go](sql-database-connect-query-go.md)|Questa guida introduttiva illustra come usare Go per connettersi a un database SQL di Azure. Vengono illustrate anche istruzioni Transact-SQL per eseguire query e modificare i dati.|
|[Java](sql-database-connect-query-java.md)|Questa guida introduttiva illustra come usare Java per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Node.JS](sql-database-connect-query-nodejs.md)|Questa guida introduttiva illustra come usare Node.js per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[PHP](sql-database-connect-query-php.md)|Questa guida introduttiva illustra come usare PHP per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Python](sql-database-connect-query-python.md)|Questa guida introduttiva illustra come usare Python per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati. |
|[Ruby](sql-database-connect-query-ruby.md)|Questa guida introduttiva illustra come usare Ruby per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerazioni su TLS per la connettività del database SQL
Transport Layer Security, ovvero TLS, viene usato da tutti i driver offerti o supportati da Microsoft per la connessione al database SQL di Azure. Non è necessaria una configurazione speciale. Per tutte le connessioni a SQL Server o al database SQL di Azure, è consigliabile impostare le configurazioni seguenti, o configurazioni equivalenti, per tutte le applicazioni:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Alcuni sistemi usano parole chiave diverse ma equivalenti per le parole chiave di configurazione. Queste configurazioni garantiscono che il driver del client verifichi l'identità del certificato TLS ricevuto dal server.

È anche consigliabile disabilitare TLS 1.1 e 1.0 nel client se è necessario ai fini della conformità con Payment Card Industry - Data Security Standard, ovvero PCI-DSS.

I driver che non appartengono a Microsoft potrebbero non usare TLS per impostazione predefinita. Potrebbe trattarsi di un fattore da prendere in considerazione quando si esegue la connessione al database SQL di Azure. Le applicazioni con driver incorporati potrebbero non consentire all'utente idi controllare le impostazioni di connessione. Si consiglia di esaminare la sicurezza di tali applicazioni e driver prima di usarli nei sistemi che interagiscono con dati sensibili.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'architettura di connettività, vedere [Architettura della connettività del database SQL di Azure](sql-database-connectivity-architecture.md).