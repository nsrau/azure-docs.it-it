---
title: Configurare una connessione a un account Cosmos DB usando un'identità gestita
titleSuffix: Azure Cognitive Search
description: Informazioni su come configurare una connessione dell'indicizzatore a un account Cosmos DB usando un'identità gestita
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422094"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Configurare una connessione dell'indicizzatore a un database Cosmos DB usando un'identità gestita

Questa pagina descrive come configurare una connessione dell'indicizzatore a un database Azure Cosmos DB usando un'identità gestita invece di immettere le credenziali nella stringa di connessione dell'oggetto origine dati.

Prima di acquisire maggiore familiarità con questa funzionalità, è consigliabile sapere che cos’è un indicizzatore e come lo si configura per l'origine dati. Altre informazioni sono disponibili nei collegamenti seguenti:

* [Panoramica degli indicizzatori](search-indexer-overview.md)
* [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurare una connessione usando un'identità gestita

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Attivare l'identità gestita assegnata dal sistema

Quando un'identità gestita assegnata dal sistema è abilitata, Azure crea un'identità per il servizio di ricerca che può essere usata per eseguire l'autenticazione ad altri servizi di Azure all'interno dello stesso tenant e della stessa sottoscrizione. È quindi possibile usare questa identità nelle assegnazioni di controllo degli accessi in base al ruolo di Azure (RBAC di Azure) che consentono l'accesso ai dati durante l'indicizzazione.

![Attivare l'identità gestita assegnata dal sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Attivare l'identità gestita assegnata dal sistema")

Dopo aver selezionato **Salva** , viene visualizzato un ID oggetto che è stato assegnato al servizio di ricerca.

![ID oggetto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID dell'oggetto.")
 
### <a name="2---add-a-role-assignment"></a>2 - Aggiungere un'assegnazione di ruolo

In questo passaggio si autorizza il servizio Ricerca cognitiva di Azure a leggere i dati nel database Cosmos DB.

1. Nella portale di Azure passare all'account Cosmos DB che contiene i dati che si intende indicizzare.
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi** e quindi **Aggiungi assegnazione di ruolo**.

    ![Aggiungi assegnazione di ruolo](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Aggiungi un'assegnazione di ruolo")

4. Selezionare **Ruolo Lettore dell'account Cosmos DB**.
5. Lasciare **Assegna accesso a** impostato su **Utente, gruppo o entità servizio di Azure AD**.
6. Cercare il servizio di ricerca di proprio interesse, selezionarlo e quindi selezionare **Salva**

    ![Aggiunta dell'assegnazione di ruolo di lettura e di accesso ai dati](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Aggiungere l'assegnazione di ruolo di lettura e di accesso ai dati")

### <a name="3---create-the-data-source"></a>3 - Creare l'origine dati

L' [API REST](/rest/api/searchservice/create-data-source), portale di Azure e [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) supportano la stringa di connessione dell'identità gestita. Di seguito è riportato un esempio di come creare un'origine dati per indicizzare i dati da Cosmos DB usando l' [API REST](/rest/api/searchservice/create-data-source) e una stringa di connessione identità gestita. Il formato della stringa di connessione identità gestita è lo stesso per l'API REST, .NET SDK e il portale di Azure.

Quando si usano identità gestite per l'autenticazione, le **credenziali** non includeranno una chiave dell'account.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Il corpo della richiesta contiene la definizione dell'origine dati, che deve includere i campi seguenti:

| Campo   | Descrizione |
|---------|-------------|
| **nome** | Obbligatorio. Scegliere un nome qualsiasi per rappresentare l’oggetto origine dati. |
|**type**| Obbligatorio. Deve essere `cosmosdb`. |
|**credentials** | Obbligatorio. <br/><br/>Quando ci si connette usando un'identità gestita, il formato delle **credenziali** si presenta come segue: *Database=[nome-database];ResourceId=[stringa-id-risorsa];(ApiKind=[tipo-api];)*<br/> <br/>Il formato di ResourceId: *ResourceId=/subscriptions/ **ID sottoscrizione** /resourceGroups/ **nome gruppo risorse** /providers/Microsoft.DocumentDB/databaseAccounts/ **nome account cosmos db** /;*<br/><br/>Per le raccolte SQL, la stringa di connessione non necessita di un ApiKind.<br/><br/>Per le raccolte MongoDB, aggiungere **ApiKind=MongoDb** alla stringa di connessione. <br/><br/>Per i grafici Gremlin e le tabelle Apache Cassandra, iscriversi all’[anteprima dell'indicizzatore gestito](https://aka.ms/azure-cognitive-search/indexer-preview) per ottenere l'accesso all'anteprima e le informazioni sul formato delle credenziali.<br/>|
| **container** | Contiene gli elementi seguenti: <br/>**name** : Obbligatorio. Specificare l'ID della raccolta di database da indicizzare.<br/>**query** : Facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che possa essere indicizzato da Ricerca cognitiva di Azure.<br/>Per le API MongoDB, Gremlin e Apache Cassandra, le query non sono supportate. |
| **dataChangeDetectionPolicy** | Consigliato |
|**dataDeletionDetectionPolicy** | Facoltativo |

### <a name="4---create-the-index"></a>4 - Creare l'indice

L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Ecco come creare un indice con un campo `booktitle` ricercabile:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Creare un indicizzatore

Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e pianifica l’automatizzazione dell'aggiornamento dei dati.

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore.

Definizione di un indicizzatore di esempio:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Questo indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se non si riesce a indicizzare i dati da Cosmos DB tenere presente quanto segue:

1. Se sono state recentemente ruotate le chiavi dell'account Cosmos DB, sarà necessario attendere fino a 15 minuti per il corretto funzionamento della stringa di connessione dell'identità gestita.

1. Verificare se per l'account Cosmos DB è stato limitato l'accesso alle reti Select. In caso contrario, fare riferimento all' [indicizzatore accesso al contenuto protetto dalle funzionalità di sicurezza di rete di Azure](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)