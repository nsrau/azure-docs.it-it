<properties 
	pageTitle="SDK e API di DocumentDB Node.js | Microsoft Azure" 
	description="Tutte le informazioni sull'SDK e sull'API di Node.js, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB Node.js SDK." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>  

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
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

##SDK e API di DocumentDB Node.js

<table>  
<tr><td>**Scaricare l'SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentazione sull'API**</td><td>[Node.js API reference documentation (Documentazione di riferimento per l'API Node.js)](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Istruzioni per l'installazione dell'SDK**</td><td>[Installation instructions (Istruzioni di installazione)](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Esempi**</td><td>[Node.js code samples (Codici di esempio di Node.js)](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Esercitazione introduttiva**</td><td>[Introduzione a Node.js SDK](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Esercitazione sull'app Web**</td><td>[Compilare un'applicazione Web Node.js con DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Piattaforma attualmente supportata**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Note sulla versione

###<a name="1.9.0"/>1.9.0</a>

- Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, DocumentDB esegue nove tentativi per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il tempo di RetryAfter nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Ora, DocumentDB attende al massimo 30 secondi per ciascuna richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.

- DocumentDB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il conteggio dei tentativi di limitazione di ripetere conteggio e il tempo cumulativo di attesa della richiesta tra i tentativi.

- La classe RetryOptions è stata aggiunta, esponendo la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l'override di alcune opzioni di ripetizione dei tentativi predefinite.

###<a name="1.8.0"/>1.8.0</a>

 - Aggiunta del supporto per gli account di database con più aree.

###<a name="1.7.0"/>1.7.0</a>

- Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

###<a name="1.6.0"/>1.6.0</a>

- Implementazione delle [raccolte partizionate](documentdb-partition-data.md) e dei [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Correzione del bug RangePartitionResolver.resolveForRead, relativo alla mancata restituzione di collegamenti a causa di un concatenamento non valido dei risultati.

###<a name="1.5.5"/>1.5.5</a>

- Corretto hashParitionResolver resolveForRead(): quando la mancata indicazione di una chiave di partizione generava un'eccezione, invece di restituire un elenco di tutti i collegamenti registrati.

###<a name="1.5.4"/>1.5.4</a>

- Correzione del problema [n. 100](https://github.com/Azure/azure-documentdb-node/issues/100) relativo all'agente HTTPS dedicato: evitare di modificare l'agente globale per gli scopi di DocumentDB. Usare un agente dedicato per tutte le richieste della libreria.

###<a name="1.5.3"/>1.5.3</a>

- Correzione del problema [n. 81](https://github.com/Azure/azure-documentdb-node/issues/81): gestione corretta dei trattini negli ID dei file multimediali.

###<a name="1.5.2"/>1.5.2</a>

- Correzione del problema [n. 95](https://github.com/Azure/azure-documentdb-node/issues/95): avviso di perdita del listener EventEmitter.

###<a name="1.5.1"/>1.5.1</a>

- Correzione del problema [n. 92](https://github.com/Azure/azure-documentdb-node/issues/90): ridenominazione della cartella Hash in hash per i sistemi con distinzione tra maiuscole e minuscole.

### <a name="1.5.0"/>1.5.0</a>

- Implementazione del supporto per il partizionamento orizzontale mediante l'aggiunta di resolver della partizione a intervalli e hash.

### <a name="1.4.0"/>1.4.0</a>

- Implementazione di Upsert. Nuovi metodi upsertXXX in documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Saltata per riallineare il numero di versione con altri SDK.

### <a name="1.2.2"/>1.2.2</a>

- Divisione del wrapper Q promise per il nuovo repository
- Aggiornamento del file del pacchetto del Registro di sistema npm

### <a name="1.2.1"/>1.2.1</a>

- Implementazione del routing basato su ID
- Correzione del problema [n. 49](https://github.com/Azure/azure-documentdb-node/issues/49): conflitto tra la proprietà current e il metodo current().

### <a name="1.2.0"/>1.2.0</a>

- Aggiunta del supporto per l'indice GeoSpatial
- Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, &#47;&#47; o terminare con uno spazio.
- Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementazione del criterio di indicizzazione V2.

### <a name="1.0.3"/>1.0.3</a>

- Problema [n. 40](https://github.com/Azure/azure-documentdb-node/issues/40): implementazione delle configurazioni eslint e grunt nell'SDK core e promise.

### <a name="1.0.2"/>1.0.2</a>

- Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45): il wrapper promise non include l'intestazione con errore

### <a name="1.0.1"/>1.0.1</a>

- Implementazione della possibilità di eseguire query per i conflitti aggiungendo readConflicts, readConflictAsync e queryConflicts
- Aggiornamento della documentazione relativa alle API
- Problema [n. 41](https://github.com/Azure/azure-documentdb-node/issues/41): errore client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>

- SDK con disponibilità generale.

## Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB con un SDK ritirato verrà rifiutata dal servizio.

> [AZURE.WARNING]
Tutte le versioni dell'SDK per Node.js di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.

<br/>

| Versione | Data di rilascio | Data di ritiro 
| ---	  | ---	         | ---
| [1\.9.0](#1.9.0) | 07 luglio 2016 |--- 
| [1\.8.0](#1.8.0) | 14 giugno 2016 |--- 
| [1\.7.0](#1.7.0) | 26 aprile 2016 |--- 
| [1\.6.0](#1.6.0) | 29 marzo 2016 |--- 
| [1\.5.6](#1.5.6) | 08 marzo 2016 |--- 
| [1\.5.5](#1.5.5) | 02 febbraio 2016 |--- 
| [1\.5.4](#1.5.4) | 01 febbraio 2016 |--- 
| [1\.5.2](#1.5.2) | 26 gennaio 2016 |--- 
| [1\.5.2](#1.5.2) | 22 gennaio 2016 |--- 
| [1\.5.1](#1.5.1) | 4 gennaio 2016 |--- 
| [1\.5.0](#1.5.0) | 31 dicembre 2015 |--- 
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
| 0.9.4 - versione non definitiva | 06 aprile 2015 | 29 febbraio 2016 
| 0.9.3 - versione non definitiva | 14 gennaio 2015 | 29 febbraio 2016 
| 0.9.2 - versione non definitiva | 18 dicembre 2014 | 29 febbraio 2016 
| 0.9.1 - versione non definitiva | 22 agosto 2014 | 29 febbraio 2016 
| 0.9.0 - versione non definitiva | 21 agosto 2014 | 29 febbraio 2016


## Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche

Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->