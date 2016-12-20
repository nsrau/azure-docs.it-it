---
title: SDK e API per Python di DocumentDB| Documentazione Microsoft
description: Altre informazioni sulle date di rilascio e le date di ritiro dell&quot;SDK e dell&quot;API per Python e sulle modifiche apportate tra le versioni dell&quot;SDK per Python di DocumentDB.
services: documentdb
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/30/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 5e182811adc29ae00d40355bb4813c30eb0b904c
ms.openlocfilehash: 6e2ab89f3895edcfb818ad918dfffd597f4905d3


---
# <a name="documentdb-apis-and-sdks"></a>SDK e API di DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-python-api-and-sdk"></a>SDK e API per Python di DocumentDB
<table>

<tr><td>**Download dell'SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento delle API di Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Istruzioni per l'installazione dell'SDK**</td><td>[Istruzioni per l'installazione dell'SDK di Python](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Attività iniziali**</td><td>[Introduzione all'SDK di Python](documentdb-python-application.md)</td></tr>

<tr><td>**Piattaforma attualmente supportata**</td><td>[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione
### <a name="a-name201201httpspypipythonorgpypipydocumentdb201"></a><a name="2.0.1"/>[2.0.1](https://pypi.python.org/pypi/pydocumentdb/2.0.1)
* Modifiche editoriali ai commenti alla documentazione.

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
* Aggiunto il supporto per Python 3.5.
* Aggiunto il supporto per il pool di connessioni con modulo di richiesta.
* Aggiunto il supporto per la coerenza di sessione.
* Aggiunto il supporto per le query TOP/ORDERBY per le raccolte partizionate.

### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, DocumentDB esegue nove tentativi per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il tempo di RetryAfter nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Ora, DocumentDB attende al massimo 30 secondi per ciascuna richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* DocumentDB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il conteggio dei tentativi di limitazione di ripetere conteggio e il tempo cumulativo di attesa della richiesta tra i tentativi.
* Rimozione della classe RetryPolicy e della proprietà corrispondente (retry_policy) esposta nella classe document_client e introduzione di una classe RetryOptions che espone la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l’override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
* Aggiunta del supporto per gli account di database con più aree.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
* Correzioni di bug relativi al partizionamento lato server per consentire caratteri speciali nel percorso partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
* Implementazione delle [raccolte partizionate](documentdb-partition-data.md) e dei [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
* Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
* Supporta l'indice geospaziale.
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \, o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
* Implementazione del criterio di indicizzazione V2.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
* Supporto della connessione proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
* SDK con disponibilità generale.

## <a name="release-retirement-dates"></a>Date di rilascio e di ritiro
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
| [2.0.1](#2.0.1) |30 ottobre 2016 |--- |
| [2.0.0](#2.0.0) |29 settembre 2016 |--- |
| [1.9.0](#1.9.0) |07 luglio 2016 |--- |
| [1.8.0](#1.8.0) |14 giugno 2016 |--- |
| [1.7.0](#1.7.0) |26 aprile 2016 |--- |
| [1.6.1](#1.6.1) |08 aprile 2016 |--- |
| [1.6.0](#1.6.0) |29 marzo 2016 |--- |
| [1.5.0](#1.5.0) |03 gennaio 2016 |--- |
| [1.4.2](#1.4.2) |06 ottobre 2015 |--- |
| [1.4.1](#1.4.1) |06 ottobre 2015 |--- |
| [1.2.0](#1.2.0) |06 agosto 2015 |--- |
| [1.1.0](#1.1.0) |09 luglio 2015 |--- |
| [1.0.1](#1.0.1) |25 maggio 2015 |--- |
| [1.0.0](#1.0.0) |07 aprile 2015 |--- |
| 0.9.4-prelease |14 gennaio 2015 |29 febbraio 2016 |
| 0.9.3-prelease |09 dicembre 2014 |29 febbraio 2016 |
| 0.9.2-prelease |25 novembre 2014 |29 febbraio 2016 |
| 0.9.1-prelease |23 settembre 2014 |29 febbraio 2016 |
| 0.9.0-prelease |21 agosto 2014 |29 febbraio 2016 |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 




<!--HONumber=Nov16_HO3-->


