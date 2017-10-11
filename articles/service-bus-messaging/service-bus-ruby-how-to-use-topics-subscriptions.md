---
title: Come usare gli argomenti del bus di servizio (Ruby) | Microsoft Docs
description: Informazioni su come usare le sottoscrizioni e gli argomenti del bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 4a4c9949843b16ae6be2f516de4fd1e3f7415959
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Come usare gli argomenti e le sottoscrizioni del bus di servizio con Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questo articolo descrive come usare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Ruby. Gli scenari illustrati includono **creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi** a un argomento, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per altre informazioni sugli argomenti e sulle sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Creare un argomento
L'oggetto **Azure::ServiceBusService** consente di usare argomenti. Il codice seguente crea un oggetto **Azure::ServiceBusService**. Per creare un argomento, usare il metodo `create_topic()`. L'esempio seguente crea un argomento o visualizza gli eventuali errori.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

È anche possibile passare un oggetto **Azure::ServiceBus::Topic** con opzioni aggiuntive, che consente di eseguire l'override delle impostazioni degli argomenti predefinite, ad esempio la durata dei messaggi o la dimensione massima della coda. L'esempio seguente illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Creare sottoscrizioni
Le sottoscrizioni di un argomento vengono create anche con l'oggetto **Azure::ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita il set di messaggi recapitati alla coda virtuale della sottoscrizione.

Le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono associate, non vengono eliminate. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è innanzitutto necessario verificare se la sottoscrizione esiste già usando il metodo getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare una sottoscrizione con il filtro (MatchAll) predefinito
Il filtro **MatchAll** è il filtro predefinito e viene usato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "all-messages" e viene usato il filtro predefinito **MatchAll**.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Creare sottoscrizioni con i filtri
È anche possibile definire i filtri che consentono di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **Azure::ServiceBus::SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter](service-bus-messaging-sql-filter.md).

È possibile aggiungere filtri a una sottoscrizione usando il metodo `create_rule()` dell'oggetto **Azure::ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima rimuovere il filtro predefinito, altrimenti **MatchAll** eseguirà l'override di qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo `delete_rule()` nell'oggetto **Azure::ServiceBusService**.

Nell'esempio seguente viene creata una sottoscrizione denominata "high-messages" con **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata `message_number` è maggiore di 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Analogamente, l'esempio seguente crea una sottoscrizione denominata `low-messages` con un filtro **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà `message_number` è minore o uguale a 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Un messaggio inviato a `test-topic` viene sempre recapitato ai ricevitori con sottoscrizione all'argomento `all-messages` e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti `high-messages` e `low-messages` (a seconda del contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo `send_topic_message()` nell'oggetto **Azure::ServiceBusService**. I messaggi inviati ad argomenti del bus di servizio sono istanze degli oggetti **Azure::ServiceBus::BrokeredMessage**. Gli oggetti **Azure::ServiceBus::BrokeredMessage** includono un insieme di proprietà standard, ad esempio `label` e `time_to_live`, un dizionario usato per mantenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa al metodo `send_topic_message()` in modo da popolare le proprietà standard necessarie con valori predefiniti.

Il seguente esempio illustra come inviare cinque messaggi di test a `test-topic`. Si noti come il valore della proprietà personalizzata `message_number` di ogni messaggio varia nell'iterazione del ciclo, determinando la sottoscrizione che lo riceverà:

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
I messaggi vengono ricevuti da una sottoscrizione tramite il metodo `receive_subscription_message()` per l'oggetto **Azure::ServiceBusService**. Per impostazione predefinita, i messaggi vengono letti (picco) e bloccati senza essere eliminati dalla sottoscrizione. È possibile leggere ed eliminare il messaggio dalla sottoscrizione, impostando l'opzione `peek_lock` su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire anche il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo `delete_subscription_message()` e specificando il messaggio da eliminare come parametro. Il metodo `delete_subscription_message()` contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

Se il parametro `:peek_lock` è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto a scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

L'esempio seguente illustra come ricevere ed elaborare messaggi tramite `receive_subscription_message()`. Nell'esempio viene prima ricevuto ed eliminato un messaggio dalla sottoscrizione `low-messages` tramite `:peek_lock` impostato su **false** e successivamente viene ricevuto da `high-messages` e poi eliminato un altro messaggio tramite `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione del ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo `unlock_subscription_message()` sull'oggetto **Azure::ServiceBusService**. In questo modo, il bus di servizio sblocca il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è anche associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo `delete_subscription_message()`, il messaggio viene nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito *At-Least-Once* per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà `message_id` del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare argomenti e sottoscrizioni
Gli argomenti e le sottoscrizioni sono persistenti e devono essere eliminati in modo esplicito nel [portale di Azure][Azure portal] oppure a livello di codice. Il seguente esempio illustra come eliminare l'argomento denominato `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice seguente dimostra come eliminare la sottoscrizione denominata `high-messages` dall'argomento `test-topic`:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i seguenti collegamenti per altre informazioni.

* Vedere [Code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md).
* Materiale di riferimento dell'API per [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Vedere il repository [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) su GitHub.

[Azure portal]: https://portal.azure.com
