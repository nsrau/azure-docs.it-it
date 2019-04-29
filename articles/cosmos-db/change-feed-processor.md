---
title: Uso della libreria del processore dei feed di modifiche in Azure Cosmos DB
description: Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: cf03233c6a92b7fd1b782f8128787bfda5582f7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893320"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processore dei feed di modifiche in Azure Cosmos DB 

La [libreria del processore dei feed di modifiche di Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md) aiuta a distribuire l'elaborazione degli eventi in più consumer. Questa libreria semplifica la lettura delle modifiche nelle partizioni e in più thread che operano in parallelo.

Il vantaggio principale della libreria del processore dei feed di modifiche è legato al fatto che non è necessario gestire ogni partizione e token di continuazione né eseguire manualmente il polling di ogni contenitore.

La libreria del processore dei feed di modifiche semplifica la lettura delle modifiche nelle partizioni e in più thread che operano in parallelo. Gestisce automaticamente la lettura delle modifiche nelle partizioni usando un meccanismo di lease. Come si può osservare nell'immagine seguente, se si avviano due client che usano la libreria del processore dei feed di modifiche, il lavoro viene diviso tra di essi. Se si continua ad aumentare il numero di client, il lavoro viene ulteriormente diviso tra di essi.

![Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](./media/change-feed-processor/change-feed-output.png)

Il client a sinistra è stato avviato per primo e ha iniziato a monitorare tutte le partizioni, quindi è stato avviato il secondo client e infine il primo ha assegnato alcuni lease al secondo client. Si tratta di un modo efficace per distribuire il lavoro tra computer e client diversi.

Se si hanno due funzioni di Azure serverless che monitorano lo stesso contenitore e usano lo stesso lease, le due funzioni possono ottenere documenti diversi a seconda di come la libreria del processore decide di elaborare le partizioni.

## <a name="implementing-the-change-feed-processor-library"></a>Implementazione della libreria del processore dei feed di modifiche

Ci sono quattro componenti principali per l'implementazione della libreria del processore dei feed di modifiche: 

1. **Contenitore monitorato:** il contenitore monitorato include i dati da cui viene generato il feed di modifiche. Eventuali inserimenti e modifiche nel contenitore monitorato vengono riflessi nel feed di modifiche del contenitore.

1. **Contenitore di lease:** il contenitore di lease coordina l'elaborazione dei feed di modifiche tra più ruoli di lavoro. Viene usato un contenitore separato per archiviare i lease con un lease per partizione. È consigliabile archiviare questo contenitore di lease in un account diverso, con un'area di scrittura più vicina alla posizione in cui è in esecuzione il processore dei feed di modifiche. Un oggetto lease contiene gli attributi seguenti:

   * Proprietario: specifica l'host proprietario del lease.

   * Continuazione: specifica la posizione (token di continuazione) nel feed di modifiche per una partizione specifica.

   * Timestamp: data dell'ultimo aggiornamento del lease. Il timestamp può essere usato per verificare se il lease viene considerato scaduto.

1. **Host del processore:** ogni host determina il numero di partizioni da elaborare in base al numero di istanze di host con lease attivi.

   * Quando un host viene avviato, acquisisce lease per bilanciare il carico di lavoro in tutti gli host. Un host rinnova periodicamente i lease, in modo che i lease rimangano attivi.

   * Un host imposta come checkpoint il token di continuazione più recente sul rispettivo lease per ogni lettura. Per garantire la sicurezza della concorrenza, un host verifica il valore ETag per ogni aggiornamento di lease. Sono supportate anche altre strategie per i checkpoint.

   * All'arresto, un host rilascia tutti i lease ma mantiene le informazioni sulla continuazione, in modo che sia possibile riprendere la lettura dal checkpoint archiviato in un secondo momento.

   Attualmente, il numero di host non può essere superiore al numero di partizioni (lease).

1. **Consumer:** i consumer, o processi di lavoro, sono thread che eseguono l'elaborazione del feed di modifiche avviata da ogni host. Ogni host di processori può includere più consumer. Ogni consumer legge il feed di modifiche dalla partizione a cui è assegnato e invia notifiche al rispettivo host in caso di modifiche e di lease scaduti.

Per una migliore comprensione dell'interazione tra i quattro elementi del processore dei feed di modifiche, è consigliabile esaminare un esempio nel diagramma seguente. La raccolta monitorata archivia i documenti e usa "City" come chiave di partizione. Come si può notare, la partizione blu contiene i documenti con il campo "City" compreso nell'intervallo "A-E" e così via. Sono disponibili due host, ognuno con due consumer che leggono dalle quattro partizioni in parallelo. Le frecce mostrano i consumer che leggono da un punto specifico nel feed di modifiche. Nella prima partizione il colore blu scuro rappresenta le modifiche non lette, mentre il colore blu chiaro rappresenta le modifiche già lette nel feed di modifiche. Gli host usano la raccolta di lease per archiviare un valore di tipo "continuation" per tenere traccia della posizione di lettura corrente per ogni consumer.

![Esempio di processore dei feed di modifiche](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Feed di modifiche e velocità effettiva di cui viene effettuato il provisioning

Vengono addebitati i costi per le UR utilizzate, in quanto lo spostamento dei dati da e verso i contenitori Cosmos comporta sempre l'utilizzo di UR. Vengono addebitati i costi per le UR utilizzate dal contenitore di lease.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Libreria del processore dei feed di modifiche di Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md)
* [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Altri esempi in GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Metodi per leggere il feed di modifiche](read-change-feed.md)
* [Uso del feed di modifiche con Funzioni di Azure](change-feed-functions.md)
