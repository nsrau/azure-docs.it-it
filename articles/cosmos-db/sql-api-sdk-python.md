---
title: 'Database di Azure Cosmos: API di Python SQL, SDK & risorse'
description: Informazioni complete sull'SDK e sull'API Python per SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: b81a3921ec11d589dadbdebd698ab9ad67d7649c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982906"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Python SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed di modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Esecutore in blocco - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Download dell'SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentazione API**|[Documentazione di riferimento delle API di Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Istruzioni per l'installazione dell'SDK**|[Istruzioni per l'installazione dell'SDK di Python](https://github.com/Azure/azure-cosmos-python)|
|**Contribuire all'SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Introduzione**|[Introduzione all'SDK di Python](sql-api-python-application.md)|
|**Piattaforma attualmente supportata**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Note sulla versione

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Aggiunta del supporto del tipo di dati MultiPolygon
* Correzione di bug nei criteri di ripetizione di lettura della sessione
* Correzione di bug relativo al riempimento non corretto durante la decodifica di stringhe in base 64

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Correzione di bug in LocationCache
* Correzione di bug nella logica di ripetizione degli endpoint
* Correzione della documentazione

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Supporto per le scritture in più aree.
* Spazio dei nomi modificato in azure.cosmos.
* Concetti di raccolta e documento rinominati in contenitore e item, document_client rinominato cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Aggiunta del supporto per proxy
* Aggiunta del supporto per il feed di modifiche in lettura
* Aggiunta del supporto per l'intestazione delle quote delle raccolte
* Correzione di bug relativi ai token di sessioni di grandi dimensioni
* Correzione di bug relativi all'API ReadMedia
* Correzione di bug nella cache dell'intervallo di chiavi di partizione

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Aggiunta del supporto per nuovi tentativi predefiniti in caso di problemi di connessione.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Documentazione aggiornata con riferimento a Azure Cosmos DB anziché Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Questa versione SDK richiede la versione più recente di [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Correzione di bug per il dizionario di aggregazione.
* Correzione di bug per la rimozione di barre iniziali nel collegamento a una risorsa.
* Aggiunti test per la codifica Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta un'opzione per disabilitare la verifica TLS durante l'esecuzione su Cosmos DB Emulator.
* Rimossa la restrizione per cui il modulo delle richieste dipendenti deve essere esattamente 2.10.0.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Aggiunta del supporto per l'abilitazione della registrazione degli script durante l'esecuzione di stored procedure.
* Versione API REST incrementata a "2017-01-19" con questo rilascio.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Modifiche editoriali ai commenti alla documentazione.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Aggiunto il supporto per Python 3.5.
* Aggiunto il supporto per il pool di connessioni con modulo di richiesta.
* Aggiunto il supporto per la coerenza di sessione.
* Aggiunto il supporto per le query TOP/ORDERBY per le raccolte partizionate.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate Le richieste limitate ricevono un'eccezione di richiesta troppo grande, codice di errore 429. Per impostazione predefinita, Azure Cosmos DB riprova nove volte per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il retryAfter tempo nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Azure Cosmos DB attende al massimo 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* Cosmos DB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il numero di nuovi tentativi di limitazione e il tempo cumulativo di attesa della richiesta tra i tentativi.
* Rimozione della classe RetryPolicy e della proprietà corrispondente (retry_policy) esposta nella classe document_client e introduzione di una classe RetryOptions che espone la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l’override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Aggiunta del supporto per gli account di database con più aree.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Correzioni di bug relativi al partizionamento lato server per consentire caratteri speciali nel percorso partitionkey.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Supporta l'indice geospaziale.
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \, o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementazione del criterio di indicizzazione V2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Supporto della connessione proxy.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato viene rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni di Python SDK per l'API SQL precedenti alla versione **1.0.0** sono state ritirate il **29 febbraio 2016.** 
> 
> 

> [!WARNING]
> Tutte le versioni 1.x e 2.x dell'API Python SDK for SQL verranno ritirate il **30 agosto 2020.** 
> 
> 

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 novembre 2018 |--- |
| [3.0.1](#3.0.1) |4 ottobre 2018 |--- |
| [2.3.3](#2.3.3) |8 settembre 2018 |30 agosto 2020 |
| [2.3.2](#2.3.2) |8 maggio 2018 |30 agosto 2020 |
| [2.3.1](#2.3.1) |21 dicembre 2017 |30 agosto 2020 |
| [2.3.0](#2.3.0) |10 novembre 2017 |30 agosto 2020 |
| [2.2.1](#2.2.1) |29 settembre 2017 |30 agosto 2020 |
| [2.2.0](#2.2.0) |10 maggio 2017 |30 agosto 2020 |
| [2.1.0](#2.1.0) |01 maggio 2017 |30 agosto 2020 |
| [2.0.1](#2.0.1) |30 ottobre 2016 |30 agosto 2020 |
| [2.0.0](#2.0.0) |29 settembre 2016 |30 agosto 2020 |
| [1.9.0](#1.9.0) |07 luglio 2016 |30 agosto 2020 |
| [1.8.0](#1.8.0) |14 giugno 2016 |30 agosto 2020 |
| [1.7.0](#1.7.0) |26 aprile 2016 |30 agosto 2020 |
| [1.6.1](#1.6.1) |08 aprile 2016 |30 agosto 2020 |
| [1.6.0](#1.6.0) |29 marzo 2016 |30 agosto 2020 |
| [1.5.0](#1.5.0) |03 gennaio 2016 |30 agosto 2020 |
| [1.4.2](#1.4.2) |06 ottobre 2015 |30 agosto 2020 |
| 1.4.1 |06 ottobre 2015 |30 agosto 2020 |
| [1.2.0](#1.2.0) |06 agosto 2015 |30 agosto 2020 |
| [1.1.0](#1.1.0) |09 luglio 2015 |30 agosto 2020 |
| [1.0.1](#1.0.1) |25 maggio 2015 |30 agosto 2020 |
| [1.0.0](#1.0.0) |07 aprile 2015 |30 agosto 2020 |
| 0.9.4-prelease |14 gennaio 2015 |29 febbraio 2016 |
| 0.9.3-prelease |09 dicembre 2014 |29 febbraio 2016 |
| 0.9.2-prelease |25 novembre 2014 |29 febbraio 2016 |
| 0.9.1-prelease |23 settembre 2014 |29 febbraio 2016 |
| 0.9.0-prelease |21 agosto 2014 |29 febbraio 2016 |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

