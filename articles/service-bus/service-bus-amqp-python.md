<properties 
   pageTitle="Bus di servizio e Python con AMQP 1.0 | Microsoft Azure"
   description="Uso del bus di servizio da Python con AMQP."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="sethm" />

# Uso del bus di servizio da Python con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python è un binding di linguaggio Python per Proton-C. In altri termini, Proton-Python viene implementato come wrapper attorno a un motore implementato in C.

## Download della libreria client basata su Proton

È possibile scaricare Proton-C e i rispettivi binding associati, incluso Python, da [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Il download è in formato codice sorgente. Per compilare il codice, seguire le istruzioni contenute nel pacchetto scaricato.

Si noti che al momento della stesura di questo articolo, il supporto SSL in Proton-C è disponibile solo per sistemi operativi Linux. Poiché il bus di servizio di Azure richiede l'uso di SSL, è attualmente possibile usare Proton-C e i binding di linguaggio solo per accedere al bus di servizio da Linux. L'abilitazione di Proton-C per l'uso con SSL in Windows è in fase di realizzazione. Verificare periodicamente la presenza di aggiornamenti.

## Utilizzo di code, argomenti e sottoscrizioni del bus di servizio da Python

Il codice seguente illustra come inviare e ricevere messaggi da un'entità di messaggistica del bus di servizio.

### Invio di messaggi tramite Proton-Python

Il codice seguente illustra come inviare un messaggio a un'entità di messaggistica del bus di servizio.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### Ricezione di messaggi tramite Proton-Python

Il codice seguente illustra come ricevere un messaggio da un'entità di messaggistica del bus di servizio.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## Messaggistica tra .NET e Proton-Python

### Proprietà dell'applicazione

#### Da Proton-Python alle API .NET del bus di servizio

I messaggi Proton-Python supportano i tipi seguenti di proprietà dell'applicazione: **int**, **long**, **float**, **uuid**, **bool**, **string**. Il codice Python seguente illustra come impostare le proprietà in un messaggio usando ognuno di questi tipi di proprietà.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Nell'API del bus di servizio le proprietà dell'applicazione di messaggi vengono inserite nella raccolta **Properties** di [BrokeredMessage][]. Il codice seguente illustra come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

La tabella seguente indica i mapping tra i tipi di proprietà Python e i tipi di proprietà .NET.

| Tipo di proprietà Python | Tipo di proprietà .NET |
|----------------------|--------------------|
| int | int |
| float | double |
| long | int64 |
| uuid | guid |
| bool | bool |
| string | string |

#### Dalle API .NET del bus di servizio a Proton-Python

Il tipo [BrokeredMessage][] supporta i tipi seguenti di proprietà dell'applicazione: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** e **TimeSpan**. Il codice .NET seguente illustra come impostare le proprietà su un oggetto [BrokeredMessage][] tramite uno di questi tipi di proprietà.

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Il codice Python seguente illustra come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client .NET del bus di servizio.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

La tabella seguente indica i mapping tra i tipi di proprietà .NET e i tipi di proprietà Python.

| Tipo di proprietà .NET | Tipo di proprietà Python | Note |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | int | - | | sbyte | int | - | | char | char | Classe Proton-Python | | short | int | - | | ushort | int | - | | int | int | - | | uint | int | - | | long | int | - | | ulong | long | Classe Proton-Python | | float | float | - | | double | float | - | | decimal | String | Valore Decimal attualmente non supportato con Proton. | | bool | bool | - | | Guid | uuid | Classe Proton-Python | | string | string | - | | DateTime | timestamp | Classe Proton-Python | | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks con mapping al tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | | TimeSpan | DescribedType | Timespan.Ticks con mapping al tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | | Uri | DescribedType | Uri.AbsoluteUri con mapping al tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Proprietà standard

La tabella seguente illustra il mapping tra le proprietà standard del messaggio Proton-Python e le proprietà standard del messaggio [BrokeredMessage][].

#### Da Proton-Python alle API .NET del bus di servizio

| Proton-Python | .NET del bus di servizio | Note |
|----------------------|--------------------------|-----------------------------------------------------------|
| durable | n/d | Il bus di servizio supporta solo messaggi durevoli. |
| priority | n/d | Il bus di servizio supporta solo una priorità singola per i messaggi. |
| Ttl | Message.TimeToLive | Conversione, il valore TTL di Proton-Python è espresso in millisecondi. |
| first\_acquirer | n/d | - | | delivery\_count | n/d | - | | Id | Message.MessageID | - | | user\_id | n/d | - | | address | Message.To | - | | subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationID | - | | content\_type | Message.ContentType | - | | content\_encoding | n/d | - | | expiry\_time | n/d | - | | creation\_time | n/d | - | | group\_id | Message.SessionId | - | | group\_sequence | n/d | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | format | n/d | - |

| .NET del bus di servizio | Proton | Note |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType | Message.content\_type | - | | CorrelationId | Message.correlation\_id | - | | EnqueuedTimeUtc | n/d | - | | Label | Message.subject | - | | MessageId | Message.id | - | | ReplyTo | Message.reply\_to | - | | ReplyToSessionId | Message.reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | n/d | - | | SessionId | Message.group\_id | - | | TimeToLive | Message.ttl | Conversione, il valore TTL di Proton-Python è espresso in millisecondi. | | To | Message.address | - |

## Passaggi successivi

Per altre informazioni, vedere i collegamenti seguenti:

- [Panoramica di AMQP per il bus di servizio]
- [Uso del bus di servizio da .NET con AMQP]
- [Uso del bus di servizio da Java con AMQP]
- [Uso del bus di servizio da PHP con AMQP]
- [Installazione di Apache Qpid Proton-C in una macchina virtuale Linux Azure]
- [AMQP nel bus di servizio per Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Uso del bus di servizio da .NET con AMQP]: service-bus-amqp-dotnet.md
[Uso del bus di servizio da Java con AMQP]: service-bus-amqp-java.md
[Uso del bus di servizio da PHP con AMQP]: service-bus-amqp-php.md
[Installazione di Apache Qpid Proton-C in una macchina virtuale Linux Azure]: service-bus-amqp-apache.md

<!---HONumber=Oct15_HO2-->