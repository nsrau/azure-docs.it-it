<properties 
	pageTitle="DocumentDB Node.js SDK | Microsoft Azure" 
	description="Tutte le informazioni su Node.js SDK, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB Node.js SDK." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [SDK per Java](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Node.js SDK

<table>
<tr><td>**Download**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Contributi**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Documentazione**</td><td>[Documentazione di riferimento per Node.js SDK](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Esempi**</td><td>[Esempi di codice per Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**Introduzione**</td><td>[Introduzione a Node.js SDK](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Piattaforma attualmente supportata**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Note sulla versione

###<a name="1.6.0"/>1.6.0</a>
- [Raccolte partizionate](documentdb-partition-data.md) e [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md) implementati. 

###<a name="1.5.6"/>1.5.6</a>

- È stato corretto il bug RangePartitionResolver.resolveForRead, relativo alla mancata restituzione di collegamenti a causa di un concatenamento non valido dei risultati.

###<a name="1.5.5"/>1.5.5</a>

- Corretto hashParitionResolver resolveForRead(): quando la mancata indicazione di una chiave di partizione generava un'eccezione, invece di restituire un elenco di tutti i collegamenti registrati.

###<a name="1.5.4"/>1.5.4</a>

- È stato corretto il problema [n. 100](https://github.com/Azure/azure-documentdb-node/issues/100) relativo all'agente HTTPS dedicato: evitare di modificare l'agente globale per gli scopi di DocumentDB. Usare un agente dedicato per tutte le richieste della libreria.

###<a name="1.5.3"/>1.5.3</a>

- È stato corretto il problema [n. 81](https://github.com/Azure/azure-documentdb-node/issues/81): gestire correttamente i trattini negli ID dei file multimediali.

###<a name="1.5.2"/>1.5.2</a>

- È stato corretto il problema [n. 95](https://github.com/Azure/azure-documentdb-node/issues/95): avviso di perdita del listener EventEmitter.

###<a name="1.5.1"/>1.5.1</a>

- È stato corretto il problema [n. 92](https://github.com/Azure/azure-documentdb-node/issues/90): ridenominazione della cartella Hash in hash per i sistemi con distinzione tra maiuscole e minuscole.

### <a name="1.5.0"/>1.5.0</a>

- Implementa il supporto del partizionamento orizzontale aggiungendo resolver della partizione a intervalli e hash

### <a name="1.4.0"/>1.4.0</a>

- Implementazione di Upsert. Nuovi metodi upsertXXX in documentClient. 

### <a name="1.3.0"/>1.3.0</a>

- Ignorata per riportare il numero di versione in allineamento con altri SDK

### <a name="1.2.2"/>1.2.2</a>

- Divisione del wrapper Q promise per il nuovo repository
- Aggiornamento del file del pacchetto del Registro di sistema npm

### <a name="1.2.1"/>1.2.1</a>

- Implementazione del routing basato su ID
- Corregge il problema [#49](https://github.com/Azure/azure-documentdb-node/issues/49): conflitto tra la proprietà current e il metodo current()

### <a name="1.2.0"/>1.2.0</a>

- Aggiunta del supporto per l'indice GeoSpatial
- Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, &#47;&#47; o terminare con uno spazio. 
- Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementa criteri di indicizzazione V2

### <a name="1.0.3"/>1.0.3</a>

- Problema [#40](https://github.com/Azure/azure-documentdb-node/issues/40): implementate le configurazioni eslint e grunt nell'SDK core e promise

### <a name="1.0.2"/>1.0.2</a>

- Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45): il wrapper promise non include l'intestazione con errore

### <a name="1.0.1"/>1.0.1</a>

- Implementata la possibilità di eseguire query per i conflitti aggiungendo readConflicts, readConflictAsync e queryConflicts
- Aggiornata la documentazione relativa alle API
- Problema [#41](https://github.com/Azure/azure-documentdb-node/issues/41): errore client.createDocumentAsync  

### <a name="1.0.0"/>1.0.0</a>

- SDK con disponibilità generale

## Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB con un SDK ritirato verrà rifiutata dal servizio.

> [AZURE.WARNING]
Tutte le versioni dell'SDK per Node.js di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.

<br/>

| Versione | Data di rilascio | Data di ritiro 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 06 ottobre 2015 |---
| [1\.3.0](#1.3.0) | 06 ottobre 2015 |---
| [1\.2.2](#1.2.2) | 10 settembre 2015 |---
| [1\.2.1](#1.2.1) | 15 agosto 2015 |---
| [1\.2.0](#1.2.0) | 05 agosto 2015 |---
| [1\.1.0](#1.1.0) | 09 luglio 2015 |---
| [1\.0.3](#1.0.3) | 04 giugno 2015 |---
| [1\.0.2](#1.0.2) | 23 maggio 2015 |---
| [1\.0.1](#1.0.1) | 15 maggio 2015 |---
| [1\.0.0](#1.0.0) | 08 aprile 2015 |---
| 0.9.4-versione non definitiva | 06 aprile 2015 | 29 febbraio 2016
| 0.9.3-versione non definitiva | 14 gennaio 2015 | 29 febbraio 2016
| 0.9.2-versione non definitiva | 18 dicembre 2014 | 29 febbraio 2016
| 0.9.1-versione non definitiva | 22 agosto 2014 | 29 febbraio 2016
| 0.9.0-versione non definitiva | 21 agosto 2014 | 29 febbraio 2016


## Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche

Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0330_2016-->