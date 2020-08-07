---
title: Risolvere i problemi di Azure Cosmos DB eccezione servizio non disponibile
description: Come diagnosticare e correggere Cosmos DB eccezione non disponibile del servizio
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987376"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB servizio non disponibile
SDK non è riuscito a connettersi al servizio Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause note e le soluzioni per le eccezioni del servizio non disponibili.

### <a name="1-the-required-ports-are-being-blocked"></a>1. le porte richieste sono bloccate
Verificare che tutte le [porte necessarie](performance-tips-dotnet-sdk-v3-sql.md#networking) siano abilitate.

### <a name="2-client-side-transient-connectivity-issues"></a>2. problemi di connettività temporanei lato client
La superficie di eccezioni del servizio non disponibile può essere visibile in presenza di problemi di connettività temporanei che causano timeout. In genere, la traccia dello stack correlata a questo scenario conterrà un `TransportException` , ad esempio:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Per risolverlo, seguire la [risoluzione dei problemi di timeout della richiesta](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="3-service-outage"></a>3. interruzione del servizio
Controllare lo [stato di Azure](https://status.azure.com/status) per verificare se è in corso un problema.


## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)