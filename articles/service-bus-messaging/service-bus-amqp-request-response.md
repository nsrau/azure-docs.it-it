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
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: d72a4de8591898a55e4225ace154fd5ed53e6f91
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
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
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
 Il corpo del messaggio di richiesta deve essere costituito da una sezione amqp-value contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matrice di UUID|Sì|Token di blocco del messaggio da rinnovare.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione amqp-value contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expirations|matrice di timestamp|Sì|Nuova scadenza del token di blocco del messaggio corrispondente ai token di blocco della richiesta.|  
  
### <a name="peek-message"></a>Visualizzazione del messaggio  

Visualizza i messaggi senza blocco.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Sì|Numero di sequenza da cui iniziare la visualizzazione.|  
|`message-count`|int|Sì|Numero massimo di messaggi da visualizzare.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|Message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="schedule-message"></a>Pianificazione del messaggio  

Pianifica i messaggi.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|message-id|stringa|Sì|`amqpMessage.Properties.MessageId` in formato stringa|  
|session-id|stringa|Sì|`amqpMessage.Properties.GroupId as string`|  
|partition-key|stringa|Sì|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|Message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numero di sequenza dei messaggi pianificati. Il numero di sequenza viene usato per l'annullamento.|  
  
### <a name="cancel-scheduled-message"></a>Annullamento del messaggio pianificato  

Annulla i messaggi pianificati.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numero di sequenza dei messaggi pianificati da annullare.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo.|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numero di sequenza dei messaggi pianificati. Il numero di sequenza viene usato per l'annullamento.|  
  
## <a name="session-operations"></a>Operazioni sulle sessioni  
  
### <a name="session-renew-lock"></a>Rinnovo del blocco della sessione  

Estende il blocco di un messaggio per il tempo specificato nella descrizione dell'entità.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|stringa|Sì|ID sessione.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|Sì|Nuova scadenza.|  
  
### <a name="peek-session-message"></a>Visualizzazione del messaggio di sessione  

Visualizza i messaggi di sessione senza blocco.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|Sì|Numero di sequenza da cui iniziare la visualizzazione.|  
|message-count|int|Sì|Numero massimo di messaggi da visualizzare.|  
|session-id|stringa|Sì|ID sessione.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un mapping con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
 Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|Message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="set-session-state"></a>Impostazione dello stato della sessione  

Imposta lo stato di una sessione.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|stringa|Sì|ID sessione.|  
|session-state|matrice di byte|Sì|Dati binari opachi.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
### <a name="get-session-state"></a>Recupero dello stato della sessione  

Recupera lo stato di una sessione.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-id|stringa|Sì|ID sessione.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|session-state|matrice di byte|Sì|Dati binari opachi.|  
  
### <a name="enumerate-sessions"></a>Enumerazione delle sessioni  

Enumera le sessioni per un'entità di messaggistica.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|Sì|Filtro per includere solo le sessioni aggiornate dopo un determinato momento.|  
|skip|int|Sì|Numero di sessioni da ignorare.|  
|top|int|Sì|Numero massimo di sessioni.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) se sono presenti altri messaggi<br /><br /> 0xcc (nessun contenuto) se non sono presenti altri messaggi|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|skip|int|Sì|Numero di sessioni ignorate se il codice di stato è 200.|  
|sessions-ids|matrice di stringhe|Sì|Matrice di ID sessione se il codice di stato è 200.|  
  
## <a name="rule-operations"></a>Operazioni sulle regole  
  
### <a name="add-rule"></a>Aggiunta di una regola  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-name|stringa|Sì|Nome della regola, senza nomi di sottoscrizione e argomento.|  
|rule-description|map|Sì|Descrizione della regola, come specificato nella sezione successiva.|  
  
Il mapping **rule-description** deve includere le voci seguenti, in cui **sql-filter** e **correlation-filter** si escludono a vicenda:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sì|`sql-filter`, come specificato nella sezione successiva.|  
|correlation-filter|map|Sì|`correlation-filter`, come specificato nella sezione successiva.|  
|sql-rule-action|map|Sì|`sql-rule-action`, come specificato nella sezione successiva.|  
  
Il mapping sql-filter deve includere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expression|stringa|Sì|Espressione di filtro SQL.|  
  
Il mapping **correlation-filter** deve includere almeno una delle voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|correlation-id|stringa|No ||  
|message-id|stringa|No ||  
|to|stringa|No ||  
|reply-to|stringa|No ||  
|label|stringa|No ||  
|session-id|stringa|No ||  
|reply-to-session-id|stringa|No ||  
|content-type|stringa|No ||  
|properties|map|No |Mapping alle [proprietà della classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties) del bus di servizio.|  
  
Il mapping **sql-rule-action** deve includere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|expression|stringa|Sì|Espressione di azione SQL.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
### <a name="remove-rule"></a>Rimozione di una regola  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|rule-name|stringa|Sì|Nome della regola, senza nomi di sottoscrizione e argomento.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
## <a name="deferred-message-operations"></a>Operazioni sui messaggi rinviati  
  
### <a name="receive-by-sequence-number"></a>Ricezione in base al numero di sequenza  

Riceve i messaggi rinviati in base al numero di sequenza.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matrice di long|Sì|Numeri di sequenza.|  
|receiver-settle-mode|ubyte|Sì|Modalità di **finalizzazione del ricevitore**, come indicata nella specifica di base AMQP versione 1.0.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|stringa|No |Descrizione dello stato.|  
  
Il corpo del messaggio di risposta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|del cloud al dispositivo|elenco di mapping|Sì|Elenco di messaggi in cui ogni mapping rappresenta un messaggio.|  
  
Il mapping che rappresenta un messaggio deve contenere le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Sì|Token di blocco se il valore di `receiver-settle-mode` è 1.|  
|Message|matrice di byte|Sì|Messaggio con codifica in transito AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Aggiornamento dello stato di ricezione  

Aggiorna lo stato di ricezione dei messaggi rinviati.  
  
#### <a name="request"></a>Richiesta  

Il messaggio di richiesta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|operation|stringa|Sì|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No |Timeout del server per l'operazione, in millisecondi.|  
  
Il corpo del messaggio di richiesta deve essere costituito da una sezione **amqp-value** contenente un **mapping** con le voci seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|disposition-status|stringa|Sì|completed<br /><br /> abandoned<br /><br /> suspended|  
|lock-tokens|matrice di UUID|Sì|Token di blocco dei messaggi per aggiornare lo stato di ricezione.|  
|deadletter-reason|stringa|No |Può essere impostato se lo stato di ricezione è **suspended**.|  
|deadletter-description|stringa|No |Può essere impostato se lo stato di ricezione è **suspended**.|  
|properties-to-modify|map|No |Elenco delle proprietà dei messaggi negoziati del bus di servizio da modificare.|  
  
#### <a name="response"></a>Risposta  

Il messaggio di risposta deve includere le proprietà di applicazione seguenti:  
  
|Chiave|Tipo di valore|Obbligatoria|Contenuti del valore|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sì|Codice di risposta HTTP [RFC2616]<br /><br /> 200 (OK) in caso di esito positivo, altro valore in caso di esito negativo|  
|statusDescription|stringa|No |Descrizione dello stato.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su AMQP e sul bus di servizio, visitare i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [Guida al protocollo AMQP 1.0]
* [AMQP nel bus di servizio per Windows Server]

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Guida al protocollo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.asp