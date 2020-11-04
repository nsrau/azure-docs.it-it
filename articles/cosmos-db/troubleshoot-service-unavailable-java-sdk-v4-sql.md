---
title: Risolvere i problemi relativi alle eccezioni Azure Cosmos DB servizio non disponibili con Java v4 SDK
description: Informazioni su come diagnosticare e correggere Azure Cosmos DB eccezioni non disponibili per il servizio con Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340078"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB eccezioni del servizio Java v4 SDK non disponibili
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Java v4 SDK non è riuscito a connettersi a Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause note e le soluzioni per le eccezioni del servizio non disponibili.

### <a name="the-required-ports-are-being-blocked"></a>Le porte necessarie sono bloccate
Verificare che tutte le [porte necessarie](sql-sdk-connection-modes.md#service-port-ranges) siano abilitate.

### <a name="client-side-transient-connectivity-issues"></a>Problemi di connettività temporanei lato client
Quando si verificano problemi di connettività temporanei che causano timeout, le eccezioni non disponibili del servizio possono essere rilevate. In genere, la traccia dello stack correlata a questo scenario conterrà un `ServiceUnavailableException` errore con i dettagli di diagnostica. Ad esempio:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Per risolverlo, seguire la procedura per la [risoluzione dei problemi di timeout della richiesta](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) .

### <a name="service-outage"></a>Interruzione del servizio
Controllare lo [stato di Azure](https://status.azure.com/status) per verificare se è in corso un problema.


## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-java-sdk-v4-sql.md) i problemi quando si usa il Azure Cosmos DB Java v4 SDK.
* Informazioni sulle linee guida sulle prestazioni per [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).