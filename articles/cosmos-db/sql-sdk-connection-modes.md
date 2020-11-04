---
title: Modalità di connettività di Azure Cosmos DB SQL SDK
description: Informazioni sulle diverse modalità di connettività disponibili in Azure Cosmos DB SQL SDK.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: 3eb10452dda0227bcada3a576d28fb55089397cb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305127"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Modalità di connettività di Azure Cosmos DB SQL SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Il modo in cui un client si connette a Azure Cosmos DB ha implicazioni importanti sulle prestazioni, specialmente per la latenza lato client osservata. Azure Cosmos DB offre un modello di programmazione RESTful semplice e aperto su HTTPS chiamato modalità gateway. Inoltre, offre un protocollo TCP efficiente, che è anche un modello di comunicazione RESTful e utilizza TLS per l'autenticazione iniziale e la crittografia del traffico, denominato modalità diretta.

## <a name="available-connectivity-modes"></a>Modalità di connettività disponibili

Le due modalità di connettività disponibili sono:

  * Modalità gateway
      
    La modalità Gateway è supportata in tutte le piattaforme SDK. Se l'applicazione viene eseguita all'interno di una rete aziendale con restrizioni rigide del firewall, la modalità Gateway è la scelta migliore perché usa la porta HTTPS standard e un singolo endpoint DNS. Il compromesso in termini di prestazioni, tuttavia, è che la modalità Gateway prevede un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in Azure Cosmos DB. Si consiglia anche la modalità di connessione del gateway quando si eseguono applicazioni in ambienti con un numero limitato di connessioni socket.

    Quando si usa l'SDK in funzioni di Azure, in particolare nel [piano a consumo](../azure-functions/functions-scale.md#consumption-plan), tenere presenti i [limiti correnti per le connessioni](../azure-functions/manage-connections.md).

  * Modalità diretta

    La modalità diretta supporta la connettività tramite il protocollo TCP e offre prestazioni migliori in quanto sono presenti meno hop di rete. L'applicazione si connette direttamente alle repliche back-end. La modalità diretta è attualmente supportata solo nelle piattaforme .NET e Java SDK.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Modalità di connettività Azure Cosmos DB" border="false":::

Queste modalità di connettività essenzialmente condizionano la route richiesta dal piano dati, ovvero letture e scritture di documenti, dal computer client alle partizioni nel Azure Cosmos DB back-end. La modalità diretta è l'opzione preferita per ottenere prestazioni ottimali: consente al client di aprire le connessioni TCP direttamente alle partizioni nel Azure Cosmos DB back-end e di inviare richieste *dirette* a ly senza intermediario. Al contrario, in modalità Gateway, le richieste effettuate dal client vengono instradate a un cosiddetto server "gateway" nel Azure Cosmos DB front-end, che a sua volta fan le richieste alle partizioni appropriate nel Azure Cosmos DB back-end.

## <a name="service-port-ranges"></a>Intervalli di porte del servizio

Quando si usa la modalità diretta, oltre alle porte del gateway, è necessario assicurarsi che l'intervallo di porte compreso tra 10000 e 20000 sia aperto perché Azure Cosmos DB usa porte TCP dinamiche. Quando si usa la modalità diretta negli [endpoint privati](./how-to-configure-private-endpoints.md), è necessario aprire l'intera gamma di porte TCP, da 0 a 65535. Se queste porte non sono aperte e si tenta di usare il protocollo TCP, è possibile che venga visualizzato un errore di 503 servizio non disponibile.

La tabella seguente mostra un riepilogo delle modalità di connettività disponibili per le varie API e le porte del servizio usate per ogni API:

|Modalità di connessione  |Protocollo supportato  |SDK supportati  |API/porta servizio  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Tutti gli SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tabella (443), Cassandra (10350), Graph (443) <br> La porta 10250 esegue il mapping a un'API Azure Cosmos DB predefinita per l'istanza di MongoDB senza replica geografica. Mentre le porte 10255 e 10256 sono mappate all'istanza con replica geografica.   |
|Connessione diretta    |     TCP    |  .NET SDK    | Quando si usano gli endpoint pubblici/di servizio: porte nell'intervallo da 10000 a 20000<br>Quando si usano endpoint privati: porte nell'intervallo da 0 a 65535 |

## <a name="next-steps"></a>Passaggi successivi

Per specifiche ottimizzazioni delle prestazioni della piattaforma SDK:

* [Suggerimenti sulle prestazioni di .NET v2 SDK](performance-tips.md)

* [Suggerimenti sulle prestazioni di .NET V3 SDK](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Suggerimenti sulle prestazioni di Java v4 SDK](performance-tips-java-sdk-v4-sql.md)