---
title: Configurare una connessione a un account di archiviazione usando un'identità gestita (anteprima)
titleSuffix: Azure Cognitive Search
description: Informazioni su come configurare una connessione dell'indicizzatore a un account di Archiviazione di Azure usando un'identità gestita (anteprima)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 1fa9581f1a7968a1bfd6df0fb82383dd45e70f54
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663179"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>Configurare una connessione a un account di Archiviazione di Azure usando un'identità gestita (anteprima)

> [!IMPORTANT] 
> Il supporto per la configurazione di una connessione a un'origine dati tramite un'identità gestita è attualmente disponibile in un'anteprima pubblica gestita. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> È possibile richiedere l'accesso all'anteprima compilando [questo modulo](https://aka.ms/azure-cognitive-search/mi-preview-request).

Questa pagina descrive come configurare una connessione dell'indicizzatore a un account di Archiviazione di Azure usando un'identità gestita invece di immettere le credenziali nella stringa di connessione dell'oggetto origine dati.

Prima di acquisire maggiore familiarità con questa funzionalità, è consigliabile sapere che cos'è un indicizzatore e come lo si configura per l'origine dati. Altre informazioni sono disponibili nei collegamenti seguenti:
* [Panoramica degli indicizzatori](search-indexer-overview.md)
* [Indicizzatore BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Indicizzatore di Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indicizzatore di tabelle di Azure](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Configurare la connessione

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Attivare l'identità gestita assegnata dal sistema

Quando un'identità gestita assegnata dal sistema è abilitata, Azure crea un'identità per il servizio di ricerca che può essere usata per eseguire l'autenticazione ad altri servizi di Azure all'interno dello stesso tenant e della stessa sottoscrizione. È quindi possibile usare questa identità nelle assegnazioni di controllo degli accessi in base al ruolo che consentono l'accesso ai dati durante l'indicizzazione.

![Attivare l'identità gestita assegnata dal sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Attivare l'identità gestita assegnata dal sistema")

Dopo aver selezionato **Salva**, viene visualizzato un ID oggetto che è stato assegnato al servizio di ricerca.

![ID oggetto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID dell'oggetto.")
 
### <a name="2---add-a-role-assignment"></a>2 - Aggiungere un'assegnazione di ruolo

In questo passaggio si autorizza il servizio Ricerca cognitiva di Azure a leggere i dati nell'account di archiviazione.

1. Nel portale di Azure passare all'account di archiviazione che contiene i dati che si intende indicizzare.
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi** e quindi **Aggiungi assegnazione di ruolo**.

    ![Aggiungi assegnazione di ruolo](./media/search-managed-identities/add-role-assignment-storage.png "Aggiungi un'assegnazione di ruolo")

4. Selezionare il ruolo o i ruoli appropriati in base al tipo di account di archiviazione da indicizzare:
    1. Per Archiviazione BLOB di Azure è necessario aggiungere il servizio di ricerca ai ruoli **Lettore e accesso ai dati** e **Lettore dei dati del BLOB di archiviazione**.
    1. Per Azure Data Lake Storage Gen2 è necessario aggiungere il servizio di ricerca ai ruoli **Lettore e accesso ai dati** e **Lettore dei dati del BLOB di archiviazione**.
    1. Per Archiviazione tabelle di Azure è necessario aggiungere il servizio di ricerca solo al ruolo **Lettore e accesso ai dati**.
5.  Lasciare **Assegna accesso a** impostato su **Utente, gruppo o entità servizio di Azure AD**
6.  Cercare il servizio di ricerca di proprio interesse, selezionarlo e quindi selezionare **Salva**

    ![Aggiungere l'assegnazione di ruolo Lettore e accesso ai dati](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Aggiungere l'assegnazione di ruolo Lettore e accesso ai dati")

Si noti che per la connessione ad Archiviazione BLOB di Azure e Azure Data Lake Storage Gen2, è necessario aggiungere anche l'assegnazione di ruolo **Lettore dei dati del BLOB di archiviazione**.

![Aggiungere l'assegnazione di ruolo Lettore dei dati del BLOB di archiviazione](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Aggiungere l'assegnazione di ruolo Lettore dei dati del BLOB di archiviazione")

### <a name="3---create-the-data-source"></a>3 - Creare l'origine dati

Quando si esegue l'indicizzazione da un account di archiviazione, l'origine dati deve avere le proprietà obbligatorie seguenti:

* **name** è il nome univoco dell'origine dati all'interno del servizio di ricerca.
* **type**
    * Archiviazione BLOB di Azure: `azureblob`
    * Archiviazione tabelle di Azure: `azuretable`
    * Azure Data Lake Storage Gen2: **type** verrà fornito dopo aver effettuato la registrazione per l'anteprima tramite [questo modulo](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **credentials**
    * Quando si usa un'identità gestita per l'autenticazione, il formato delle **credenziali** è diverso rispetto alla situazione in cui non viene usata un'identità gestita. In questo caso, si fornirà un valore ResourceId senza chiave o password dell'account. ResourceId deve includere l'ID sottoscrizione dell'account di archiviazione, il gruppo di risorse dell'account di archiviazione e il nome dell'account di archiviazione.
    * Formato dell'identità gestita: 
        * *ResourceId=/subscriptions/**ID sottoscrizione**/resourceGroups/**nome del gruppo di risorse**/providers/Microsoft.Storage/storageAccounts/**nome dell'account di archiviazione**/;*
* **container** specifica un nome di contenitore o di tabella nell'account di archiviazione. Per impostazione predefinita, tutti i BLOB all'interno del contenitore sono recuperabili. Per indicizzare soltanto i BLOB in una determinata directory virtuale, è possibile specificare la directory usando il parametro facoltativo **query**.

Esempio di creazione di un oggetto origine dati BLOB usando l'[API REST ](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

Il portale di Azure e [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) supportano anche la stringa di connessione di identità gestite. Il portale di Azure richiede un flag di funzionalità che verrà fornito all'utente durante la registrazione per l'anteprima usando il collegamento riportato nella parte superiore di questa pagina. 

### <a name="4---create-the-index"></a>4 - Creare l'indice

L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Di seguito viene illustrato come creare un indice con un campo `content` ricercabile per archiviare il testo estratto dagli oggetti BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Creare l'indicizzatore

Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e consente di pianificare l'automatizzazione dell'aggiornamento dei dati.

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore.

Esempio di definizione dell'indicizzatore per un indicizzatore BLOB:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Questo indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Vedere anche

Altre informazioni sugli indicizzatori di Archiviazione di Azure:
* [Indicizzatore BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Indicizzatore di Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indicizzatore di tabelle di Azure](search-howto-indexing-azure-tables.md)
