---
title: Connettersi ed eseguire query su un singolo server MySQL
description: Collegamenti alle guide introduttive che illustrano come connettersi al server singolo del database SQL di Azure ed eseguire query.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546433"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Panoramica di connessione ed esecuzione di query per database di Azure per MySQL-server singolo

Il documento seguente include collegamenti ad esempi che illustrano come connettersi ed eseguire query con il server singolo database di Azure per MySQL. Questa guida include anche le raccomandazioni e le librerie TLS che è possibile usare per connettersi al server nelle lingue supportate di seguito.

## <a name="quickstarts"></a>Avvi rapidi

| Guida introduttiva | Descrizione |
|---|---|
|[Workbench MySQL](connect-workbench.md)|Questa Guida introduttiva illustra come usare il client MySQL Workbench per connettersi a un database. È quindi possibile usare le istruzioni MySQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|Questo articolo illustra come eseguire **mysql.exe** in [Azure cloud Shell](../cloud-shell/overview.md) per connettersi al server e quindi eseguire istruzioni per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[MySQL con Visual Studio](https://www.mysql.com/why-mysql/windows/visualstudio)|È possibile usare MySQL per Visual Studio per la connessione al server MySQL. MySQL per Visual Studio si integra direttamente in Esplora server facilitando la configurazione di nuove connessioni e l'utilizzo di oggetti di database.|
|[PHP](connect-php.md)|Questa Guida introduttiva illustra come usare PHP per creare un programma per connettersi a un database e usare istruzioni MySQL per eseguire query sui dati.|
|[Java](connect-java.md)|Questa Guida introduttiva illustra come usare Java per connettersi a un database e quindi usare le istruzioni MySQL per eseguire query sui dati.|
|[Node.js](connect-nodejs.md)|Questa Guida introduttiva illustra come usare Node.js per creare un programma per connettersi a un database e usare istruzioni MySQL per eseguire query sui dati.|
|[.NET (C#)](connect-csharp.md)|Questa Guida introduttiva illustra come use.NET (C#) per creare un programma C# per connettersi a un database e usare istruzioni MySQL per eseguire query sui dati.|
|[Go](connect-go.md)|Questa guida di avvio rapido illustra come usare Go per connettersi a un database. Vengono illustrate anche istruzioni Transact-SQL per eseguire query e modificare i dati.|
|[Python](connect-python.md)|Questa Guida introduttiva illustra come usare Python per connettersi a un database e usare le istruzioni MySQL per eseguire query sui dati. |
|[Ruby](connect-ruby.md)|Questa Guida introduttiva illustra come usare Ruby per creare un programma per connettersi a un database e usare istruzioni MySQL per eseguire query sui dati.|
|[C++](connect-cpp.md)|Questa Guida introduttiva illustra come usare C++ + per creare un programma per connettersi a un database e usare i dati di query.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerazioni su TLS per la connettività del database

Transport Layer Security (TLS) viene usato da tutti i driver forniti o supportati da Microsoft per la connessione ai database in database di Azure per MySQL. Non è necessaria alcuna configurazione speciale, ma si applica TLS 1,2 per i server appena creati. È consigliabile usare TLS 1,0 e 1,1, quindi aggiornare la versione di TLS per i server. Vedere [come configurare TLS](howto-tls-configurations.md)

## <a name="libraries"></a>Librerie

Database di Azure per MySQL usa la versione di community del database MySQL più diffusa al mondo. Pertanto, è compatibile con un'ampia gamma di linguaggi di programmazione e driver. L'obiettivo è supportare le tre versioni più recenti dei driver MySQL e continuare l'attività  con gli autori della community open source per migliorare costantemente le funzionalità  e l'usabilità  dei driver MySQL.

Vedere quali [driver](concepts-compatibility.md) sono compatibili con il server singolo database di Azure per MySQL.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione dei dati tramite dump e Restore](concepts-migrate-dump-restore.md)
- [Eseguire la migrazione dei dati tramite importazione ed esportazione](concepts-migrate-import-export.md)