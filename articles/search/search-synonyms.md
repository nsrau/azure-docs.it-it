---
pageTitle: Synonyms in Azure Search (preview) | Microsoft Docs
description: "Documentazione preliminare per la funzionalità relativa ai sinonimi (anteprima) esposta nell'API REST di Ricerca di Azure."
services: search
documentationCenter: 
authors: mhko
manager: pablocas
editor: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/07/2016
ms.author: nateko
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 739a0ad77c68ea74ec25bc80c7539ac8b3f18201
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017

---
# <a name="synonyms-in-azure-search-preview"></a>Sinonimi in Ricerca di Azure (anteprima)

La funzionalità relativa ai sinonimi nei motori di ricerca associa termini equivalenti, che espandono in modo implicito l'ambito di una query, senza che l'utente debba fornire effettivamente il termine. Ad esempio, dato il termine "cane" e le associazioni sinonimiche "canino" e "cucciolo", tutti i documenti contenenti "cane", "canino" o "cucciolo" saranno inclusi nella query.

In Ricerca di Azure l'espansione sinonimica viene eseguita in fase di query. È possibile aggiungere mappe sinonimiche a un servizio senza compromettere le operazioni esistenti. La proprietà **synonymMaps** può essere aggiunta alla definizione di un campo senza dover ricompilare l'indice. Per altre informazioni, vedere [Aggiornare un indice](https://docs.microsoft.com/rest/api/searchservice/update-index) .

## <a name="feature-availability"></a>Disponibilità delle funzionalità

La funzionalità relativa ai sinonimi è attualmente disponibile in anteprima ed è supportata solo nelle versioni API di anteprima più recenti (api-version=2016-09-01-Preview). Non è attualmente disponibile alcun supporto nel portale di Azure. Poiché la versione dell'API è specificata nella richiesta, è possibile combinare API disponibili a livello generale (GA) e di anteprima nella stessa applicazione. Le API disponibili in anteprima non rientrano nel Contratto di servizio e le funzionalità disponibili in anteprima possono subire modifiche, quindi non è consigliabile usarle nelle applicazioni di produzione.

## <a name="how-to-use-synonyms-in-azure-search"></a>Come usare i sinonimi in Ricerca di Azure

In Ricerca di Azure il supporto dei sinonimi si basa sulle mappe sinonimiche definite e caricate nel servizio. Queste mappe costituiscono una risorsa indipendente (ad esempio indici o origini dati) e possono essere utilizzate da qualsiasi campo ricercabile in qualsiasi indice nel servizio di ricerca.

Le mappe sinonimiche e gli indici vengono mantenuti in modo indipendente. Dopo aver definito una mappa sinonimica e averla caricata nel servizio, è possibile abilitare la funzionalità relativa ai sinonimi per un campo tramite l'aggiunta della nuova proprietà **synonymMaps** nella definizione del campo. La creazione, l'aggiornamento e l'eliminazione di una mappa sinonimica è sempre un'operazione che riguarda l'intero documento, perciò non è possibile creare, aggiornare o eliminare parti della mappa sinonimica in modo incrementale. Se si aggiorna anche una singola voce è necessario ripetere il caricamento.

L'aggiunta di sinonimi in un'applicazione di ricerca è una procedura in due passaggi:

1.  Aggiungere una mappa sinonimica al servizio di ricerca tramite le API indicate di seguito.  

2.  Configurare un campo ricercabile per l'uso della mappa sinonimica nella definizione dell'indice.

### <a name="synonymmaps-resource-apis"></a>API di risorsa SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Aggiungere o aggiornare una mappa sinonimica nel servizio tramite POST o PUT.

Le mappe sinonimiche vengono caricate nel servizio tramite POST o PUT. Ogni regola deve essere delimitata dal carattere nuova riga ('\n'). È possibile definire fino a 5.000 regole per ogni mappa sinonimica in un servizio gratuito e 10.000 regole in tutti gli altri SKU. Ogni regola può avere fino a 20 espansioni.

In questa anteprima le mappe sinonimiche devono essere nel formato Apache Solr descritto di seguito. Se si ha un dizionario di sinonimi in un formato diverso e si desidera usarlo direttamente, segnalarlo mediante [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

È possibile creare una nuova mappa sinonimica usando HTTP POST, come nell'esempio seguente:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

In alternativa è possibile usare PUT e specificare il nome della mappa sinonimica nell'URI. Se la mappa sinonimica non esiste, verrà creata.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato dei sinonimi Apache Solr

Il formato Solr supporta il mapping sinonimico equivalente ed esplicito. Le regole del mapping aderiscono alla specifica di filtro dei sinonimi open source di Apache Solr descritta in questo documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Di seguito è riportata una regola di esempio per sinonimi equivalenti.
```
              USA, United States, United States of America
```

Con la regola precedente, la query di ricerca "USA" si espanderà in "USA" OR "Stati Uniti" OR "Stati Uniti d'America".

Il mapping esplicito è indicato da una freccia "=>". Quando è specificato, una sequenza di termini di una query di ricerca che corrisponde al lato sinistro di "= >" verrà sostituita con le alternative sul lato destro. Data la regola seguente, le query di ricerca "Washington", "Wash." o "WA" saranno riscritte tutte come "WA". Il mapping esplicito si applica solo nella direzione specificata e, in questo caso, non riscrivere la query "WA" come "Washington".
```
              Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Elencare le mappe sinonimiche del proprio servizio.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Aggiungere una mappa sinonimica al proprio servizio.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Eliminare una mappa sinonimica dal proprio servizio.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configurare un campo ricercabile per l'uso della mappa sinonimica nella definizione dell'indice.

La nuova proprietà di campo **synonymMaps** consente di specificare una mappa sinonimica da usare per un campo ricercabile. Le mappe sinonimiche sono risorse a livello di servizio e possono essere referenziate da qualsiasi campo di un indice del servizio.

    POST https://[servicename].search.windows.net/indexes?api-version=2016-09-01-Preview
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

È possibile specificare **synonymMaps** per i campi ricercabili di tipo "Edm. String" o "Collection".

> [!NOTE]
> In questa versione di anteprima è possibile avere solo una mappa sinonimica per campo. Se si vuole usare più mappe sinonimiche, segnalarlo tramite [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impatto dei sinonimi sulle altre funzionalità di ricerca

La funzionalità relativa ai sinonimi riscrive la query originale con sinonimi usando l'operatore OR. Per questo motivo, l'evidenziazione dei risultati e i profili di punteggio trattano il termine originale e i sinonimi come equivalenti.

La funzionalità relativa ai sinonimi si applica alle query di ricerca e non ai filtri o ai facet. Analogamente, i suggerimenti sono basati solo sul termine originale; le corrispondenze sinonimiche non compaiono nella risposta.

Le espansioni dei sinonimi non si applicano ai termini di ricerca con caratteri jolly; i termini con prefisso, fuzzy e regex non vengono espansi.

## <a name="tips-for-building-a-synonym-map"></a>Suggerimenti per la creazione di una mappa sinonimica

- Una mappa sinonimica concisa e ben progettata è più efficiente rispetto a un elenco completo delle possibili corrispondenze. I dizionari eccessivamente grandi o complessi richiedono più tempo di analisi e influenzano la latenza della query se la query si espande in molti sinonimi. Invece di indovinare quali termini potrebbero essere usati, è possibile ottenere i termini effettivi tramite un [report di analisi del traffico di ricerca](search-traffic-analytics.md).

- Come esercizio sia preliminare che di convalida, abilitare e usare questo report per determinare con precisione quali termini trarranno vantaggio da una corrispondenza sinonimica e quindi continuare a usarlo per verificare che la mappa sinonimica sta generando risultati migliori. Nel report predefinito il riquadro delle query di ricerca più comuni e il riquadro delle query di ricerca senza risultati forniranno le informazioni necessarie.

- È possibile creare più mappe sinonimiche per l'applicazione di ricerca (ad esempio in base alla lingua se l'applicazione supporta clienti multilingue). Attualmente un campo può usarne una sola. È possibile aggiornare la proprietà synonymMaps di un campo in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

- Se si ha un indice esistente in un ambiente di sviluppo (non di produzione), provare con un piccolo dizionario per vedere come l'aggiunta di sinonimi cambia l'esperienza di ricerca, compreso l'impatto sui profili di punteggio, l'evidenziazione dei risultati e i suggerimenti.

- [Abilitare l'analisi del traffico di ricerca](search-traffic-analytics.md) e usare i report predefiniti di Power BI per ottenere informazioni su quali termini sono più utilizzati e quali non restituiscono documenti. In base a queste informazioni, modificare il dizionario per includere i sinonimi per le query improduttive che devono risolversi in documenti nell'indice.

