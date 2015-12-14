<properties 
	pageTitle="DocumentDB Java SDK | Microsoft Azure" 
	description="Altre informazioni sulle date di rilascio e le date di ritiro dell'SDK per Java e sulle modifiche apportate tra le versioni di DocumentDB Java SDK." 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table> <tr><td>**Download**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr> <tr><td>**Contributi**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr> <tr><td>**Documentazione**</td><td>[Documentazione di riferimento sull'SDK per Java](http://azure.github.io/azure-documentdb-java/)</td></tr> <tr><td>**Informazioni di base**</td><td>[Introduzione all'SDK per Java](documentdb-java-application.md)</td></tr> <tr><td>**Runtime attualmente supportato**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr> </table></br>

## Note sulla versione

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
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK in modo da agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB tramite un SDK ritirato verrà rifiutata dal servizio.

> [AZURE.WARNING]Tutte le versioni dell'SDK per Java di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.

<br/>

| Versione | Data di rilascio | Data di ritiro 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 05 ottobre 2015 |--- | [1\.3.0](#1.3.0) | 05 ottobre 2015 |--- | [1\.2.0](#1.2.0) | 05 agosto 2015 |--- | [1\.1.0](#1.1.0) | 09 luglio 2015 |--- | [1\.0.1](#1.0.1) | 12 maggio 2015 |--- | [1\.0.0](#1.0.0) | 07 aprile 2015 |--- | 0.9.5-versione non definitiva | 09 marzo 2015 | 29 febbraio 2016 | 0.9.4-versione non definitiva | 17 febbraio 2015 | 29 febbraio 2016 | 0.9.3-versione non definitiva | 13 gennaio 2015 | 29 febbraio 2016 | 0.9.2-versione non definitiva | 19 dicembre 2014 | 29 febbraio 2016 | 0.9.1-versione non definitiva | 19 dicembre 2014 | 29 febbraio 2016 | 0.9.0-versione non definitiva | 10 dicembre 2014 | 29 febbraio 2016

## Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche

Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1203_2015-->