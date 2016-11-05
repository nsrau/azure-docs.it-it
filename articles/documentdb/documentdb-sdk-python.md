---
title: SDK e API per Python di DocumentDB| Microsoft Docs
description: Altre informazioni sulle date di rilascio e le date di ritiro dell'SDK e dell'API per Python e sulle modifiche apportate tra le versioni dell'SDK per Python di DocumentDB.
services: documentdb
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 08/09/2016
ms.author: rnagpal

---
# SDK e API di DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## SDK e API per Python di DocumentDB
<table>  

<tr><td>**Scaricare l'SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Python API reference documentation (Documentazione di riferimento per l'API Python)](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Istruzioni per l'installazione dell'SDK**</td><td>[Python SDK installation instructions (Istruzioni per l'installazione di Python SDK)](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione a Python SDK](documentdb-python-application.md)</td></tr>

<tr><td>**Piattaforma attualmente supportata**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Note sulla versione
### <a name="1.9.0"/>[1\.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, DocumentDB esegue nove tentativi per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il tempo di RetryAfter nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Ora, DocumentDB attende al massimo 30 secondi per ciascuna richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* DocumentDB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il conteggio dei tentativi di limitazione di ripetere conteggio e il tempo cumulativo di attesa della richiesta tra i tentativi.
* Rimozione della classe RetryPolicy e della proprietà corrispondente (retry\_policy) esposta nella classe document\_client e introduzione di una classe RetryOptions che espone la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l’override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="1.8.0"/>[1\.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
* Aggiunta del supporto per gli account di database con più aree.

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
* Correzioni di bug relativi al partizionamento lato server per consentire caratteri speciali nel percorso partitionkey.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
* Implementazione delle [raccolte partizionate](documentdb-partition-data.md) e dei [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md).

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
* Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
* Supporta l'indice geospaziale.
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \\ o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
* Implementazione del criterio di indicizzazione V2.

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
* Supporto della connessione proxy.

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
* SDK con disponibilità generale.

## Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB con un SDK ritirato verrà rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni dell'SDK per Python di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.
> 
> 

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1\.9.0](#1.9.0) |07 luglio 2016 |--- |
| [1\.8.0](#1.8.0) |14 giugno 2016 |--- |
| [1\.7.0](#1.7.0) |26 aprile 2016 |--- |
| [1\.6.1](#1.6.1) |08 aprile 2016 |--- |
| [1\.6.0](#1.6.0) |29 marzo 2016 |--- |
| [1\.5.0](#1.5.0) |03 gennaio 2016 |--- |
| [1\.4.2](#1.4.2) |06 ottobre 2015 |--- |
| [1\.4.1](#1.4.1) |06 ottobre 2015 |--- |
| [1\.2.0](#1.2.0) |06 agosto 2015 |--- |
| [1\.1.0](#1.1.0) |09 luglio 2015 |--- |
| [1\.0.1](#1.0.1) |25 maggio 2015 |--- |
| [1\.0.0](#1.0.0) |07 aprile 2015 |--- |
| 0.9.4 - versione non definitiva |14 gennaio 2015 |29 febbraio 2016 |
| 0.9.3 - versione non definitiva |09 dicembre 2014 |29 febbraio 2016 |
| 0.9.2 - versione non definitiva |25 novembre 2014 |29 febbraio 2016 |
| 0.9.1 - versione non definitiva |23 settembre 2014 |29 febbraio 2016 |
| 0.9.0 - versione non definitiva |21 agosto 2014 |29 febbraio 2016 |

## Domande frequenti
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche
Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->