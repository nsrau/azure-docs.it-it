---
title: Risolvere i problemi di Azure Cosmos DB frequenza delle richieste a un'eccezione grande
description: Come diagnosticare e correggere la frequenza delle richieste a un'eccezione di grandi dimensioni
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294269"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnosticare e risolvere i problemi Cosmos DB troppe richieste
' Frequenza richieste troppo grande ' o codice di errore 429 indica che le richieste sono state limitate.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause e le soluzioni note per un numero eccessivo di richieste.

### <a name="1-check-the-metrics"></a>1. controllare le metriche
Il cliente deve controllare il [monitoraggio Azure Cosmos DB](monitor-cosmos-db.md) per verificare se il numero 429 eccezioni.

## <a name="cause"></a>Causa:
La velocità effettiva utilizzata (UR/sec) ha superato la [velocità effettiva con provisioning](set-throughput.md). L'SDK tenterà automaticamente di ritentare le richieste in base ai criteri di ripetizione specificati. Se questo errore si verifica spesso, è consigliabile aumentare la velocità effettiva della raccolta. Controllare le metriche del portale per vedere se si ricevono errori 429. Esaminare la chiave di partizione per assicurarsi che venga generata una [distribuzione uniforme del volume di archiviazione e di richiesta](partition-data.md).

## <a name="solution"></a>Soluzione:
1. Usare il [portale o l'SDK](set-throughput.md) per aumentare la velocità effettiva con provisioning.
2. Consente di passare al database o al contenitore per la [scalabilità](provision-throughput-autoscale.md)automatica.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)