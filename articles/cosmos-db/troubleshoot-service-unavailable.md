---
title: Risolvere i problemi relativi alle eccezioni Azure Cosmos DB servizio non disponibile
description: Informazioni su come diagnosticare e correggere Azure Cosmos DB eccezioni non disponibili del servizio.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870868"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnosticare e risolvere i problemi relativi alle eccezioni Azure Cosmos DB servizio non disponibili
SDK non è riuscito a connettersi a Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause note e le soluzioni per le eccezioni del servizio non disponibili.

### <a name="the-required-ports-are-being-blocked"></a>Le porte necessarie sono bloccate
Verificare che tutte le [porte necessarie](performance-tips-dotnet-sdk-v3-sql.md#networking) siano abilitate.

### <a name="client-side-transient-connectivity-issues"></a>Problemi di connettività temporanei lato client
Quando si verificano problemi di connettività temporanei che causano timeout, le eccezioni non disponibili del servizio possono essere rilevate. In genere, la traccia dello stack correlata a questo scenario conterrà un `TransportException` errore. Ad esempio:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Per risolverlo, seguire la procedura per la [risoluzione dei problemi di timeout della richiesta](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="service-outage"></a>Interruzione del servizio
Controllare lo [stato di Azure](https://status.azure.com/status) per verificare se è in corso un problema.


## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).