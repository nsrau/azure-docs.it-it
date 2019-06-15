---
title: Scambiare eventi tra app che usano protocolli diversi - Hub eventi di Azure | Microsoft Docs
description: Questo articolo illustra in che modo i consumer e i producer che usano protocolli diversi (AMQP, Apache Kafka e HTTPS) possono scambiare eventi quando usano Hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: e704a2595130a2a815388447ac482ab96789d64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821776"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Scambiare eventi tra consumer e producer che usano protocolli diversi: AMQP, Kafka e HTTPS
Hub eventi di Azure supporta tre protocolli per consumer e producer: AMQP, Kafka e HTTPS. Ognuno di questi protocolli rappresenta un messaggio in modo univoco, pertanto sorge la domanda seguente: se un'applicazione invia eventi a un hub eventi con un protocollo e li usa con un protocollo diverso, in che modo le varie parti e i valori dell'evento verranno visualizzati dal consumer? Questo articolo illustra le procedure consigliate per producer e consumer per garantire che i valori all'interno di un evento vengano interpretati correttamente dall'applicazione consumer.

I consigli in questo articolo riguardano nello specifico questi client, con le versioni elencate usate nello sviluppo dei frammenti di codice:

* Client Java di Kafka (versione 1.1.1 in https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Client Hub eventi di Microsoft Azure per Java (versione 1.1.0 in https://github.com/Azure/azure-event-hubs-java)
* Client Hub eventi di Microsoft Azure per .NET (versione 2.1.0 in https://github.com/Azure/azure-event-hubs-dotnet)
* Bus di servizio di Microsoft Azure (versione 5.0.0 in https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (supporta solo i producer)

Altri client AMQP possono comportarsi in modo leggermente diverso. AMQP offre un sistema di tipi ben definito, ma le specifiche della serializzazione dei tipi specifici del linguaggio da e verso tale sistema di tipi dipende dal client, così come il modo in cui il client garantisce l'accesso alle parti di un messaggio AMQP.

## <a name="event-body"></a>Corpo dell'evento
Tutti i client Microsoft AMQP rappresentano il corpo dell'evento come un contenitore di byte non interpretato. Un'applicazione di produzione passa una sequenza di byte al client e un'applicazione consumer riceve la stessa sequenza dal client. L'interpretazione della sequenza di byte avviene all'interno del codice dell'applicazione.

Quando si invia un evento tramite HTTPS, il corpo dell'evento è il contenuto POST, considerato anche come byte non interpretati. È facile ottenere lo stesso stato in un producer o consumer Kafka usando gli elementi ByteArraySerializer e ByteArrayDeserializer specificati, come illustrato nel codice seguente:

### <a name="kafka-byte-producer"></a>Producer di byte[] Kafka

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Consumer di byte[] Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Questo codice crea una pipeline di byte trasparente tra le due metà dell'applicazione e consente allo sviluppatore dell'applicazione di serializzare e deserializzare manualmente nel modo preferito e di prendere decisioni di deserializzazione in fase di esecuzione, ad esempio in base alle informazioni sul tipo o sul mittente nelle proprietà impostate dall'utente nell'evento.

Le applicazioni con tipo di corpo a singolo evento fisso potrebbero essere in grado di usare altri serializzatori e deserializzatori Kafka per convertire i dati in modo trasparente. Si consideri ad esempio un'applicazione che usa JSON. La costruzione e l'interpretazione della stringa JSON si verificano a livello di applicazione. A livello di hub eventi, il corpo dell'evento è sempre una stringa, una sequenza di byte che rappresenta caratteri nella codifica UTF-8. In questo caso, il producer o il consumer Kafka può usare l'oggetto StringSerializer o StringDeserializer specificato come illustrato nel codice seguente:

### <a name="kafka-utf-8-string-producer"></a>Producer di stringa UTF-8 di Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Consumer di stringa UTF-8 di Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Per il lato AMQP, Java e .NET offrono processi integrati per convertire le stringhe da e verso sequenze di byte UTF-8. I client Microsoft AMQP rappresentano gli eventi come una classe denominata EventData. Gli esempi seguenti mostrano come serializzare una stringa UTF-8 nel corpo di un evento EventData in un producer AMQP e come deserializzare il corpo di un evento EventData in una stringa UTF-8 in un consumer AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Producer di stringa UTF-8 AMQP di Java
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Consumer di stringa UTF-8 AMQP di Java
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Producer di stringa UTF-8 .NET di C#
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>Consumer di stringa UTF-8 .NET di C#
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Poiché Kafka è open source, lo sviluppatore dell'applicazione può ispezionare l'implementazione di qualsiasi serializzatore o deserializzatore e implementare codice, che produce o usa una sequenza di byte compatibile sul lato AMQP.

## <a name="event-user-properties"></a>Proprietà utente di eventi

Le proprietà impostate dall'utente possono essere impostate e recuperate dai client AMQP (nei client Microsoft AMQP sono denominate proprietà) e Kafka (in cui sono chiamate intestazioni). I mittenti HTTPS possono impostare proprietà utente su un evento specificandole come intestazioni HTTP nell'operazione POST. Kafka considera comunque i corpi e i valori di intestazione degli eventi come sequenze di byte. Mentre nei client AMQP, i valori delle proprietà hanno tipi comunicati codificando i valori delle proprietà in base al sistema di tipi AMQP.

HTTPS è un caso speciale. Al momento dell'invio, tutti i valori delle proprietà sono in testo UTF-8. Il servizio Hub eventi esegue una quantità limitata di interpretazione per convertire i valori delle proprietà appropriati in interi con segno a 32 e 64 bit con codifica AMQP, numeri a virgola mobile a 64 bit e valori booleani. Qualsiasi valore di proprietà che non rientra in questi tipi viene considerato come stringa.

Grazie alla combinazione di questi approcci alla definizione di tipi di proprietà, un consumer Kafka visualizza la sequenza di byte con codifica AMQP non elaborata, incluse le informazioni sul tipo AMQP, mentre un consumer AMQP visualizzerà la sequenza di byte non tipizzata inviata dal producer Kafka, che deve essere interpretata dall'applicazione.

Per i consumer Kafka che ricevono le proprietà da producer AMQP o HTTPS, usare la classe AmqpDeserializer, che viene modellata in base agli altri deserializzatori nell'ecosistema Kafka. Interpreta le informazioni sul tipo nelle sequenze di byte con codifica AMQP per deserializzare i byte di dati in un tipo Java.

È consigliabile includere una proprietà nei messaggi inviati tramite AMQP o HTTPS. Il consumer Kafka può usarla per determinare se i valori di intestazione necessitano di deserializzazione AMQP. Il valore della proprietà non è importante. È sufficiente un nome noto che il consumer Kafka può reperire nell'elenco di intestazioni per modificarne il comportamento di conseguenza.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>Da AMQP a Kafka parte 1: creare e inviare un evento in C# (.NET) con proprietà
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>Da AMQP a Kafka parte 2: usare AmqpDeserializer per deserializzare le proprietà in un consumer Kafka
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Se l'applicazione conosce il tipo previsto per una proprietà, esistono metodi di deserializzazione che non richiedono un cast in un secondo momento, ma generano un errore se la proprietà non è del tipo previsto.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>Da AMQP a Kafka parte 3: un modo diverso di usare AmqpDeserializer in un consumer Kafka
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Procedere nell'altra direzione è più difficile, poiché le intestazioni impostate da un producer Kafka vengono sempre visualizzate da un consumer AMQP come byte non elaborati (digitare org.apache.qpid.proton.amqp.Binary per il client di Hub eventi di Microsoft Azure per Java o `System.Byte[]` per i client AMQP .NET di Microsoft). Il percorso più semplice consiste nell'usare uno dei serializzatori di Kafka per generare i byte per i valori di intestazione sul lato producer Kafka e scrivere un codice di deserializzazione compatibile sul lato consumer AMQP.

Come accade nel processo da AMQP a Kafka, la procedura consigliata è quella di includere una proprietà nei messaggi inviati tramite Kafka. Il consumer AMQP può usarla per determinare se i valori di intestazione necessitano di deserializzazione. Il valore della proprietà non è importante. È sufficiente un nome noto che il consumer AMQP può reperire nell'elenco di intestazioni per modificarne il comportamento di conseguenza. Se non è possibile modificare il producer Kafka, l'applicazione consumer può anche verificare se il valore della proprietà è di tipo binario o byte e tentare di eseguire la deserializzazione in base al tipo.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Da Kafka ad AMQP parte 1: creare e inviare un evento da Kafka con proprietà
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Da Kafka ad AMQP parte 2: deserializzare manualmente le proprietà in C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Da Kafka ad AMQP parte 3: deserializzare manualmente le proprietà in Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come eseguire lo streaming in un'istanza di Hub eventi abilitata per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

* [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)
* [Leggere le informazioni su Hub eventi per Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Esplorare altri esempi di Hub eventi per Kafka in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per lo [streaming di eventi dall'istanza di Kafka locale all'istanza di Hub eventi abilitata per Kafka nel cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Leggere le informazioni su come eseguire lo streaming nel servizio Hub eventi abilitato per Kafka usando le [applicazioni native Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md) o [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
