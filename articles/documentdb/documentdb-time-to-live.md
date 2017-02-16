---
title: Impostare come scaduti i dati in DocumentDB usando la durata (TTL) | Documentazione Microsoft
description: "Con l&quot;impostazione TTL, Microsoft Azure DocumentDB offre la possibilità di eliminare automaticamente i documenti dal sistema dopo un periodo di tempo determinato."
services: documentdb
documentationcenter: 
keywords: Durata (TTL)
author: arramac
manager: jhubbard
editor: 
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 1ad5307054dbd860f9c65db4b82ea5f560a554c8
ms.openlocfilehash: 14a06dd20547f2910b2321372b27d9f777e54cc7


---
# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Impostare automaticamente come scaduti i dati nelle raccolte DocumentDB usando la durata (TTL)
Le applicazioni posso produrre e archiviare grandi quantità di dati. Alcuni di questi, come i dati eventi generati da computer, i registri e le informazioni sulle sessioni utente sono utili per un periodo di tempo limitato. Quando i dati eccedono le esigenze dell'applicazione è possibile eliminarli e ridurre le risorse di archiviazione necessarie per l'applicazione.

Con l'impostazione della durata (TTL), Microsoft Azure DocumentDB offre la possibilità di eliminare automaticamente i documenti dal database dopo un periodo di tempo determinato. La durata predefinita può essere impostata a livello di raccolta ed è possibile eseguirne l'override in base ai singoli documenti. Quando il valore di TTL è impostato, come impostazione predefinita di una raccolta o a livello di documento, DocumentDB rimuove automaticamente i documenti esistenti dopo un numero di secondi dall'ultima modifica pari a tale valore.

In DocumentDB la durata usa la differenza dall'ora dell'ultima modifica al documento. A tale scopo usa il campo `_ts` che esiste in ogni documento. Il campo _ts è un timestamp Epoch di tipo Unix che rappresenta la data e l'ora e il campo `_ts` viene aggiornato ogni volta che si modifica un documento. 

## <a name="ttl-behavior"></a>Comportamento di TTL
La funzionalità TTL è controllata dalle proprietà TTL a livello di raccolta e a livello di documento. I valori sono espressi in secondi e vengono trattati come differenziale dal `_ts` dell'ultima modifica al documento.

1. DefaultTTL per la raccolta:
   
   * Se non è presente o è impostata su Null, i documenti non vengono eliminati automaticamente.
   * Se è presente e il valore è "-1" = infinito, i documenti non scadono per impostazione predefinita.
   * Se è presente e il valore è un numero "n", i documenti scadono "n" secondi dopo l'ultima modifica.
2. TTL per i documenti: 
   
   * La proprietà è applicabile solo se DefaultTTL è presente per la raccolta padre.
   * Esegue l'override del valore di DefaultTTL per la raccolta padre.

Nel momento in cui è scade (`ttl` + `_ts` >= ora corrente del server), il documento viene contrassegnato come "scaduto". Da questo momento sui documenti scaduti non è possibile eseguire alcuna operazione e vengono esclusi dai risultati delle query. I documenti vengono eliminati fisicamente dal sistema e vengono eliminati in background in base alle esigenze in un secondo momento. L'operazione non utilizza le [unità richiesta (UR)](documentdb-request-units.md) del budget della raccolta.

La logica precedente può essere illustrata in questa matrice:

|  | DefaultTTL mancante o non impostata nella raccolta | DefaultTTL = -1 nella raccolta | DefaultTTL = n nella raccolta |
| --- |:--- |:--- |:--- |
| TTL mancante nel documento |Non viene eseguito l'override a livello di documento, perché sia il documento che la raccolta sono privi di TTL. |I documenti in questa raccolta non scadono. |I documenti nella raccolta scadono al termine dell'intervallo n. |
| Durata TTL = -1 nel documento |Non viene eseguito l'override a livello di documento, perché la raccolta non definisce la proprietà DefaultTTL di cui è possibile eseguire l'override a livello di documento. L'impostazione TTL a livello di documento non viene interpretata dal sistema. |I documenti in questa raccolta non scadono. |Il documento con TTL =-1 in questa raccolta non scade. Tutti gli altri documenti scadono dopo l'intervallo di tempo n. |
| TTL = n nel documento |Non viene eseguito l'override a livello di documento. L'impostazione TTL a livello di documento non viene interpretata dal sistema. |Il documento con TTL = n scade dopo l'intervallo di tempo n, espresso in secondi. Gli altri documenti ereditano l'intervallo -1 e non scadono. |Il documento con TTL = n scade dopo l'intervallo di tempo n, espresso in secondi. Gli altri documenti ereditano l'intervallo n dalla raccolta. |

## <a name="configuring-ttl"></a>Configurazione di TTL
Per impostazione predefinita, la durata è disabilitata in tutte le raccolte DocumentDB e in tutti i documenti.

## <a name="enabling-ttl"></a>Abilitazione di TTL
Per abilitare la durata (TTL) in una raccolta o nei documenti all'interno di una raccolta, è necessario impostare la proprietà DefaultTTL della raccolta su -1 o su un numero positivo diverso da zero. Quando DefaultTTL = -1, per impostazione predefinita tutti i documenti nella raccolta hanno durata infinita. Il servizio DocumentDB deve tuttavia monitorare i documenti per cui viene eseguito l'override di questa impostazione predefinita nella raccolta.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Configurazione del valore TTL predefinito in una raccolta
È possibile configurare una durata predefinita a livello di raccolta. Per impostare la durata (TTL) in una raccolta, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo, espresso in secondi, per la scadenza di tutti i documenti nella raccolta dopo l'ultimo timestamp di modifica del documento (`_ts`). In alternativa, è possibile impostare il valore predefinito su -1, per fare in modo che tutti i documenti inseriti nella raccolta abbiano una durata illimitata per impostazione predefinita.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Impostazione di TTL in un documento
Oltre al valore TTL predefinito in una raccolta è possibile impostare una durata (TTL) specifica a livello di documento. Questa impostazione esegue l'override dell'impostazione predefinita della raccolta.

* Per impostare la durata (TTL) in un documento, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo, espresso in secondi, per la scadenza del documento dopo l'ultimo timestamp di modifica del documento (`_ts`).
* Se il documento non ha il campo TTL, viene applicata l'impostazione predefinita della raccolta.
* Se la funzionalità TTL è disabilitata a livello di raccolta, il campo TTL del documento viene ignorato finché non viene abilitata nuovamente la funzionalità TTL per la raccolta.

Di seguito è riportato un frammento di codice che illustra come impostare la scadenza della durata (TTL) su un documento:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Estensione di TTL in un documento esistente
Per reimpostare il valore TTL in un documento è possibile eseguire una qualsiasi operazione di scrittura nel documento. L'operazione imposta `_ts` sull'ora corrente e fa ripartire il conto alla rovescia per la scadenza del documento, data dal valore di `ttl`. Per modificare il valore `ttl` di un documento, è possibile aggiornare il campo come si fa con qualsiasi altro campo impostabile.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Rimozione di TTL da un documento
Se è stato impostato un valore TTL per un documento ma si preferisce non farlo scadere, è possibile recuperare il documento, rimuovere il campo TTL e sostituire il documento nel server. Quando il campo TTL viene rimosso dal documento, viene applicata l'impostazione predefinita della raccolta. Per impedire la scadenza di un documento e fare in modo che non erediti dalla raccolta, è necessario impostare il valore TTL su -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Disabilitazione di TTL
Per disabilitare del tutto la durata (TTL) in una raccolta e impedire al processo in background di cercare documenti scaduti, è necessario eliminare la proprietà DefaultTTL dalla raccolta. Eliminare questa proprietà non equivale a impostarla su -1. Se la proprietà è impostata su -1, i nuovi documenti aggiunti alla raccolta non hanno scadenza ma è possibile eseguire l'override dell'impostazione per documenti specifici nella raccolta. Se la proprietà viene rimossa dalla raccolta, nessuno dei documenti ha una scadenza, anche se sono presenti documenti con override esplicito di una impostazione predefinita precedente.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);


## <a name="faq"></a>Domande frequenti
**Quanto costa la durata (TTL)?**

Non sono previsti costi aggiuntivi per l'impostazione di una durata (TTL) in un documento.

**Quanto tempo è necessario per eliminare il documento dopo la scadenza?**

I documenti scadono immediatamente dopo la scadenza e non sarà possibile accedervi usando operazioni CRUD o API di query. 

**La durata (TTL) impostata per un documento influisce sugli addebiti delle unità richiesta?**

No, gli addebiti delle unità richiesta non risentono delle eliminazioni di documenti scaduti con TTL in DocumentDB.

**La funzionalità TTL si applica solo all'intero documento o è possibile impostare come scaduti singoli valori delle proprietà di un documento?**

La funzionalità TTL si applica all'intero documento. Per impostare come scaduta solo una parte di un documento, è consigliabile estrarla dal documento principale, inserirla in un documento "collegato" separato e usare la funzionalità TTL sul documento estratto.

**La funzionalità TTL ha requisiti di indicizzazione specifici?**

Sì. I [criteri di indicizzazione](documentdb-indexing-policies.md) della raccolta devono essere impostati su Coerente o Differita. Il tentativo di impostare DefaultTTL in una raccolta la cui indicizzazione è impostata su None genera un errore, come anche il tentativo di disabilitare l'indicizzazione in una raccolta in cui la proprietà DefaultTTL è già impostata.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Azure DocumentDB, vedere la pagina della [*documentazione*](https://azure.microsoft.com/documentation/services/documentdb/) del servizio.




<!--HONumber=Jan17_HO3-->


