---
title: Connettersi ed eseguire query su un solo server PostgreSQL
description: Collegamenti alle guide introduttive che illustrano come connettersi al server singolo del database di Azure per PostgreSQL ed eseguire query.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546552"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Panoramica di connessione ed esecuzione di query per database di Azure per PostgreSQL-server singolo

Il documento seguente include collegamenti ad esempi che illustrano come connettersi ed eseguire query con il server singolo database di Azure per PostgreSQL. Questa guida include anche le raccomandazioni e l'estensione TLS che è possibile usare per connettersi al server nelle lingue supportate di seguito.

## <a name="quickstarts"></a>Avvi rapidi

| Guida introduttiva | Descrizione |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|È possibile utilizzare pgAdmin per connettersi al server e semplificare la creazione, la manutenzione e l'utilizzo di oggetti di database.|
|[PSQL in Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Questo articolo illustra come eseguire [**PSQL**](https://www.postgresql.org/docs/current/static/app-psql.html) in [Azure cloud Shell](../cloud-shell/overview.md) per connettersi al server e quindi eseguire istruzioni per eseguire query e inserire, aggiornare ed eliminare dati nel database. È possibile eseguire **PSQL** se installato nell'ambiente di sviluppo|
|[PostgreSQL con VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|L'estensione database di Azure per VS Code (anteprima) consente di esplorare ed eseguire query sul server PostgreSQL in locale e nel cloud usando gli album con IntelliSense avanzato. |
|[PHP](connect-php.md)|Questa Guida introduttiva illustra come usare PHP per creare un programma per connettersi a un database e usare il lavoro con oggetti di database per eseguire query sui dati.|
|[Java](connect-java.md)|Questa Guida introduttiva illustra come usare Java per connettersi a un database e quindi usare il lavoro con oggetti di database per eseguire query sui dati.|
|[Node.js](connect-nodejs.md)|Questa Guida introduttiva illustra come usare Node.js per creare un programma per connettersi a un database e usare il lavoro con oggetti di database per eseguire query sui dati.|
|[.NET (C#)](connect-csharp.md)|Questa Guida introduttiva illustra come use.NET (C#) per creare un programma C# per connettersi a un database e usare il lavoro con oggetti di database per eseguire query sui dati.|
|[Go](connect-go.md)|Questa guida di avvio rapido illustra come usare Go per connettersi a un database. Vengono illustrate anche istruzioni Transact-SQL per eseguire query e modificare i dati.|
|[Python](connect-python.md)|Questa Guida introduttiva illustra come usare Python per connettersi a un database e usare il lavoro con oggetti di database per eseguire query sui dati. |
|[Ruby](connect-ruby.md)|Questa Guida introduttiva illustra come usare Ruby per creare un programma per connettersi a un database e usare il lavoro con oggetti di database per eseguire query sui dati.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerazioni su TLS per la connettività del database

Transport Layer Security (TLS) viene usato da tutti i driver forniti o supportati da Microsoft per la connessione ai database in database di Azure per PostgreSQL. Non è necessaria alcuna configurazione speciale, ma si applica TLS 1,2 per i server appena creati. È consigliabile usare TLS 1,0 e 1,1, quindi aggiornare la versione di TLS per i server. Vedere [come configurare TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>Estensioni di PostgreSQL

PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni creano un bundle di più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un singolo comando. Dopo essere stato caricate nel database, le estensioni si comportano come le funzionalità predefinite.

- [Estensioni Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [Estensioni Postgres 10](./concepts-extensions.md#postgres-10-extensions)
- [Estensioni Postgres 9,6](./concepts-extensions.md#postgres-96-extensions)
- [Estensioni Postgres 9,5](./concepts-extensions.md#postgres-95-extensions)

Per altri dettagli, vedere [come usare le estensioni di PostgreSQL in un server singolo](concepts-extensions.md).

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione dei dati tramite dump e Restore](howto-migrate-using-dump-and-restore.md)
- [Eseguire la migrazione dei dati tramite importazione ed esportazione](howto-migrate-using-export-and-import.md)
