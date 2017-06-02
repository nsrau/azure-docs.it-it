---
title: Come usare le code del bus di servizio di Azure con Ruby | Microsoft Docs
description: Informazioni su come usare le code del bus di servizio in Azure. Gli esempi di codice sono scritti in Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: b649bab171770b1edb3ed4b4e345375d948e6a97
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-use-service-bus-queues"></a>Come usare le code del bus di servizio
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questa guida illustra come usare le code del bus di servizio. Gli esempi sono scritti in Ruby e utilizzano la gemma di Azure. Gli scenari illustrati includono la **creazione di code, l'invio e la ricezione di messaggi** e **l'eliminazione di code**. Per altre informazioni sulle code del bus di servizio, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Come creare una coda
L'oggetto **Azure::ServiceBusService** consente di usare le code. Per creare una coda, usare il metodo **create_queue()**. Nel seguente esempio viene creata una coda o stampato l'eventuale errore.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

È anche possibile passare un oggetto **Azure::ServiceBus::Queue** con opzioni aggiuntive che consentono di sostituire le impostazioni predefinite delle code, ad esempio la durata dei messaggi o la dimensione massima della coda. Il seguente esempio illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Come inviare messaggi a una coda
Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiama il metodo **send\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. I messaggi inviati e ricevuti dalle code del bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage** e includono un set di proprietà standard, ad esempio **label** e **time\_to\_live**, un dizionario usato per mantenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un valore stringa come messaggio, in modo da popolare le proprietà standard necessarie con valori predefiniti.

Nell'esempio seguente viene illustrato come inviare un messaggio di prova alla coda denominata "test-queue" tramite **send\_queue\_message()**:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Come ricevere messaggi da una coda
I messaggi vengono ricevuti da una coda tramite il metodo **receive\_queue\_message()** per l'oggetto **Azure::ServiceBusService**. Per impostazione predefinita, i messaggi vengono letti e bloccati senza essere eliminati dalla coda. È tuttavia possibile eliminare i messaggi dalla coda dopo essere stati letti impostando l'opzione **:peek_lock** su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire anche il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo **delete\_queue\_message()** e specificando il messaggio da eliminare come parametro. Il metodo **delete\_queue\_message()** contrassegna il messaggio come letto e lo rimuove dalla coda.

Se il parametro **:peek\_lock** è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto a scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi tramite **receive\_queue\_message()**. Nell'esempio viene prima ricevuto ed eliminato un messaggio tramite **:peek\_lock** impostato su **false** e successivamente viene ricevuto ed eliminato un altro messaggio tramite **delete\_queue\_message()**:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione del ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. In questo modo, il bus di servizio sblocca il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o di un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete\_queue\_message()**, il messaggio verrà recapitato di nuovo al riavvio dell'applicazione. Questo processo di elaborazione viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **message\_id** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i collegamenti seguenti per altre informazioni.

* Panoramica di [code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md).
* Vedere il repository [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) su GitHub.

Per un confronto tra le code del bus di servizio di Azure discusse in questo articolo e le code di Azure discusse nell'articolo [Come usare l'archiviazione di accodamento da Ruby](../storage/storage-ruby-how-to-use-queue-storage.md), vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](service-bus-azure-and-service-bus-queues-compared-contrasted.md).


