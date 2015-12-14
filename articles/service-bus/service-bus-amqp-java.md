<properties 
   pageTitle="Bus di servizio e Java con AMQP 1.0 | Microsoft Azure"
   description="Uso del bus di servizio da Java con AMQP"
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

# Uso del bus di servizio da Java con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java Message Service (JMS) è un'API standard per l'uso di middleware orientato ai messaggi sulla piattaforma Java. Il bus di servizio di Azure è stato testato con AMQP 1.0 basato sulla libreria client JMS sviluppata dal progetto Apache Qpid. Questa libreria supporta l'API JMS 1.1 completa e può essere usata con qualsiasi servizio di messaggistica conforme a AMQP 1.0. Questo scenario è supportato anche nel bus di servizio per Windows Server (bus di servizio locale). Per altre informazioni, vedere [AMQP nel bus di servizio per Windows Server][].

## Download della libreria client JMS basata su AMQP 1.0 di Apache Qpid

Per informazioni sul download dell'ultima versione della libreria client JMS basata su AMQP 1.0 di Apache Qpid, visitare il sito Web all'indirizzo [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

È necessario aggiungere i seguenti quattro file JAR dall'archivio di distribuzione AMQP 1.0 di Apache Qpid al CLASSPATH Java durante la compilazione e l'esecuzione di applicazioni JMS con il bus di servizio:

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## Utilizzo di code, argomenti e sottoscrizioni del bus di servizio da JMS

### Java Naming and Directory Interface (JNDI)

JMS usa l'interfaccia JNDI (Java Naming and Directory Interface) per creare una separazione tra i nomi logici e i nomi fisici. Con JNDI vengono risolti due tipi di oggetti JMS: **ConnectionFactory** e **Destination**. JNDI usa un modello di provider in cui è possibile inserire diversi servizi directory per gestire le attività di risoluzione dei nomi. La libreria JMS basata su AMQP 1.0 di Apache Qpid viene fornita con un semplice provider JNDI basato su file delle proprietà, che viene configurato usando un file di testo.

Il provider JNDI basato su file delle proprietà Qpid viene configurato usando un file delle proprietà con il formato seguente:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### Configurazione della factory di connessione

La voce utilizzata per definire un oggetto **ConnectionFactory** nel provider JNDI basato sul file delle proprietà Qpid è nel formato seguente:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Dove `[jndi\_name]` e `[ConnectionURL]` hanno i significati seguenti:

| Name | Significato | | | | |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]` | Nome logico della factory di connessione. Questo nome viene risolto nell'applicazione Java mediante il metodo `IntialContext.lookup()` JNDI. | | | | |
| `[ConnectionURL]` | URL che fornisce alla libreria JMS le informazioni necessarie per il broker AMQP. | | | | |

Il formato dell'URL di connessione è il seguente:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Dove `[namespace]`, `[username]` e `[password]` hanno i significati seguenti:

| Name | Significato | | | | |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | Spazio dei nomi del bus di servizio ottenuto dal [portale di Azure classico][]. | | | | |
| `[username]` | Nome dell'autorità emittente del bus di servizio ottenuto dal [portale di Azure classico][]. | | | | |
| `[password]` | Chiave dell'autorità emittente del bus di servizio con codifica URL ottenuta dal [portale di Azure classico][]. | | | | |

> [AZURE.NOTE]è necessario applicare manualmente la codifica URL alla password. Un'efficace utilità di codifica URL è disponibile all'indirizzo [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Ad esempio, se le informazioni ottenute dal portale sono le seguenti:

| Spazio dei nomi: | test.servicebus.windows.net |
|--------------|----------------------------------------------|
| Nome autorità emittente: | proprietario |
| Chiave autorità emittente: | abcdefg |

Per definire un oggetto **ConnectionFactory** denominato `SBCONNECTIONFACTORY`, la stringa di configurazione sarà la seguente:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://owner:abcdefg@test.servicebus.windows.net
```

#### Configurazione delle destinazioni

La voce che definisce una destinazione nel provider JNDI basato su file delle proprietà Qpid ha il formato seguente:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Dove `[jndi\_name]` e `[physical\_name]` hanno i significati seguenti:

| Name | Significato |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]` | Nome logico della destinazione. Questo nome viene risolto nell'applicazione Java mediante il metodo `IntialContext.lookup()` JNDI. |
| `[physical\name]` | Nome dell'entità del bus di servizio a cui l'applicazione invia messaggi o da cui l'applicazione riceve messaggi. |

Tenere presente quanto segue:

- Il valore `[physical\name]` può essere una coda o un argomento del bus di servizio.
- In caso di ricezione da una sottoscrizione a un argomento del bus di servizio, il nome fisico specificato in JNDI deve essere il nome dell'argomento. Il nome della sottoscrizione viene fornito al momento della creazione della sottoscrizione durevole nel codice dell'applicazione JMS.
- È anche possibile considerare una sottoscrizione dell'argomento del bus di servizio come una coda JMS. Questo approccio offre alcuni vantaggi: lo stesso codice ricevitore può essere usato per sottoscrizioni di code e argomenti e tutte le informazioni sugli indirizzi (nomi di argomenti e sottoscrizioni) vengono esternalizzate nel file delle proprietà.
- Per considerare una sottoscrizione dell'argomento del bus di servizio come coda JMS, la voce del file delle proprietà dovrà avere il formato seguente: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

Per definire una destinazione JMS logica denominata "TOPIC", con mapping a un argomento del bus di servizio denominato "topic1", la voce del file delle proprietà sarà analoga alla seguente:

```
topic.TOPIC = topic1
```

### Invio di messaggi tramite JMS

Il codice seguente illustra come inviare un messaggio a un argomento del bus di servizio. Si presuppone che `SBCONNECTIONFACTORY` e `TOPIC` siano definiti in un file di configurazione **servicebus.properties**, come illustrato nella sezione precedente.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### Ricezione di messaggi tramite JMS

Il codice seguente illustra `how` ricevere un messaggio da una sottoscrizione dell'argomento del bus di servizio. Si presuppone che `SBCONNECTIONFACTORY` e TOPIC siano definiti in un file di configurazione **servicebus.properties**, come illustrato nella sezione precedente. Si presuppone anche che il nome della sottoscrizione sia `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### Indicazioni per lo sviluppo di applicazioni affidabili

La specifica JMS definisce il modo in cui è necessario scrivere il contratto di eccezioni dei metodi API e il codice dell'applicazione in modo da gestire queste eccezioni. Ecco alcuni altri punti da tenere presenti riguardo alla gestione delle eccezioni:

-   Registrare un oggetto **ExceptionListener** con la connessione JMS mediante **connection.setExceptionListener**. In questo modo, un client può ricevere una notifica di un problema in modo asincrono. Questa notifica è particolarmente importante per le connessioni che usano solo messaggi, perché non possono individuare eventuali errori di connessione in altro modo. L'oggetto **ExceptionListener** viene chiamato se si verifica un problema con la connessione AMQP sottostante, la sessione o il collegamento. In questa situazione il programma dell'applicazione deve ricreare completamente gli oggetti **JMS Connection**, **Session**, **MessageProducer** e **MessageConsumer**.

-   Per verificare che l'invio di un messaggio da **MessageProducer** a un'entità del bus di servizio sua riuscito, assicurarsi che l'applicazione sia stata configurata con il set di proprietà di sistema **qpid.sync\_publish**. È possibile eseguire questa operazione avviando il programma con l'opzione VM Java **-Dqpid.sync\_publish=true** impostata nella riga di comando all'avvio dell'applicazione. L'impostazione di questa opzione configura la libreria in modo che restituisca dati dalla chiamata di invio solo dopo la ricezione della conferma di accettazione del messaggio da parte del bus di servizio. Se si verifica un problema durante l'operazione di invio, verrà generata un'eccezione **JMSException**. Le cause possono essere due:
	1. Se il problema è dovuto al fatto che il bus di servizio rifiuta l'invio del messaggio specifico, verrà generata un'eccezione **MessageRejectedException**. Questo errore può essere temporaneo o dovuto a un problema con il messaggio. L'azione consigliata consiste nel riprovare diverse volte l'operazione con una logica di backoff. Se il problema persiste, il messaggio deve essere abbandonato registrando in locale un errore. Non è necessario ricreare gli oggetti **JMS Connection**, **Session** o **MessageProducer** in questa situazione. 
	2. Se il problema è dovuto al fatto che il bus di servizio chiude il collegamento AMQP, verrà generata un'eccezione **InvalidDestinationException**. La causa può essere un problema temporaneo o l'eliminazione dell'entità del messaggio. In entrambi i casi sarà necessario ricreare gli oggetti **JMS Connection**, **Session** e **MessageProducer**. Se la condizione di errore è temporanea, questa operazione verrà infine completata correttamente. Se l'entità è stata eliminata, l'errore sarà permanente.

## Messaggistica tra .NET e JMS

### Corpi dei messaggi

JMS definisce cinque tipi di messaggio diversi: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** e **TextMessage**. L'API .NET del bus di servizio ha un singolo tipo di messaggio, ovvero, [BrokeredMessage][].

#### Da JMS all'API .NET del bus di servizio

Le sezioni seguenti descrivono come usare messaggi di ognuno dei tipi di messaggio JMS da .NET. Non è stato incluso alcun esempio **ObjectMessage**, poiché il corpo di un messaggio **ObjectMessage** contiene un oggetto serializzabile nel linguaggio di programmazione Java, che non può essere interpretato da un'applicazione .NET.

##### BytesMessage

Il codice seguente illustra come utilizzare il corpo di un oggetto **BytesMessage** tramite le API .NET del bus di servizio.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### MapMessage

Il codice seguente illustra come utilizzare il corpo di un oggetto **MapMessage** tramite le API .NET del bus di servizio. Questo codice esegue l'iterazione tra gli elementi della mappa, visualizzando il nome e il valore di ognuno.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### StreamMessage

Il codice seguente illustra come utilizzare il corpo di un oggetto **StreamMessage** tramite le API .NET del bus di servizio. Questo codice elenca ognuno degli elementi del flusso, insieme ai rispettivi tipi.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### TextMessage

Il codice seguente illustra come utilizzare il corpo di un oggetto **TextMessage** tramite le API .NET del bus di servizio. Questo codice permette di visualizzare la stringa di testo contenuta nel corpo del messaggio.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### Dalle API .NET del bus di servizio a JMS

Le sezioni seguenti mostrano il modo in cui un'applicazione .NET può creare un messaggio ricevuto in JMS in ognuno dei diversi tipi di messaggio JMS. Non è stato incluso alcun esempio **ObjectMessage**, poiché il corpo di un messaggio **ObjectMessage** contiene un oggetto serializzabile nel linguaggio di programmazione Java, che non può essere interpretato da un'applicazione .NET.

##### BytesMessage

Il codice seguente illustra come creare in .NET un oggetto [BrokeredMessage][] che viene ricevuto da un client JMS come **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### StreamMessage

Il codice seguente illustra come creare in .NET un oggetto [BrokeredMessage][] che viene ricevuto da un client JMS come **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### TextMessage

Il codice seguente illustra come utilizzare il corpo di un oggetto **TextMessage** tramite l'API .NET del bus di servizio. Questo codice permette di visualizzare la stringa di testo contenuta nel corpo del messaggio.

```
message = new BrokeredMessage("this is a text string");
```

### Proprietà dell'applicazione

####Da JMS alle API .NET del bus di servizio

I messaggi JMS supportano le proprietà dell'applicazione dei tipi di seguenti: **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** e **String**. Il codice Java seguente illustra come impostare le proprietà in un messaggio usando ognuno di questi tipi di proprietà.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

Nelle API del bus di servizio le proprietà dell'applicazione di messaggi vengono inserite nella raccolta **Properties** di [BrokeredMessage][]. Il codice seguente illustra come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client JMS.

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

La tabella seguente illustra il mapping dei tipi di proprietà JMS ai tipi di proprietà .NET.

| Tipo di proprietà JMS | Tipo di proprietà .NET |
|-------------------|--------------------|
| Byte | sbyte |
| Integer | int |
| Float | float |
| Double | double |
| Boolean | bool |
| String | string |

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
```

Il codice Java seguente illustra come leggere le proprietà dell'applicazione di un messaggio ricevuto da un client .NET del bus di servizio.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

La tabella seguente illustra il mapping dei tipi di proprietà .NET ai tipi di proprietà JMS.

| Tipo di proprietà .NET | Tipo di proprietà JMS | Note |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | UnsignedByte | - | | sbyte | Byte | - | | char | Character | - | | short | Short | - | | ushort | UnsignedShort | - | | int | Integer | - | | uint | UnsignedInteger | - | | long | Long | - | | ulong | UnsignedLong | - | | float | Float | - | | double | Double | - | | decimal | BigDecimal | - | | bool | Boolean | - | | Guid | UUID | - | | string | String | - | | DateTime | Date | - | | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks con mapping al tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | | TimeSpan | DescribedType | Timespan.Ticks con mapping al tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | | Uri | DescribedType | Uri.AbsoluteUri con mapping al tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Intestazioni standard

Le tabelle seguenti illustrano in che modo viene eseguito il mapping delle intestazioni standard JMS e le proprietà standard di [BrokeredMessage][] mediante AMQP 1.0.

#### Da JMS alle API .NET del bus di servizio

| JMS | .NET del bus di servizio | Note |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID | - | | JMSDeliveryMode | Attualmente non disponibile | Il bus di servizio supporta solo i messaggi durevoli, ad esempio, DeliveryMode.PERSISTENT, indipendentemente da quanto specificato. | | JMSDestination | Message.To | - | | JMSExpiration | Message. TimeToLive | Conversione | | JMSMessageID | Message.MessageID | Per impostazione predefinita, JMSMessageID viene codificato in formato binario nel messaggio AMQP. Alla ricezione dell'ID messaggio binario, la libreria client .NET esegue la conversione in una rappresentazione di stringa basata sui valori Unicode dei byte. Per fare sì che la libreria JMS torni a usare gli ID messaggio stringa, aggiungere la stringa "binary-messageid=false" ai parametri di query dell'oggetto JNDI ConnectionURL. Ad esempio: "amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false". | | JMSPriority | Attualmente non disponibile | Il bus di servizio non supporta la priorità dei messaggi. | | JMSRedelivered | Attualmente non disponibile | - | | JMSReplyTo | Message. ReplyTo | - | | JMSTimestamp | Message.EnqueuedTimeUtc | Conversione | | JMSType | Message.Properties["jms-type"] | - |

#### Dalle API .NET del bus di servizio a JMS

| .NET del bus di servizio | JMS | Note |
|-------------------------|------------------|-------------------------|
| ContentType | - | Attualmente non disponibile | | CorrelationId | JMSCorrelationID | - | | EnqueuedTimeUtc | JMSTimestamp | Conversione | | Label | n/d | Attualmente non disponibile | | MessageId | JMSMessageID | - | | ReplyTo | JMSReplyTo | - | | ReplyToSessionId | n/d | Attualmente non disponibile | | ScheduledEnqueueTimeUtc | n/d | Attualmente non disponibile | | SessionId | n/d | Attualmente non disponibile | | TimeToLive | JMSExpiration | Conversione | | To | JMSDestination | - |

## Funzionalità non supportate e restrizioni

Quando si usa JMS su AMQP 1.0 con il bus di servizio esistono le seguenti restrizioni:

-   È consentito solo un oggetto **MessageProducer** o **MessageConsumer** per sessione. Per creare più oggetti **MessageProducer** o **MessageConsumer** in un'applicazione, creare sessioni dedicate per ogni oggetto.

-   Le sottoscrizioni a un argomento volatile non sono attualmente supportate.

-   Gli oggetti **MessageSelector** non sono supportati.

-   Le destinazioni temporanee, ad esempio **TemporaryQueue** o **TemporaryTopic**, non sono supportate così come le API **QueueRequestor** e **TopicRequestor** che le usano.

-   Le sessioni transazionali non sono supportate.

-   Le transazioni distribuite non sono supportate.

## Passaggi successivi

Per altre informazioni, vedere i collegamenti seguenti:

- [Panoramica di AMQP per il bus di servizio]
- [AMQP nel bus di servizio per Windows Server]

[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[portale di Azure classico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->