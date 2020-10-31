---
title: Risolvere i problemi relativi alle eccezioni di timeout delle richieste di Azure Cosmos DB
description: Informazioni su come diagnosticare e correggere Azure Cosmos DB eccezioni di timeout della richiesta di servizio.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a18c4cf2f2bc1882a82afe0ba201f0d8b070924d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081366"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB le eccezioni di timeout richieste
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ha restituito un timeout della richiesta HTTP 408.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause e le soluzioni note per le eccezioni di timeout della richiesta.

### <a name="check-the-sla"></a>Verificare il contratto di contratto
Controllare [Azure Cosmos DB monitoraggio](monitor-cosmos-db.md) per verificare se il numero di 408 eccezioni viola il contratto di Azure Cosmos DB.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Soluzione 1: non ha violato il contratto di Azure Cosmos DB
L'applicazione deve gestire questo scenario e riprovare a eseguire questi errori temporanei.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Soluzione 2: l'it ha violato il contratto di Azure Cosmos DB
Contattare il [supporto tecnico di Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Chiave di partizione a caldo
Azure Cosmos DB distribuisce in modo uniforme la velocità effettiva di provisioning complessiva tra le partizioni fisiche. Quando è presente una partizione a caldo, una o più chiavi di partizione logiche in una partizione fisica utilizzano tutte le unità richiesta della partizione fisica al secondo (UR/sec). Allo stesso tempo, le UR/sec in altre partizioni fisiche sono inutilizzate. Come sintomo, il numero totale di Ur/s utilizzato sarà inferiore alle UR/sec di cui è stato effettuato il provisioning nel database o nel contenitore. Verranno comunque visualizzate le limitazioni (429s) sulle richieste rispetto alla chiave di partizione logica attiva. Usare la [metrica consumo unità richiesta normalizzata](monitor-normalized-request-units.md) per verificare se il carico di lavoro sta riscontrando una partizione a caldo. 

#### <a name="solution"></a>Soluzione:
Scegliere una chiave di partizione appropriata che distribuisca equamente il volume di richiesta e l'archiviazione. Informazioni su come [modificare la chiave di partizione](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).