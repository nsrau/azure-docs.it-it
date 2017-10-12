---
title: "Disponibilità e coerenza nell'Hub eventi di Azure | Microsoft Docs"
description: "Come fornire la quantità massima di disponibilità e coerenza con l'Hub eventi di Azure usando le partizioni."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 681a9d1636d547492f6f827461c6b2494b918778
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilità e coerenza nell'Hub eventi

## <a name="overview"></a>Panoramica
Hub eventi di Azure usa un [modello di partizionamento](event-hubs-features.md#partitions) per migliorare la disponibilità e la parallelizzazione all'interno di un singolo hub eventi. Se ad esempio un hub eventi include quattro partizioni e una di queste partizioni viene spostata da un server a un altro in un'operazione di bilanciamento del carico, è comunque possibile inviare e ricevere dalle altre tre partizioni. Più partizioni consentono anche di avere più lettori simultaneamente che elaborano i dati, migliorando la velocità effettiva di aggregazione. Comprendere le implicazioni del partizionamento e dell'ordinamento in un sistema distribuito è un aspetto critico della progettazione di una soluzione.

Per spiegare il compromesso tra ordinamento e disponibilità, vedere il [teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), noto anche come teorema di Brewer. Il teorema discute la scelta tra coerenza, disponibilità e tolleranza di partizione.

Il teorema di Brewer definisce coerenza e disponibilità come segue:
* Tolleranza di partizione: la capacità di un sistema di elaborazione dei dati di continuare l'elaborazione dei dati anche se si verifica un errore della partizione.
* Disponibilità: un nodo non di errore restituisce una risposta accettabile in un periodo ragionevole di tempo, senza errori o timeout.
* Coerenza: una lettura garantisce la restituzione della scrittura più recente per un determinato client.

## <a name="partition-tolerance"></a>Tolleranza di partizione
L'Hub eventi si basa su un modello di dati partizionato. È possibile configurare il numero di partizioni nell'hub eventi durante l'installazione, ma non è possibile modificare questo valore in un secondo momento. Poiché è obbligatorio usare le partizioni con l'Hub eventi, è necessario prendere una decisione relativa a disponibilità e coerenza dell'applicazione.

## <a name="availability"></a>Disponibilità
Il modo più semplice per iniziare a usare l'Hub eventi è il comportamento predefinito. Se si crea un nuovo oggetto `EventHubClient` e si usa il metodo `Send`, gli eventi vengono distribuiti automaticamente tra le partizioni nell'hub eventi. Questo comportamento consente la maggiore quantità di tempo di attività.

Per i casi di uso che richiedono il massimo del tempo di attività, è preferibile usare questo modello.

## <a name="consistency"></a>Coerenza
In alcuni scenari, l'ordinamento degli eventi può essere importante. È ad esempio, potrebbe essere necessario che il sistema back-end elabori un comando di aggiornamento prima di un comando di eliminazione. In questo caso, è possibile impostare la chiave di partizione su un evento oppure usare un oggetto `PartitionSender` solo per inviare eventi a una determinata partizione. In tal modo, quando questi eventi vengono letti dalla partizione, vengono letti nell'ordine.

Con questa configurazione, tenere presente che se la partizione specifica alla quale si esegue l'invio non è disponibile, si riceverà una risposta di errore. Per fare un confronto, se non è presente un'affinità a una singola partizione, il servizio dell'Hub eventi invia l'evento alla partizione successiva disponibile.

Una possibile soluzione per garantire l'ordinamento ottimizzando allo stesso tempo i tempi di attività sarebbe l'aggregazione di eventi come parte dell'applicazione di elaborazione di eventi. Il modo più semplice per eseguire questa operazione è contrassegnare l'evento con una proprietà con numero di sequenza personalizzato. Il codice seguente mostra un esempio:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

In questo esempio l'evento viene inviato a una delle partizioni disponibili nell'hub eventi e il numero di sequenza corrispondente viene impostato dall'applicazione. Questa soluzione richiede che l'applicazione di elaborazione mantenga lo stato, ma propone ai mittenti un endpoint che ha maggiori probabilità di essere disponibile.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica del servizio Hub eventi](event-hubs-what-is-event-hubs.md)
* [Creare un hub eventi](event-hubs-create.md)
