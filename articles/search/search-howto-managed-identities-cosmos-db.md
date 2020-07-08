---
title: Configurare una connessione a un account Cosmos DB usando un'identità gestita (anteprima)
titleSuffix: Azure Cognitive Search
description: Informazioni su come configurare una connessione dell'indicizzatore a un account Cosmos DB usando un'identità gestita (anteprima)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: c67a5537a74e37473280fbd44fa47c65f2a37806
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563154"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Configurare una connessione dell'indicizzatore a un database Cosmos DB usando un'identità gestita (anteprima)

> [!IMPORTANT] 
> Il supporto per la configurazione di una connessione a un'origine dati tramite un'identità gestita è attualmente disponibile in un'anteprima pubblica gestita. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> È possibile richiedere l'accesso all'anteprima compilando [questo modulo](https://aka.ms/azure-cognitive-search/mi-preview-request).

Questa pagina descrive come configurare una connessione dell'indicizzatore a un database Azure Cosmos DB usando un'identità gestita invece di immettere le credenziali nella stringa di connessione dell'oggetto origine dati.

Prima di acquisire maggiore familiarità con questa funzionalità, è consigliabile sapere che cos’è un indicizzatore e come lo si configura per l'origine dati. Altre informazioni sono disponibili nei collegamenti seguenti:
* [Panoramica degli indicizzatori](search-indexer-overview.md)
* [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurare una connessione usando un'identità gestita

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Attivare l'identità gestita assegnata dal sistema

Quando un'identità gestita assegnata dal sistema è abilitata, Azure crea un'identità per il servizio di ricerca che può essere usata per eseguire l'autenticazione ad altri servizi di Azure all'interno dello stesso tenant e della stessa sottoscrizione. È quindi possibile usare questa identità nelle assegnazioni di controllo degli accessi in base al ruolo che consentono l'accesso ai dati durante l'indicizzazione.

![Attivare l'identità gestita assegnata dal sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Attivare l'identità gestita assegnata dal sistema")

Dopo aver selezionato **Salva**, viene visualizzato un ID oggetto che è stato assegnato al servizio di ricerca.

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

Un'**origine dati** specifica i dati per l'indice, le credenziali e i criteri per l'identificazione delle modifiche apportate ai dati (ad esempio documenti modificati o eliminati nella raccolta). L'origine dati è definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Quando si usano le identità gestite per eseguire l'autenticazione all'origine dati, le **credenziali** non includono una chiave dell'account.

Esempio di come creare un oggetto origine dati Cosmos DB usando l'[API REST ](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

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
|**credentials** | Obbligatorio. <br/><br/>Quando ci si connette usando un'identità gestita, il formato delle **credenziali** si presenta come segue: *Database=[nome-database];ResourceId=[stringa-id-risorsa];(ApiKind=[tipo-api];)*<br/> <br/>Il formato di ResourceId: *ResourceId=/subscriptions/**ID sottoscrizione**/resourceGroups/**nome gruppo risorse**/providers/Microsoft.DocumentDB/databaseAccounts/**nome account cosmos db**/;*<br/><br/>Per le raccolte SQL, la stringa di connessione non necessita di un ApiKind.<br/><br/>Per le raccolte MongoDB, aggiungere **ApiKind=MongoDb** alla stringa di connessione. <br/><br/>Per i grafici Gremlin e le tabelle Apache Cassandra, iscriversi all’[anteprima dell'indicizzatore gestito](https://aka.ms/azure-cognitive-search/indexer-preview) per ottenere l'accesso all'anteprima e le informazioni sul formato delle credenziali.<br/>|
| **container** | Contiene gli elementi seguenti: <br/>**name**: Obbligatorio. Specificare l'ID della raccolta di database da indicizzare.<br/>**query**: Facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che possa essere indicizzato da Ricerca cognitiva di Azure.<br/>Per le API MongoDB, Gremlin e Apache Cassandra, le query non sono supportate. |
| **dataChangeDetectionPolicy** | Consigliato |
|**dataDeletionDetectionPolicy** | Facoltativo |

Il portale di Azure e il [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) supportano anche la stringa di connessione di identità gestite. Il portale di Azure richiede un flag di funzionalità che verrà indicato all'utente durante l'iscrizione all'anteprima usando il collegamento riportato nella parte superiore di questa pagina. 

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

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Creare un indicizzatore

Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e pianifica l’automatizzazione dell'aggiornamento dei dati.

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore.

Definizione di un indicizzatore di esempio:

    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Questo indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Vedere anche

Per altre informazioni sugli indicizzatori Cosmos DB:
* [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
