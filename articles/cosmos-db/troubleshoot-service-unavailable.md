---
title: Risolvere i problemi di Azure Cosmos DB eccezione servizio non disponibile
description: Come diagnosticare e correggere Cosmos DB eccezione non disponibile del servizio
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294265"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB servizio non disponibile
SDK non è riuscito a connettersi al servizio Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause note e le soluzioni per le eccezioni del servizio non disponibili.

### <a name="1-the-required-ports-are-not-enabled"></a>1. le porte obbligatorie non sono abilitate.
Verificare che tutte le [porte necessarie](performance-tips-dotnet-sdk-v3-sql.md#networking) siano abilitate.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Se un'applicazione o un servizio esistente ha iniziato a ottenere 503

### <a name="1-there-is-an-outage"></a>1. si verifica un'interruzione
Controllare lo [stato di Azure](https://status.azure.com/status) per verificare se è in corso un problema.

### <a name="2-snat-port-exhaustion"></a>2. esaurimento delle porte SNAT
Seguire la [Guida all'esaurimento delle porte di SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. le porte necessarie sono bloccate
Verificare che tutte le [porte necessarie](performance-tips-dotnet-sdk-v3-sql.md#networking) siano abilitate.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)