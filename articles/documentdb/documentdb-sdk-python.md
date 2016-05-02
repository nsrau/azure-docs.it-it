<properties 
	pageTitle="SDK per Python di DocumentDB| Microsoft Azure" 
	description="Altre informazioni sulle date di rilascio e le date di ritiro dell'SDK per Python e sulle modifiche apportate tra le versioni dell'SDK per Python di DocumentDB." 
	services="documentdb" 
	documentationCenter="python" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="rnagpal"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [SDK per Java](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##SDK per Python di DocumentDB

<table>
<tr><td>**Download**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Contributi**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Documentazione**</td><td>[Documentazione di riferimento per Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Introduzione**</td><td>[Introduzione a Python SDK](documentdb-python-application.md)</td></tr>
<tr><td>**Piattaforma attualmente supportata**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Note sulla versione

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correzioni di bug relativi al partizionamento lato server per consentire caratteri speciali nel percorso partitionkey.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- [Raccolte partizionate](documentdb-partition-data.md) e [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md) implementati. 

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
- Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Supporta l'indice geospaziale.
- Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \\ o terminare con uno spazio.
- Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementa criteri di indicizzazione V2

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Supporta la connessione proxy

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- SDK con disponibilità generale

## Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB tramite un SDK ritirato verrà rifiutata dal servizio.

> [AZURE.WARNING]
Tutte le versioni dell'SDK per Python di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.

<br/>

| Versione | Data di rilascio | Data di ritiro 
| ---	  | ---	         | ---
| [1\.6.1](#1.6.1) | 08 aprile 2016 |---
| [1\.6.0](#1.6.0) | 29 marzo 2016 |---
| [1\.5.0](#1.5.0) | 03 gennaio 2016 |---
| [1\.4.2](#1.4.2) | 06 ottobre 2015 |---
| [1\.4.1](#1.4.1) | 06 ottobre 2015 |---
| [1\.2.0](#1.2.0) | 06 agosto 2015 |---
| [1\.1.0](#1.1.0) | 09 luglio 2015 |---
| [1\.0.1](#1.0.1) | 25 maggio 2015 |---
| [1\.0.0](#1.0.0) | 07 aprile 2015 |---
| 0.9.4-versione non definitiva | 14 gennaio 2015 | 29 febbraio 2016
| 0.9.3-versione non definitiva | 09 dicembre 2014 | 29 febbraio 2016
| 0.9.2-versione non definitiva | 25 novembre 2014 | 29 febbraio 2016
| 0.9.1-versione non definitiva | 23 settembre 2014 | 29 febbraio 2016
| 0.9.0-versione non definitiva | 21 agosto 2014 | 29 febbraio 2016

## Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche

Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0420_2016-->