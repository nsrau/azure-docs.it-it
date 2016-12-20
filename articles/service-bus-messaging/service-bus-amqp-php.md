---
title: Bus di servizio e PHP con AMQP 1.0 | Documentazione Microsoft
description: Uso del bus di servizio da PHP con AMQP.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dfb26c2b-41d3-4ed6-936b-b8d2f1dbd470
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 122865f056d6bd7fc8c75665d29753542a83405c


---
# <a name="using-service-bus-from-php-with-amqp-10"></a>Uso del bus di servizio da PHP con AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP è un binding di linguaggio PHP per Proton-C. In altri termini, Proton-PHP viene implementato come wrapper attorno a un motore implementato in C.

## <a name="downloading-the-proton-client-library"></a>Download della libreria client basata su Proton
È possibile scaricare Proton-C e i rispettivi binding associati, incluso PHP, da [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Il download è in formato codice sorgente. Per compilare il codice, seguire le istruzioni contenute nel pacchetto scaricato.

> [!IMPORTANT]
> Al momento della stesura di questo articolo, il supporto SSL in Proton-C è disponibile solo per sistemi operativi Linux. Poiché il bus di servizio di Azure richiede l'uso di SSL, è attualmente possibile usare Proton-C e i binding di linguaggio solo per accedere al bus di servizio da Linux. L'abilitazione di Proton-C per l'uso con SSL in Windows è in fase di realizzazione. Verificare periodicamente la presenza di aggiornamenti.
> 
> 

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Uso di code, argomenti e sottoscrizioni del bus di servizio da PHP
Il codice seguente illustra come inviare e ricevere messaggi da un'entità di messaggistica del bus di servizio.

### <a name="sending-messages-using-proton-php"></a>Invio di messaggi tramite Proton-PHP
Il codice seguente illustra come inviare un messaggio a un'entità di messaggistica del bus di servizio.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Ricezione di messaggi tramite Proton-PHP
Il codice seguente illustra come ricevere un messaggio da un'entità di messaggistica del bus di servizio.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Messaggistica tra .NET e Proton-PHP
### <a name="application-properties"></a>Proprietà dell'applicazione
#### <a name="protonphp-to-service-bus-net-apis"></a>Da Proton-PHP alle API .NET del bus di servizio
I messaggi Proton-PHP supportano i tipi seguenti di proprietà dell'applicazione: **integer**, **double**, **Boolean**, **string** e **object**. Il seguente codice PHP mostra come impostare le proprietà in un messaggio usando ognuno di questi tipi di proprietà.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Nelle interfacce API del bus di servizio le proprietà dell'applicazione di messaggi vengono inserite nella raccolta **Properties** di [BrokeredMessage][BrokeredMessage]. Il seguente codice mostra come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

La tabella seguente indica i mapping tra i tipi di proprietà PHP e i tipi di proprietà .NET.

| Tipo di proprietà PHP | Tipo di proprietà .NET |
| --- | --- |
| integer |int |
| double |double |
| boolean |bool |
| string |string |
| object |Object |

#### <a name="service-bus-net-apis-to-php"></a>Dalle API .NET del bus di servizio a PHP
Il tipo [BrokeredMessage][BrokeredMessage] supporta i tipi seguenti di proprietà dell'applicazione: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** e **TimeSpan**. Il codice .NET seguente illustra come impostare le proprietà di un oggetto [BrokeredMessage][BrokeredMessage] usando tutti questi tipi di proprietà.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Il codice PHP seguente illustra come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client .NET del bus di servizio.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

La tabella seguente indica i mapping tra i tipi di proprietà .NET e i tipi di proprietà PHP.

| Tipo di proprietà .NET | Tipo di proprietà PHP | Note |
| --- | --- | --- |
| byte |numero intero |- |
| sbyte |numero intero |- |
| char |Char |Classe Proton-PHP |
| short |numero intero |- |
| ushort |numero intero |- |
| int |numero intero |- |
| uint |Integer |- |
| long |numero intero |- |
| ulong |numero intero |- |
| float |double |- |
| double |double |- |
| Decimal |string |Il separatore decimale non è attualmente supportato con Proton. |
| bool |boolean |- |
| Guid |UUID |Classe Proton-PHP |
| string |string |- |
| DateTime |numero intero |- |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks mappato al tipo AMQP:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan |DescribedType |Timespan.Ticks mappato al tipo AMQP:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> |
| Uri |DescribedType |Uri.AbsoluteUri mappato al tipo AMQP:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### <a name="standard-properties"></a>Proprietà standard
La tabella seguente illustra il mapping tra le proprietà standard del messaggio Proton-PHP e le proprietà standard del messaggio [BrokeredMessage][BrokeredMessage].

| Proton-PHP | .NET del bus di servizio | Note |
| --- | --- | --- |
| Durable |n/d |Il bus di servizio supporta solo messaggi durevoli. |
| Priorità |n/d |Il bus di servizio supporta solo una priorità singola per i messaggi. |
| Ttl |Message.TimeToLive |Conversione, il valore TTL di Proton-PHP è espresso in millisecondi. |
| first\_acquirer |- |- |
| delivery\_count |- |- |
| ID |Message.Id |- |
| user\_id |- |- |
| Indirizzo |Message.To |- |
| Subject |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationId |- |
| content\_type |Message.ContentType |- |
| content\_encoding |n/d |- |
| expiry\_time |Message.ExpiresAtUTC |- |
| creation\_time |n/d |- |
| group\_id |Message.SessionId |- |
| group\_sequence |- |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| Format |n/d |- |

#### <a name="service-bus-net-apis-to-proton-php"></a>Dalle API .NET del bus di servizio a Proton-PHP
| .NET del bus di servizio | Proton-PHP | Note |
| --- | --- | --- |
| ContentType |Message-\>content\_type |- |
| CorrelationId |Message-\>correlation\_id |- |
| EnqueuedTimeUtc |Message-\>annotations[x-opt-enqueued-time] |- |
| Etichetta |Message-\>subject |- |
| MessageId |Message-\>id |- |
| ReplyTo |Message-\>reply\_to |- |
| ReplyToSessionId |Message-\>reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |Message-\>annotations ["x-opt-scheduled-enqueue-time"] |- |
| SessionId |Message-\>group\_id |- |
| TimeToLive |Message-\>ttl |Conversione, il valore TTL di Proton-PHP è espresso in millisecondi. |
| To |Message-\>address |- |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [AMQP nel bus di servizio per Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md



<!--HONumber=Nov16_HO3-->


