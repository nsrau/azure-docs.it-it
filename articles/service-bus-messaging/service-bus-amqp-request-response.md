---
title: AMQP 1,0-operazioni di richiesta/risposta nel bus di servizio di Azure
description: Elenco delle operazioni basate su richiesta/risposta del bus di servizio di Microsoft Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: b549aee197f35df29a982a1a86644c46b9061c63
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72785282"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 nel bus di servizio di Microsoft Azure: operazioni basate su richiesta/risposta

Questo articolo offre un elenco delle operazioni basate su richiesta/risposta del bus di servizio di Microsoft Azure. Le informazioni sono basate sulla bozza di lavoro di AMQP Management versione 1.0.  
  
Per una guida dettagliata al protocollo a livello di rete AMQP 1.0, che illustra come il bus di servizio è basato sulla specifica tecnica OASIS AMQP e la implementa, vedere [Guida al protocollo AMQP 1.0 nel bus di servizio e in Hub eventi di Azure][Guida al protocollo AMQP 1.0].  
  
## <a name="concepts"></a>Concetti  
  
### <a name="entity-description"></a>Descrizione di entità  

Una descrizione di entità fa riferimento a un oggetto [classe QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) o [classe SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) del bus di servizio.  
  
### <a name="brokered-message"></a>Messaggio negoziato  

Rappresenta un messaggio nel bus di servizio mappato a un messaggio AMQP. Per la definizione del mapping, consultare la [Guida al protocollo AMQP nel bus di servizio](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Eseguire il collegamento a un nodo di gestione di entità  

Tutte le operazioni descritte in questo documento seguono un modello richiesta/risposta, hanno come ambito un'entità e richiedono il collegamento a un nodo di gestione di entità.  
  
### <a name="create-link-for-sending-requests"></a>Creare il collegamento per l'invio delle richieste  

Crea un collegamento al nodo di gestione per l'invio delle richieste.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Creare il collegamento per la ricezione delle risposte  

Crea un collegamento per la ricezione delle risposte dal nodo di gestione.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Trasferire un messaggio di richiesta  

Trasferisce un messaggio di richiesta.  
È possibile aggiungere facoltativamente uno stato di transazione per le operazioni che supportano la transazione.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Ricevere un messaggio di risposta  

Riceve il messaggio di risposta dal collegamento per le risposte.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Il messaggio di risposta è nel modulo seguente:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Indirizzo delle entità del bus di servizio  

L'indirizzo delle entità del bus di servizio deve essere definito come segue:  
  
|Tipo di entità|Indirizzo|Esempio|  
|-----------------|-------------|-------------|  
|coda|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|argomento|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|sottoscrizione|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operazioni sui messaggi  
  
### <a name="message-renew-lock"></a>Rinnovo del blocco del messaggio  

Estende il blocco di un messaggio per il tempo specificato nella descrizione dell'entità.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
 Il corpo del messaggio di richiesta deve essere costituito da una sezione amqp-value contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matrice di UUID|SÌ|Token di blocco del messaggio da rinnovare.|  

> [!NOTE]
> I token di blocco vengono visualizzati nella proprietà `DeliveryTag` per i messaggi ricevuti. Vedere questo esempio in [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) in cui vengono recuperati tali token. Il token può essere visualizzato anche in DeliveryAnnotations come x-opt-lock-token. Tale visualizzazione, tuttavia, ma non è garantita ed è preferibile quella in `DeliveryTag`. 
> 
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione amqp-value contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expirations|matrice di timestamp|SÌ|Nuova scadenza del token di blocco del messaggio corrispondente ai token di blocco della richiesta.|  
  
### <a name="peek-message"></a>Visualizzazione del messaggio  

Visualizza i messaggi senza blocco.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|SÌ|Numero di sequenza da cui iniziare la visualizzazione.|  
|`message-count`|int|SÌ|Numero massimo di messaggi da visualizzare.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 204: (Nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|SÌ|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|Message|matrice di byte|SÌ|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="schedule-message"></a>Pianificazione del messaggio  

Pianifica i messaggi. Questa operazione supporta la transazione.
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|SÌ|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|message-id|string|SÌ|`amqpMessage.Properties.MessageId` in formato stringa|  
|session-id|string|No|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|tramite chiave di partizione|string|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|Message|matrice di byte|SÌ|Messaggio con codifica in transito AMQP 1.0.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|SÌ|Numero di sequenza dei messaggi pianificati. Il numero di sequenza viene usato per l'annullamento.|  
  
### <a name="cancel-scheduled-message"></a>Annullamento del messaggio pianificato  

Annulla i messaggi pianificati.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|SÌ|Numero di sequenza dei messaggi pianificati da annullare.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|string|No|Descrizione dello stato.|   
  
## <a name="session-operations"></a>Operazioni sulle sessioni  
  
### <a name="session-renew-lock"></a>Rinnovo del blocco della sessione  

Estende il blocco di un messaggio per il tempo specificato nella descrizione dell'entità.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|string|SÌ|ID sessione.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 204: (Nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|SÌ|Nuova scadenza.|  
  
### <a name="peek-session-message"></a>Visualizzazione del messaggio di sessione  

Visualizza i messaggi di sessione senza blocco.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|SÌ|Numero di sequenza da cui iniziare la visualizzazione.|  
|message-count|int|SÌ|Numero massimo di messaggi da visualizzare.|  
|session-id|string|SÌ|ID sessione.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 204: (Nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|SÌ|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
 Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|Message|matrice di byte|SÌ|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="set-session-state"></a>Impostazione dello stato della sessione  

Imposta lo stato di una sessione.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|string|SÌ|ID sessione.|  
|session-state|matrice di byte|SÌ|Dati binari opachi.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
### <a name="get-session-state"></a>Recupero dello stato della sessione  

Recupera lo stato di una sessione.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|string|SÌ|ID sessione.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-state|matrice di byte|SÌ|Dati binari opachi.|  
  
### <a name="enumerate-sessions"></a>Enumerazione delle sessioni  

Enumera le sessioni per un'entità di messaggistica.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|SÌ|Filtro per includere solo le sessioni aggiornate dopo un determinato momento.|  
|skip|int|SÌ|Numero di sessioni da ignorare.|  
|top|int|SÌ|Numero massimo di sessioni.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 204: (Nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|skip|int|SÌ|Numero di sessioni ignorate se il codice di stato è 200.|  
|sessions-ids|matrice di stringhe|SÌ|Matrice di ID sessione se il codice di stato è 200.|  
  
## <a name="rule-operations"></a>Operazioni sulle regole  
  
### <a name="add-rule"></a>Aggiunta di una regola  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|SÌ|Nome della regola, senza nomi di sottoscrizione e argomento.|  
|rule-description|map|SÌ|Descrizione della regola, come specificato nella sezione successiva.|  
  
Il mapping **rule-description** deve includere le voci seguenti, in cui **sql-filter** e **correlation-filter** si escludono a vicenda:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|SÌ|`sql-filter`, come specificato nella sezione successiva.|  
|correlation-filter|map|SÌ|`correlation-filter`, come specificato nella sezione successiva.|  
|sql-rule-action|map|SÌ|`sql-rule-action`, come specificato nella sezione successiva.|  
  
Il mapping sql-filter deve includere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expression|string|SÌ|Espressione di filtro SQL.|  
  
Il mapping **correlation-filter** deve includere almeno una delle voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|No||  
|message-id|string|No||  
|to|string|No||  
|reply-to|string|No||  
|label|string|No||  
|session-id|string|No||  
|reply-to-session-id|string|No||  
|content-type|string|No||  
|properties|map|No|Mapping alle [proprietà della classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) del bus di servizio.|  
  
Il mapping **sql-rule-action** deve includere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expression|string|SÌ|Espressione di azione SQL.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
### <a name="remove-rule"></a>Rimozione di una regola  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|SÌ|Nome della regola, senza nomi di sottoscrizione e argomento.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
### <a name="get-rules"></a>Regole Get

#### <a name="request"></a>Richiesta

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:

|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  

Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|top|int|SÌ|Il numero di regole da recuperare nella pagina.|  
|skip|int|SÌ|Il numero di regole da ignorare. Definisce l'indice iniziale (+ 1) nell'elenco di regole. | 

#### <a name="response"></a>Response

Il messaggio di risposta include le proprietà seguenti:

|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|regole| matrice di mapping|SÌ|Matrice di regole. Ogni regola è rappresentata da una mappa.|

Ogni voce della mappa nella matrice include le proprietà seguenti:

|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-description|matrice di oggetti descritti|SÌ|`com.microsoft:rule-description:list` con codice descritto AMQP 0x0000013700000004| 

`com.microsoft.rule-description:list` è una matrice di oggetti descritti. La matrice include quanto segue:

|Indice|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
| 0 | matrice di oggetti descritti | SÌ | `filter` come specificato di seguito. |
| 1 | matrice di oggetto descritto | SÌ | `ruleAction` come specificato di seguito. |
| 2 | string | SÌ | nome della regola. |

`filter` può essere di uno dei tipi seguenti:

| Nome descrittore | Codice descrittore | Value |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtro di correlazione |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtro true che rappresenta 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtro false che rappresenta 1 = 0 |

`com.microsoft:sql-filter:list` è una matrice descritta che include:

|Indice|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
| 0 | string | SÌ | Espressione filtro SQL |

`com.microsoft:correlation-filter:list` è una matrice descritta che include:

|Indice (se presente)|Tipo di valore|Contenuti del valore|  
|---------|----------------|--------------|
| 0 | string | ID correlazione |
| 1 | string | ID del messaggio |
| 2 | string | Per |
| 3 | string | Rispondi a |
| 4 | string | Etichette |
| 5 | string | ID sessione |
| 6 | string | ID sessione risposta|
| 7 | string | Content Type |
| 8 | Mappa | Mappa delle proprietà definite dall'applicazione |

`ruleAction` può essere di uno dei tipi seguenti:

| Nome descrittore | Codice descrittore | Value |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Operazione regola vuota: nessuna operazione regola presente |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Operazione regola SQL |

`com.microsoft:sql-rule-action:list` è una matrice di oggetti descritti la cui prima voce è una stringa che contiene l'espressione dell'operazione regola SQL.

## <a name="deferred-message-operations"></a>Operazioni sui messaggi rinviati  
  
### <a name="receive-by-sequence-number"></a>Ricezione in base al numero di sequenza  

Riceve i messaggi rinviati in base al numero di sequenza.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|SÌ|Numeri di sequenza.|  
|receiver-settle-mode|ubyte|SÌ|Modalità di **finalizzazione del ricevitore**, come indicata nella specifica di base AMQP versione 1.0.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|SÌ|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|SÌ|Token di blocco se il valore di `receiver-settle-mode` è 1.|  
|Message|matrice di byte|SÌ|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Aggiornamento dello stato di ricezione  

Aggiorna lo stato di ricezione dei messaggi rinviati. Questa operazione supporta le transazioni.
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|SÌ|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|SÌ|completed<br /><br /> abandoned<br /><br /> suspended|  
|lock-tokens|matrice di UUID|SÌ|Token di blocco dei messaggi per aggiornare lo stato di ricezione.|  
|deadletter-reason|string|No|Può essere impostato se lo stato di ricezione è **suspended**.|  
|deadletter-description|string|No|Può essere impostato se lo stato di ricezione è **suspended**.|  
|properties-to-modify|map|No|Elenco delle proprietà dei messaggi negoziati del bus di servizio da modificare.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SÌ|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su AMQP e sul bus di servizio, visitare i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [Guida al protocollo AMQP 1.0]
* [AMQP nel bus di servizio per Windows Server]

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Guida al protocollo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP nel bus di servizio per Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
