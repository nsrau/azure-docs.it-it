---
title: 'Azure Cosmos DB: API Python, risorse e SDK per SQL | Microsoft Docs'
description: Informazioni complete sull'SDK e sull'API Python per SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Python SDK.
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0be360b5fdc6b562dc7aa6af2b8395b0f910c2d0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Python SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Download dell'SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento delle API di Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Istruzioni per l'installazione dell'SDK**</td><td>[Istruzioni per l'installazione dell'SDK di Python](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione all'SDK di Python](sql-api-python-application.md)</td></tr>

<tr><td>**Piattaforma attualmente supportata**</td><td>[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione
### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Documentazione aggiornata con riferimento a Azure Cosmos DB anziché Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Questa versione dell'SDK richiede la versione più recente dell'emulatore di Azure Cosmos DB che è possibile scaricare dalla pagina https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Correzione di bug per il dizionario di aggregazione.
* Correzione di bug per la rimozione di barre iniziali nel collegamento a una risorsa.
* Aggiunti test per la codifica Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta di un'opzione per disabilitare la verifica SSL durante l'esecuzione sull'emulatore Cosmos DB.
* Rimossa la restrizione per cui il modulo delle richieste dipendenti deve essere esattamente 2.10.0.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Aggiunta del supporto per l'abilitazione della registrazione degli script durante l'esecuzione di stored procedure.
* Versione API REST incrementata a "2017-01-19" con questo rilascio.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Modifiche editoriali ai commenti alla documentazione.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Aggiunto il supporto per Python 3.5.
* Aggiunto il supporto per il pool di connessioni con modulo di richiesta.
* Aggiunto il supporto per la coerenza di sessione.
* Aggiunto il supporto per le query TOP/ORDERBY per le raccolte partizionate.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, Cosmos DB esegue nove tentativi per ogni richiesta con codice di errore 429, rispettando l'intervallo di tempo di retryAfter specificato nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Azure Cosmos DB attende al massimo 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* Cosmos DB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il numero di nuovi tentativi di limitazione e il tempo cumulativo di attesa della richiesta tra i tentativi.
* Rimozione della classe RetryPolicy e della proprietà corrispondente (retry_policy) esposta nella classe document_client e introduzione di una classe RetryOptions che espone la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l’override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Aggiunta del supporto per gli account di database con più aree.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Correzioni di bug relativi al partizionamento lato server per consentire caratteri speciali nel percorso partitionkey.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Supporta l'indice geospaziale.
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \, o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementazione del criterio di indicizzazione V2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Supporto della connessione proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato viene rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni dell'SDK per Python di Azure SQL precedenti alla versione **1.0.0** sono state ritirate il **29 febbraio 2016**. 
> 
> 

<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.3.1](#2.3.1) |21 dicembre 2017 |--- |
| [2.3.0](#2.3.0) |10 novembre 2017 |--- |
| [2.2.1](#2.2.1) |29 settembre 2017 |--- |
| [2.2.0](#2.2.0) |10 maggio 2017 |--- |
| [2.1.0](#2.1.0) |01 maggio 2017 |--- |
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
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche 
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

