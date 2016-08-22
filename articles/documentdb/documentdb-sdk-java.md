<properties 
	pageTitle="SDK e API di DocumentDB Java | Microsoft Azure" 
	description="Altre informazioni sulle date di rilascio e le date di ritiro dell'SDK e dell'API per Java e sulle modifiche apportate tra le versioni di DocumentDB Java SDK." 
	services="documentdb" 
	documentationCenter="java" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>  

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>  

# SDK e API di DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.JS](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## SDK e API di DocumentDB Java

<table>  
<tr><td>**Download dell'SDK**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**Documentazione sull'API**</td><td>[Java API Reference Documentation (Documentazione di riferimento sull'API Java)](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Introduzione**</td><td>[Get started with the Java SDK](documentdb-java-application.md)</td></tr>
<tr><td>**Runtime attualmente supportato**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## Note sulla versione

### <a name="1.8.1"/>[1\.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Risolto un bug in PartitionKeyDefinitionMap per memorizzare nella cache le raccolte a partizione singola ed evitare richieste aggiuntive di chiavi di partizione da recuperare.
  - Risolto un bug per non eseguire un nuovo tentativo se viene fornito un valore di chiave di partizione errato.

### <a name="1.8.0"/>[1\.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Aggiunta del supporto per gli account di database con più aree.
  - Aggiunta del supporto per la ripetizione automatica delle richieste limitate con la possibilità di personalizzare il numero massimo di tentativi e il relativo tempo di attesa massimo. Vedere RetryOptions e ConnectionPolicy.getRetryOptions().
  - IPartitionResolver basato su codice di partizionamento personalizzato è stato deprecato. Usare le raccolte partizionate per un'archiviazione e una velocità effettiva superiori.

### <a name="1.7.1"/>[1\.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Aggiunta del supporto per il criterio di ripetizione per la limitazione.

### <a name="1.7.0"/>[1\.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Aggiunta del supporto per la durata (TTL) relativa ai documenti.

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implementazione delle [raccolte partizionate](documentdb-partition-data.md) e dei [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md).

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Risolto un bug in HashPartitionResolver per generare valori hash in little endian per coerenza con altri SDK.

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
- Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="1.3.0"/>1.3.0
- Versione ignorata per riportare il numero di versione in allineamento con altri SDK

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Supporta l'indice geospaziale
- Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \\ o terminare con uno spazio.
- Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implementa criteri di indicizzazione V2

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- SDK con disponibilità generale

## Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB tramite un SDK ritirato verrà rifiutata dal servizio.

> [AZURE.WARNING]
Tutte le versioni dell'SDK per Java di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.

<br/>  

| Versione | Data di rilascio | Data di ritiro 
| ---	  | ---	         | ---
| [1\.8.1](#1.8.1) | 30 giugno 2016 |--- | [1\.8.0](#1.8.0) | 14 giugno 2016 |--- | [1\.7.1](#1.7.1) | 30 aprile 2016 |--- | [1\.7.0](#1.7.0) | 27 aprile 2016 |--- | [1\.6.0](#1.6.0) | 29 marzo 2016 |--- | [1\.5.1](#1.5.1) | 31 dicembre 2015 |--- | [1\.5.0](#1.5.0) | 04 dicembre 2015 |--- | [1\.4.0](#1.4.0) | 05 ottobre 2015 |--- | [1\.3.0](#1.3.0) | 05 ottobre 2015 |--- | [1\.2.0](#1.2.0) | 05 agosto 2015 |--- | [1\.1.0](#1.1.0) | 09 luglio 2015 |--- | [1\.0.1](#1.0.1) | 12 maggio 2015 |--- | [1\.0.0](#1.0.0) | 07 aprile 2015 |--- | 0.9.5 - versione non definitiva | 09 marzo 2015 | 29 febbraio 2016 | 0.9.4 - versione non definitiva | 17 febbraio 2015 | 29 febbraio 2016 | 0.9.3 - versione non definitiva | 13 gennaio 2015 | 29 febbraio 2016 | 0.9.2 - versione non definitiva | 19 dicembre 2014 | 29 febbraio 2016 | 0.9.1 - versione non definitiva | 19 dicembre 2014 | 29 febbraio 2016 | 0.9.0 - versione non definitiva | 10 dicembre 2014 | 29 febbraio 2016

## Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche

Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->