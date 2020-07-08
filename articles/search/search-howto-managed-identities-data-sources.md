---
title: Configurare una connessione a un'origine dati usando un'identità gestita (anteprima)
titleSuffix: Azure Cognitive Search
description: Informazioni su come configurare una connessione dell'indicizzatore a un'origine dati usando un'identità gestita (anteprima)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 48b94b8cd047f62ea13bf4e062254088ea11840e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663599"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity-preview"></a>Configurare una connessione dell'indicizzatore a un'origine dati usando un'identità gestita (anteprima)

> [!IMPORTANT] 
> Il supporto per la configurazione di una connessione a un'origine dati tramite un'identità gestita è attualmente disponibile in un'anteprima pubblica gestita. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> È possibile richiedere l'accesso all'anteprima compilando [questo modulo](https://aka.ms/azure-cognitive-search/mi-preview-request).

Un [indicizzatore](search-indexer-overview.md) in Ricerca cognitiva di Azure è un crawler che fornisce un modo per eseguire il pull dei dati dall'origine dati in Ricerca cognitiva di Azure. Un indicizzatore ottiene una connessione all'origine dati dall'oggetto origine dati creato. L'oggetto origine dati in genere include le credenziali per l'origine dati di destinazione. Ad esempio, l'oggetto origine dati potrebbe includere una chiave dell'account di archiviazione di Azure se si vuole indicizzare i dati da un contenitore di archiviazione BLOB.

In molti casi, fornire le credenziali direttamente nell'oggetto origine dati non costituisce un problema, ma è necessario tenere conto di alcune problematiche:
* Com'è possibile proteggere le credenziali nel codice che crea l'oggetto origine dati?
* Se la chiave o la password dell'account è compromessa e deve essere modificata, è necessario aggiornare gli oggetti origine dati con la nuova chiave o password dell'account, in modo che l'indicizzatore possa connettersi di nuovo all'origine dati.

Questi problemi possono essere risolti impostando la connessione tramite un'identità gestita.

## <a name="using-managed-identities"></a>Uso di identità gestite

Le [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) sono una funzionalità che fornisce ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa funzionalità in Ricerca cognitiva di Azure per creare un oggetto origine dati con una stringa di connessione che non include credenziali. Al servizio di ricerca verrà invece concesso l'accesso all'origine dati tramite il controllo degli accessi in base al ruolo.

Quando si configura un'origine dati usando un'identità gestita, è possibile modificare le credenziali dell'origine dati e gli indicizzatori saranno comunque in grado di connettersi all'origine dati. È anche possibile creare oggetti origine dati nel codice senza dover includere una chiave dell'account o usare Key Vault per recuperare una chiave dell'account.

## <a name="limitations"></a>Limitazioni

Le origini dati seguenti supportano la configurazione di una connessione dell'indicizzatore mediante identità gestite. 

* [Archiviazione BLOB di Azure, Azure Data Lake Storage Gen2 (anteprima), Archiviazione tabelle di Azure](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Database SQL di Azure](search-howto-managed-identities-sql.md)

Le funzionalità seguenti attualmente non supportano l'uso di identità gestite per la configurazione della connessione:
* Knowledge Store
* Competenze personalizzate
 
## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come configurare una connessione dell'indicizzatore usando le identità gestite:

* [Archiviazione BLOB di Azure, Azure Data Lake Storage Gen2 (anteprima), Archiviazione tabelle di Azure](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Database SQL di Azure](search-howto-managed-identities-sql.md)