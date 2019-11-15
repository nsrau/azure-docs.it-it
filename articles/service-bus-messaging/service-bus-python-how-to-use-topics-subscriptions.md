---
title: 'Guida introduttiva: Usare gli argomenti e le sottoscrizioni del bus di servizio di Azure con Python'
description: Informazioni su come usare gli argomenti e le sottoscrizioni del bus di servizio da Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 94a49b31139947c6323ab391b78ecd03ee911e0a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748496"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Guida introduttiva: Usare gli argomenti e le sottoscrizioni del bus di servizio con Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questo articolo descrive come usare Python con gli argomenti e le sottoscrizioni del bus di servizio di Azure. Gli esempi usano il pacchetto [Azure Python SDK][Azure Python package] per: 

- Creare argomenti e sottoscrizioni di argomenti
- Creare filtri e regole nelle sottoscrizioni
- Inviare messaggi ad argomenti 
- Ricevere messaggi da sottoscrizioni
- Eliminare argomenti e sottoscrizioni

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Uno spazio dei nomi del bus di servizio, creato seguendo la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni](service-bus-quickstart-topics-subscriptions-portal.md). Copiare il nome dello spazio dei nomi, il nome della chiave di accesso condiviso e il valore della chiave primaria dalla schermata**Criteri di accesso condiviso**, che verranno usati più avanti in questo argomento di avvio rapido. 
- Python 3.4x o versione successiva, con il pacchetto [Azure Python SDK][Azure Python package] installato. Per altre informazioni, vedere la [Guida all'installazione di Python](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>Creare un oggetto ServiceBusService

Un oggetto **ServiceBusService** consente di usare argomenti e sottoscrizioni di argomenti. Per accedere al bus di servizio a livello di codice, aggiungere la riga seguente verso l'inizio del file Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Aggiungere il codice seguente per creare un oggetto **ServiceBusService**. Sostituire `<namespace>`, `<sharedaccesskeyname>` e `<sharedaccesskeyvalue>` con il nome dello spazio dei nomi del bus di servizio, il nome della chiave di firma di accesso condiviso e il valore della chiave primaria. Questi valori sono disponibili in **Criteri di accesso condiviso** nello spazio dei nomi del bus di servizio nel [portale di Azure][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Creare un argomento

Il codice seguente usa il metodo `create_topic` per creare un argomento del bus di servizio denominato `mytopic`, con le impostazioni predefinite:

```python
bus_service.create_topic('mytopic')
```

È possibile usare le opzioni degli argomenti per sostituire le impostazioni predefinite, ad esempio la durata (TTL) dei messaggi o le dimensioni massime degli argomenti. L'esempio seguente crea un argomento denominato `mytopic` con dimensioni massime di 5 GB e la durata TTL predefinita di un minuto per i messaggi:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Creare sottoscrizioni

È possibile usare l'oggetto **ServiceBusService** anche per creare sottoscrizioni di argomenti. Una sottoscrizione può includere un filtro per limitare il set di messaggi recapitati alla relativa coda virtuale. Se non si specifica un filtro, le nuove sottoscrizioni usano il filtro predefinito **MatchAll**, che inserisce tutti i messaggi pubblicati nell'argomento nella coda virtuale della sottoscrizione. L'esempio seguente crea una sottoscrizione di `mytopic` denominata `AllMessages` che usa il filtro **MatchAll**:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Usare i filtri con le sottoscrizioni

Usare il metodo `create_rule` dell'oggetto **ServiceBusService** per filtrare i messaggi visualizzati in una sottoscrizione. È possibile specificare regole quando si crea la sottoscrizione oppure aggiungerle a quelle esistenti.

Il tipo di filtro più flessibile è **SqlFilter**, che usa un sottoinsieme di SQL-92. I filtri SQL agiscono in base alle proprietà dei messaggi pubblicati nell'argomento. Per altre informazioni sulle espressioni che è possibile usare con un filtro SQL, vedere la sintassi di [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Poiché il filtro predefinito **MatchAll** viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario rimuoverlo dalle sottoscrizioni da filtrare, altrimenti **MatchAll** sostituirà qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita con il metodo `delete_rule` dell'oggetto **ServiceBusService**.

L'esempio seguente crea una sottoscrizione di `mytopic` denominata `HighMessages`, con una regola **SqlFilter** denominata `HighMessageFilter`. La regola `HighMessageFilter` seleziona solo i messaggi con una proprietà `messageposition` personalizzata maggiore di 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

L'esempio seguente crea una sottoscrizione di `mytopic` denominata `LowMessages`, con una regola **SqlFilter** denominata `LowMessageFilter`. La regola `LowMessageFilter` seleziona solo i messaggi con una proprietà `messageposition` minore o uguale a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Con le regole `AllMessages`, `HighMessages`e `LowMessages` tutte attivate, i messaggi inviati a `mytopic` vengono sempre recapitati ai destinatari della sottoscrizione `AllMessages`. I messaggi vengono inoltre recapitati selettivamente alla sottoscrizione `HighMessages` o `LowMessages`, a seconda del valore della proprietà `messageposition` del messaggio. 

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento

Le applicazioni usano il metodo `send_topic_message` dell'oggetto **ServiceBusService** per inviare messaggi all'argomento del bus di servizio.

L'esempio seguente invia cinque messaggi di test all'argomento `mytopic`. Il valore della proprietà `messageposition` personalizzata dipende dall'iterazione del ciclo e determina quali sottoscrizione ricevono i messaggi. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limiti e quote delle dimensioni dei messaggi

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esistono limiti al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione per le dimensioni totali dei messaggi. È possibile definire le dimensioni dell'argomento al momento della creazione, con un limite superiore di 5 GB. 

Per altre informazioni sulle quote, vedere [Quote del bus di servizio][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione

Le applicazioni usano il metodo `receive_subscription_message` nell'oggetto **ServiceBusService** per ricevere i messaggi da una sottoscrizione. L'esempio seguente riceve i messaggi dalla sottoscrizione `LowMessages` e li elimina non appena vengono letti:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Il parametro facoltativo `peek_lock` di `receive_subscription_message` determina se il bus di servizio elimina i messaggi dalla sottoscrizione non appena vengono letti. La modalità predefinita per la ricezione dei messaggi è *PeekLock* oppure `peek_lock` impostato su **True**, che legge e blocca i messaggi senza eliminarli dalla sottoscrizione. È quindi necessario che ogni messaggio venga completato esplicitamente per rimuoverlo dalla sottoscrizione.

Per eliminare i messaggi dalla sottoscrizione non appena vengono letti, è possibile impostare il parametro `peek_lock` su **False**, come nell'esempio precedente. L'eliminazione dei messaggi durante l'operazione di ricezione è il modello più semplice ed è efficace se l'applicazione è in grado di tollerare i messaggi mancanti in caso di errore. Per comprendere meglio questo comportamento, si consideri uno scenario in cui l'applicazione invia una richiesta di ricezione e quindi viene arrestata in modo anomalo prima di elaborarla. Se il messaggio viene eliminato durante la ricezione, quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, il messaggio ricevuto prima dell'arresto anomalo risulta mancante.

Se l'applicazione non è in grado di tollerare messaggi mancanti, la ricezione diventa un'operazione in due fasi. PeekLock trova il messaggio successivo da consumare, lo blocca per impedire ad altri consumer di riceverlo e lo restituisce all'applicazione. Dopo aver elaborato o archiviato il messaggio, l'applicazione completa la seconda fase del processo di ricezione chiamando il metodo `complete` nell'oggetto **Message**.  Il metodo `complete` contrassegna il messaggio come utilizzato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra questo scenario di blocco della visualizzazione:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non riesce a elaborare il messaggio per qualsiasi motivo, può chiamare il metodo `unlock` nell'oggetto **Message**. Il bus di servizio sblocca il messaggio all'interno della sottoscrizione e lo rende nuovamente disponibile per la ricezione, da parte della stessa applicazione consumer o di un'altra.

Esiste anche un timeout per i messaggi bloccati nella sottoscrizione. Se un'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout del blocco, ad esempio a causa di un arresto anomalo, il bus di servizio sblocca automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo `complete`, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo comportamento viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera l'elaborazione duplicata, è possibile usare la proprietà **MessageId** del messaggio, che rimane costante tra tutti i tentativi di recapito, per gestire il recapito duplicato dei messaggi. 

## <a name="delete-topics-and-subscriptions"></a>Eliminare argomenti e sottoscrizioni

Per eliminare gli argomenti e le sottoscrizioni, usare il [portale di Azure][Azure portal] o il metodo `delete_topic`. Il codice seguente elimina l'argomento denominato `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

L'eliminazione di un argomento comporta l'eliminazione di tutte le relative sottoscrizioni. È anche possibile eliminare le sottoscrizioni in modo indipendente. Il codice seguente elimina la sottoscrizione denominata `HighMessages` dall'argomento `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Per impostazione predefinita, gli argomenti e le sottoscrizioni sono persistenti e rimangono disponibili fino a quando non vengono eliminati. Per eliminare automaticamente le sottoscrizioni dopo un determinato periodo di tempo, è possibile impostare il parametro [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) nella sottoscrizione. 

> [!TIP]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica con facilità. Lo strumento offre funzionalità avanzate, tra cui importazione/esportazione e la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i seguenti collegamenti per altre informazioni:

* [Code, argomenti e sottoscrizioni del bus di servizio][Queues, topics, and subscriptions]
* Informazioni di riferimento su [SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
