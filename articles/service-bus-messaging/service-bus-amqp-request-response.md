---
title: AMQP 1.0 nelle operazioni basate su richiesta/risposta del bus di servizio di Azure | Microsoft Docs
description: Elenco delle operazioni basate su richiesta/risposta del bus di servizio di Microsoft Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 756565b3da6e0a818d1ee3d5e17f942d96be14f0
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 nel bus di servizio di Microsoft Azure: operazioni basate su richiesta/risposta

Questo argomento offre un elenco delle operazioni basate su richiesta/risposta del bus di servizio di Microsoft Azure. Le informazioni sono basate sulla bozza di lavoro di AMQP Management versione 1.0.  
  
Per una guida dettagliata al protocollo wire-level AMQP 1.0, che illustra come il bus di servizio è basato sulla specifica tecnica OASIS AMQP e la implementa, vedere [Guida al protocollo AMQP 1.0 nel bus di servizio e in Hub eventi di Azure](service-bus-amqp-protocol-guide.md).  
  
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
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
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
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
 Il corpo del messaggio di richiesta deve essere costituito da una sezione amqp-value contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matrice di UUID|Sì|Token di blocco del messaggio da rinnovare.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione amqp-value contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expirations|matrice di timestamp|Sì|Nuova scadenza del token di blocco del messaggio corrispondente ai token di blocco della richiesta.|  
  
### <a name="peek-message"></a>Visualizzazione del messaggio  

Visualizza i messaggi senza blocco.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Sì|Numero di sequenza da cui iniziare la visualizzazione.|  
|`message-count`|int|Sì|Numero massimo di messaggi da visualizzare.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="schedule-message"></a>Pianificazione del messaggio  

Pianifica i messaggi.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|message-id|string|Sì|`amqpMessage.Properties.MessageId` in formato stringa|  
|session-id|string|Sì|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|Sì|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numero di sequenza dei messaggi pianificati. Il numero di sequenza viene usato per l'annullamento.|  
  
### <a name="cancel-scheduled-message"></a>Annullamento del messaggio pianificato  

Annulla i messaggi pianificati.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numero di sequenza dei messaggi pianificati da annullare.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numero di sequenza dei messaggi pianificati. Il numero di sequenza viene usato per l'annullamento.|  
  
## <a name="session-operations"></a>Operazioni sulle sessioni  
  
### <a name="session-renew-lock"></a>Rinnovo del blocco della sessione  

Estende il blocco di un messaggio per il tempo specificato nella descrizione dell'entità.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sì|ID sessione.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|Sì|Nuova scadenza.|  
  
### <a name="peek-session-message"></a>Visualizzazione del messaggio di sessione  

Visualizza i messaggi di sessione senza blocco.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|Sì|Numero di sequenza da cui iniziare la visualizzazione.|  
|message-count|int|Sì|Numero massimo di messaggi da visualizzare.|  
|session-id|string|Sì|ID sessione.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
 Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="set-session-state"></a>Impostazione dello stato della sessione  

Imposta lo stato di una sessione.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sì|ID sessione.|  
|session-state|matrice di byte|Sì|Dati binari opachi.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
### <a name="get-session-state"></a>Recupero dello stato della sessione  

Recupera lo stato di una sessione.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sì|ID sessione.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-state|matrice di byte|Sì|Dati binari opachi.|  
  
### <a name="enumerate-sessions"></a>Enumerazione delle sessioni  

Enumera le sessioni per un'entità di messaggistica.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|Sì|Filtro per includere solo le sessioni aggiornate dopo un determinato momento.|  
|skip|int|Sì|Numero di sessioni da ignorare.|  
|top|int|Sì|Numero massimo di sessioni.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|skip|int|Sì|Numero di sessioni ignorate se il codice di stato è 200.|  
|sessions-ids|matrice di stringhe|Sì|Matrice di ID sessione se il codice di stato è 200.|  
  
## <a name="rule-operations"></a>Operazioni sulle regole  
  
### <a name="add-rule"></a>Aggiunta di una regola  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sì|Nome della regola, senza nomi di sottoscrizione e argomento.|  
|rule-description|map|Sì|Descrizione della regola, come specificato nella sezione successiva.|  
  
Il mapping **rule-description** deve includere le voci seguenti, in cui **sql-filter** e **correlation-filter** si escludono a vicenda:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sì|`sql-filter`, come specificato nella sezione successiva.|  
|correlation-filter|map|Sì|`correlation-filter`, come specificato nella sezione successiva.|  
|sql-rule-action|map|Sì|`sql-rule-action`, come specificato nella sezione successiva.|  
  
Il mapping sql-filter deve includere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expression|string|Sì|Espressione di filtro SQL.|  
  
Il mapping **correlation-filter** deve includere almeno una delle voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|No||  
|message-id|string|No||  
|to|string|No||  
|reply-to|string|No||  
|label|string|No||  
|session-id|string|No||  
|reply-to-session-id|string|No||  
|content-type|string|No||  
|properties|map|No|Mapping alle [proprietà della classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties) del bus di servizio.|  
  
Il mapping **sql-rule-action** deve includere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expression|string|Sì|Espressione di azione SQL.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
### <a name="remove-rule"></a>Rimozione di una regola  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sì|Nome della regola, senza nomi di sottoscrizione e argomento.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
## <a name="deferred-message-operations"></a>Operazioni sui messaggi rinviati  
  
### <a name="receive-by-sequence-number"></a>Ricezione in base al numero di sequenza  

Riceve i messaggi rinviati in base al numero di sequenza.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numeri di sequenza.|  
|receiver-settle-mode|ubyte|Sì|Modalità di **finalizzazione del ricevitore**, come indicata nella specifica di base AMQP versione 1.0.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Sì|Token di blocco se il valore di `receiver-settle-mode` è 1.|  
|message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Aggiornamento dello stato di ricezione  

Aggiorna lo stato di ricezione dei messaggi rinviati.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sì|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No|Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|Sì|completed<br /><br /> abandoned<br /><br /> suspended|  
|lock-tokens|matrice di UUID|Sì|Token di blocco dei messaggi per aggiornare lo stato di ricezione.|  
|deadletter-reason|string|No|Può essere impostato se lo stato di ricezione è **suspended**.|  
|deadletter-description|string|No|Può essere impostato se lo stato di ricezione è **suspended**.|  
|properties-to-modify|map|No|Elenco delle proprietà dei messaggi negoziati del bus di servizio da modificare.|  
  
#### <a name="response"></a>Response  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatorio|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|string|No|Descrizione dello stato.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su AMQP e sul bus di servizio, visitare i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]
* [AMQP nel bus di servizio per Windows Server]

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.asp
