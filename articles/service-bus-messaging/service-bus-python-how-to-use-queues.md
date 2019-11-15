---
title: 'Guida introduttiva: Usare le code del bus di servizio con Python'
description: Informazioni su come usare le code del bus di servizio con Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: d0f579fcd82860380f1aaa651a61c0259d075a0d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748539"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Guida introduttiva: Usare le code del bus di servizio con Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questo articolo illustra come usare Python per creare, inviare e ricevere messaggi dalle code del bus di servizio di Azure. 

Per altre informazioni sulle librerie del bus di servizio di Azure per Python, vedere [Librerie del bus di servizio per Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Uno spazio dei nomi del bus di servizio, creato seguendo la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni](service-bus-quickstart-topics-subscriptions-portal.md). Copiare la stringa di connessione primaria dalla schermata **Criteri di accesso condiviso**, che verrà usata più avanti in questo articolo. 
- Python 3.4x o versione successiva, con il pacchetto [Python Azure Service Bus][Python Azure Service Bus package] installato. Per altre informazioni, vedere la [Guida all'installazione di Python](/azure/python/python-sdk-azure-install). 

## <a name="create-a-queue"></a>Creare una coda

Un oggetto **ServiceBusClient** consente di usare le code. Per accedere al bus di servizio a livello di codice, aggiungere la riga seguente verso l'inizio del file Python:

```python
from azure.servicebus import ServiceBusClient
```

Aggiungere il codice seguente per creare un oggetto **ServiceBusClient**. Sostituire `<connectionstring>` con il valore della stringa di connessione primaria del bus di servizio. Questo valore è disponibile in **Criteri di accesso condiviso** nello spazio dei nomi del bus di servizio nel [portale di Azure][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Il codice seguente usa il metodo `create_queue` di **ServiceBusClient**per creare una coda denominata `taskqueue` con le impostazioni predefinite:

```python
sb_client.create_queue("taskqueue")
```

È possibile usare le opzioni per sostituire le impostazioni predefinite delle code, ad esempio la durata (TTL) dei messaggi o le dimensioni massime degli argomenti. Il codice seguente crea una coda denominata `taskqueue` con dimensioni massime di 5 GB e durata TTL di 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, un'applicazione chiama il metodo `send` nell'oggetto **ServiceBusClient**. L'esempio di codice seguente crea un client della coda e invia un messaggio di test alla coda `taskqueue`. Sostituire `<connectionstring>` con il valore della stringa di connessione primaria del bus di servizio. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limiti e quote delle dimensioni dei messaggi

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esistono limiti al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione per le dimensioni totali dei messaggi. È possibile definire le dimensioni della coda al momento della creazione, con un limite superiore di 5 GB. 

Per altre informazioni sulle quote, vedere [Quote del bus di servizio][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

Il client della coda riceve i messaggi da una coda usando il metodo `get_receiver` nell'oggetto **ServiceBusClient**. L'esempio di codice seguente crea un client della coda e riceve un messaggio dalla coda `taskqueue`. Sostituire `<connectionstring>` con il valore della stringa di connessione primaria del bus di servizio. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Usare il parametro peek_lock

Il parametro facoltativo `peek_lock` di `get_receiver` determina se il bus di servizio elimina i messaggi dalla coda non appena vengono letti. La modalità predefinita per la ricezione dei messaggi è *PeekLock* oppure `peek_lock` impostato su **True**, che legge e blocca i messaggi senza eliminarli dalla coda. È quindi necessario che ogni messaggio venga completato esplicitamente per rimuoverlo dalla coda.

Per eliminare i messaggi dalla coda non appena vengono letti, è possibile impostare il parametro `peek_lock` di `get_receiver` su **False**. L'eliminazione dei messaggi durante l'operazione di ricezione è il modello più semplice, ma è efficace solo se l'applicazione è in grado di tollerare i messaggi mancanti in caso di errore. Per comprendere meglio questo comportamento, si consideri uno scenario in cui il consumer invia una richiesta di ricezione e viene arrestato in modo anomalo prima di elaborarla. Se il messaggio viene eliminato durante la ricezione, quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, il messaggio ricevuto prima dell'arresto anomalo risulta mancante.

Se l'applicazione non è in grado di tollerare messaggi mancanti, la ricezione diventa un'operazione in due fasi. PeekLock trova il messaggio successivo da consumare, lo blocca per impedire ad altri consumer di riceverlo e lo restituisce all'applicazione. Dopo aver elaborato o archiviato il messaggio, l'applicazione completa la seconda fase del processo di ricezione chiamando il metodo `complete` nell'oggetto **Message**.  Il metodo `complete` contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non riesce a elaborare il messaggio per qualsiasi motivo, può chiamare il metodo `unlock` nell'oggetto **Message**. Il bus di servizio sblocca il messaggio all'interno della coda e lo rende nuovamente disponibile per la ricezione, da parte della stessa applicazione consumer o di un'altra.

Esiste anche un timeout per i messaggi bloccati nella coda. Se un'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout del blocco, ad esempio a causa di un arresto anomalo, il bus di servizio sblocca automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo `complete`, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo comportamento viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera l'elaborazione duplicata, è possibile usare la proprietà **MessageId** del messaggio, che rimane costante tra tutti i tentativi di recapito, per gestire il recapito duplicato dei messaggi. 

> [!TIP]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica con facilità. Lo strumento offre funzionalità avanzate, tra cui importazione/esportazione e la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere [Code, argomenti e sottoscrizioni][Queues, topics, and subscriptions] per altre informazioni.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
