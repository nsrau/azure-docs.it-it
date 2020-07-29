---
title: Risoluzione dei problemi Azure Cosmos DB eccezione timeout richiesta servizio
description: Come diagnosticare e correggere l'eccezione di timeout della richiesta di servizio Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294274"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB timeout della richiesta
Azure Cosmos DB ha restituito un timeout della richiesta HTTP 408

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause e le soluzioni note per le eccezioni di timeout della richiesta.

### <a name="1-check-the-sla"></a>1. controllare il contratto di contratto
Il cliente deve controllare il [monitoraggio Azure Cosmos DB](monitor-cosmos-db.md) per verificare se il numero 408 di eccezioni viola il contratto di servizio Cosmos DB.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Soluzione 1: non ha violato il contratto di Cosmos DB
L'applicazione deve gestire questo scenario e riprovare a eseguire questi errori temporanei.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Soluzione 2: l'it ha violato il contratto di Cosmos DB
Contattare il supporto tecnico di Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. chiave di partizione a caldo
Azure Cosmos DB distribuisce in modo uniforme la velocità effettiva di provisioning complessiva tra le partizioni fisiche. Quando è presente una partizione a caldo, una o più chiavi di partizione logiche in una partizione fisica utilizzano tutte le UR/s della partizione fisica, mentre le UR/sec in altre partizioni fisiche non vengono utilizzate. Come sintomo, il numero totale di Ur/s utilizzato sarà inferiore alle UR/sec di cui è stato effettuato il provisioning nel database o nel contenitore, ma verrà comunque visualizzata la limitazione (429s) sulle richieste rispetto alla chiave di partizione logica attiva. Usare la [metrica consumo unità richiesta normalizzata](monitor-normalized-request-units.md) per verificare se il carico di lavoro sta riscontrando una partizione a caldo. 

#### <a name="solution"></a>Soluzione:
Scegliere una chiave di partizione appropriata che distribuisca equamente il volume di richiesta e l'archiviazione. Informazioni su come [modificare la chiave di partizione](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)