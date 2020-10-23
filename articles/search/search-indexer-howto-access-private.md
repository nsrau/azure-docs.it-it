---
title: Connessioni dell'indicizzatore tramite un endpoint privato
titleSuffix: Azure Cognitive Search
description: Configurare le connessioni dell'indicizzatore per accedere al contenuto da altre risorse di Azure protette tramite un endpoint privato.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: ff8aa6688d8a838fa2e06d2eef546025cdd9213f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340054"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Creare connessioni dell'indicizzatore tramite un endpoint privato

Molte risorse di Azure, ad esempio gli account di archiviazione di Azure, possono essere configurate in modo da accettare le connessioni da un elenco di reti virtuali e rifiutare le connessioni esterne che provengono da una rete pubblica. Se si usa un indicizzatore per indicizzare i dati in Azure ricerca cognitiva e l'origine dati si trova in una rete privata, è possibile creare una [connessione all'endpoint privato](../private-link/private-endpoint-overview.md) in uscita per raggiungere i dati.

Questo metodo di connessione dell'indicizzatore è soggetto ai due requisiti seguenti:

+ La risorsa di Azure che fornisce contenuto o codice deve essere registrata in precedenza con il [servizio di collegamento privato di Azure](https://azure.microsoft.com/services/private-link/).

+ Il servizio Azure ricerca cognitiva deve trovarsi nel livello Basic o superiore. La funzionalità non è disponibile nel livello gratuito. Inoltre, se l'indicizzatore ha un numero di competenze, il livello deve essere standard 2 (S2) o superiore. Per altre informazioni, vedere [limiti del servizio](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>API di gestione delle risorse di collegamento privato condivise

Gli endpoint privati delle risorse protette create tramite le API di Azure ricerca cognitiva vengono definiti risorse di *collegamento privato condiviso*. Questo perché si sta "condividendo" l'accesso a una risorsa, ad esempio un account di archiviazione, che è stato integrato con il [servizio di collegamento privato di Azure](https://azure.microsoft.com/services/private-link/).

Tramite l'API REST di gestione, Azure ricerca cognitiva fornisce un'operazione [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) che è possibile usare per configurare l'accesso da un indicizzatore di Azure ricerca cognitiva.

È possibile creare connessioni di endpoint privati ad alcune risorse solo usando la versione di anteprima dell'API di gestione di ricerca (versione *2020-08-01-Preview* o successiva), che è designata come *Anteprima* nella tabella seguente. Le risorse senza una designazione di *Anteprima* possono essere create con la versione di anteprima o API disponibile a livello generale (*2020-08-01* o versione successiva).

La tabella seguente elenca le risorse di Azure per le quali è possibile creare endpoint privati in uscita da Azure ricerca cognitiva. Per creare una risorsa di collegamento privato condiviso, immettere i valori **ID del gruppo** esattamente come sono scritti nell'API. con l'esatta distinzione tra maiuscole e minuscole.

| Risorsa di Azure | ID gruppo |
| --- | --- |
| Archiviazione di Azure-BLOB (o) ADLS gen 2 | `blob`|
| Archiviazione di Azure-tabelle | `table`|
| API Azure Cosmos DB-SQL | `Sql`|
| database SQL di Azure | `sqlServer`|
| Database di Azure per MySQL (anteprima) | `mysqlServer`|
| Insieme di credenziali chiave di Azure | `vault` |
| Funzioni di Azure (anteprima) | `sites` |

È anche possibile eseguire una query sulle risorse di Azure per le quali le connessioni agli endpoint privati in uscita sono supportate usando l' [elenco delle API supportate](/rest/api/searchmanagement/privatelinkresources/listsupported).

Nella parte restante di questo articolo viene usata una combinazione di API [ARMClient](https://github.com/projectkudu/ARMClient) e [postazione](https://www.postman.com/) per illustrare le chiamate all'API REST.

> [!NOTE]
> Gli esempi in questo articolo si basano sui presupposti seguenti:
> * Il nome del servizio di ricerca è _Contoso-Search_, che esiste nel gruppo di risorse _Contoso_ di una sottoscrizione con ID sottoscrizione _00000000-0000-0000-0000-000000000000_. 
> * L'ID risorsa di questo servizio di ricerca è _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.search/searchServices/contoso-Search_.

Il resto degli esempi Mostra come è possibile configurare il servizio _Contoso-Search_ in modo che gli indicizzatori possano accedere ai dati dall'account di archiviazione protetto _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.storage/storageAccounts/contoso-storage_.

## <a name="secure-your-storage-account"></a>Proteggere l'account di archiviazione

Configurare l'account di archiviazione per [consentire l'accesso solo da subnet specifiche](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Nel portale di Azure, se si seleziona questa opzione e si lascia vuoto il set, significa che non è consentito alcun traffico dalle reti virtuali.

   ![Screenshot del riquadro "firewall e reti virtuali", che mostra l'opzione per consentire l'accesso alle reti selezionate. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> È possibile usare l' [approccio di servizio Microsoft attendibile](../storage/common/storage-network-security.md#trusted-microsoft-services) per ignorare le restrizioni di rete virtuale o IP in un account di archiviazione. È anche possibile abilitare il servizio di ricerca per accedere ai dati nell'account di archiviazione. A tale scopo, vedere [indicizzatore accesso ad archiviazione di Azure con l'eccezione del servizio attendibile](search-indexer-howto-access-trusted-service-exception.md). 
>
> Tuttavia, quando si usa questo approccio, la comunicazione tra ricerca cognitiva di Azure e l'account di archiviazione avviene tramite l'indirizzo IP pubblico dell'account di archiviazione, tramite la rete dorsale Microsoft sicura.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Passaggio 1: creare una risorsa di collegamento privato condiviso nell'account di archiviazione

Per richiedere ad Azure ricerca cognitiva di creare una connessione all'endpoint privato in uscita con l'account di archiviazione, effettuare la chiamata API seguente: 

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Il contenuto del *create-pe.jsnel* file, che rappresenta il corpo della richiesta all'API, è il seguente:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`Viene restituita una risposta in seguito all'esito positivo. Il processo di creazione di un endpoint privato in uscita è un'operazione con esecuzione prolungata (asincrona). Comporta la distribuzione delle risorse seguenti:

* Un endpoint privato, allocato con un indirizzo IP privato in uno `"Pending"` stato. L'indirizzo IP privato viene ottenuto dallo spazio degli indirizzi allocato alla rete virtuale dell'ambiente di esecuzione per l'indicizzatore privato specifico del servizio di ricerca. Dopo l'approvazione dell'endpoint privato, qualsiasi comunicazione da Azure ricerca cognitiva all'account di archiviazione ha origine dall'indirizzo IP privato e da un canale di collegamento privato protetto.

* Zona DNS privata per il tipo di risorsa, in base a `groupId` . Distribuendo questa risorsa, si garantisce che qualsiasi ricerca DNS per la risorsa privata utilizzerà l'indirizzo IP associato all'endpoint privato.

Assicurarsi di specificare il corretto `groupId` per il tipo di risorsa per cui si sta creando l'endpoint privato. Eventuali mancate corrispondenze comporteranno un messaggio di risposta non riuscito.

Come in tutte le operazioni asincrone di Azure, la `PUT` chiamata restituisce un `Azure-AsyncOperation` valore di intestazione simile al seguente:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

È possibile eseguire periodicamente il polling di questo URI per ottenere lo stato dell'operazione. Prima di procedere, è consigliabile attendere che lo stato dell'operazione della risorsa collegamento privato condiviso abbia raggiunto uno stato terminale, ovvero che lo stato dell'operazione sia *succeeded*.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Passaggio 2a: approvare la connessione all'endpoint privato per l'account di archiviazione

> [!NOTE]
> In questa sezione si usa il portale di Azure per esaminare il flusso di approvazione di un endpoint privato per l'archiviazione. In alternativa, è possibile usare l' [API REST](/rest/api/storagerp/privateendpointconnections) disponibile tramite il provider di risorse di archiviazione.
>
> Altri provider, ad esempio Azure Cosmos DB o Azure SQL Server, offrono API del provider di risorse di archiviazione simili per la gestione delle connessioni agli endpoint privati.

1. Nella portale di Azure selezionare la scheda **connessioni endpoint privato** dell'account di archiviazione. Una volta completata l'operazione asincrona, deve essere richiesta una connessione all'endpoint privato con il messaggio di richiesta della chiamata API precedente.

   ![Screenshot del portale di Azure, che mostra il riquadro "connessioni a endpoint privati".](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Selezionare l'endpoint privato creato da Azure ricerca cognitiva. Nella colonna **endpoint privato** identificare la connessione all'endpoint privato in base al nome specificato nell'API precedente, selezionare **approva**, quindi immettere un messaggio appropriato. Il contenuto del messaggio non è significativo. 

   Assicurarsi che la connessione all'endpoint privato venga visualizzata come illustrato nello screenshot seguente. Potrebbero essere necessari da uno a due minuti per aggiornare lo stato nel portale.

   ![Screenshot del portale di Azure, che mostra uno stato "approvato" nel riquadro "connessioni a endpoint privati".](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Una volta approvata la *richiesta di connessione* all'endpoint privato, il traffico può propagarsi attraverso l'endpoint privato. Dopo l'approvazione dell'endpoint privato, Azure ricerca cognitiva crea i mapping della zona DNS necessari nella zona DNS creata per l'endpoint.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Passaggio 2b: eseguire una query sullo stato della risorsa collegamento privato condiviso

Per confermare che la risorsa di collegamento privato condiviso è stata aggiornata dopo l'approvazione, ottenerne lo stato tramite l' [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Se la proprietà `properties.provisioningState` della risorsa è `Succeeded` e `properties.status` è `Approved` , significa che la risorsa collegamento privato condiviso è funzionante e l'indicizzatore può essere configurato per comunicare tramite l'endpoint privato.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Passaggio 3: configurare l'indicizzatore per l'esecuzione nell'ambiente privato

> [!NOTE]
> È possibile eseguire questo passaggio prima che la connessione all'endpoint privato venga approvata. Fino a quando non viene approvata la connessione all'endpoint privato, qualsiasi indicizzatore che tenti di comunicare con una risorsa protetta, ad esempio l'account di archiviazione, finirà in uno stato di errore temporaneo. Non sarà possibile creare nuovi indicizzatori. Non appena viene approvata la connessione all'endpoint privato, gli indicizzatori possono accedere all'account di archiviazione privato.

1. [Creare un'origine dati](/rest/api/searchservice/create-data-source) che punti all'account di archiviazione protetto e un contenitore appropriato nell'account di archiviazione. La schermata seguente mostra la richiesta in post.

   ![Screenshot che illustra la creazione di un'origine dati nell'interfaccia utente di un post.](media\search-indexer-howto-secure-access\create-ds.png )

1. Analogamente, [creare un indice](/rest/api/searchservice/create-index) e, facoltativamente, [creare un skillt](/rest/api/searchservice/create-skillset) usando l'API REST.

1. [Creare un indicizzatore](/rest/api/searchservice/create-indexer) che punti all'origine dati, all'indice e alle competenze create nel passaggio precedente. Inoltre, forzare l'esecuzione dell'indicizzatore nell'ambiente di esecuzione privata impostando la `executionEnvironment` proprietà di configurazione dell'indicizzatore su `private` .

   ![Screenshot che illustra la creazione di un indicizzatore nell'interfaccia utente di un utente.](media\search-indexer-howto-secure-access\create-idr.png)

   Dopo che l'indicizzatore è stato creato correttamente, deve iniziare a indicizzare il contenuto dall'account di archiviazione tramite la connessione all'endpoint privato. È possibile monitorare lo stato dell'indicizzatore usando l' [API dello stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Se sono già presenti indicizzatori, è possibile aggiornarli tramite l' [API put](/rest/api/searchservice/create-indexer) impostando `executionEnvironment` su `private` .

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Se la creazione dell'indicizzatore ha esito negativo con un messaggio di errore simile a "le credenziali dell'origine dati non sono valide", significa che lo stato della connessione all'endpoint privato non è ancora *approvato* o che la connessione non è funzionante. Per risolvere il problema: 
  * Ottenere lo stato della risorsa di collegamento privato condiviso usando l' [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get). Se lo stato è *approvato*, controllare l'oggetto `properties.provisioningState` della risorsa. Se lo stato è `Incomplete` , ciò significa che non è stato possibile configurare alcune dipendenze sottostanti per la risorsa. La `PUT` rigenerazione della richiesta per ricreare la risorsa di collegamento privato condiviso dovrebbe risolvere il problema. Potrebbe essere necessaria una riapprovazione. Controllare nuovamente lo stato della risorsa per verificare che il problema sia stato risolto.

- Se si crea l'indicizzatore senza impostare la relativa `executionEnvironment` proprietà, la creazione potrebbe avere esito positivo, ma la cronologia di esecuzione indicherà che l'esecuzione dell'indicizzatore non è riuscita. Per risolvere il problema:
   * [Aggiornare l'indicizzatore](/rest/api/searchservice/update-indexer) per specificare l'ambiente di esecuzione.

- Se è stato creato l'indicizzatore senza impostare la `executionEnvironment` proprietà e l'esecuzione è stata completata, significa che Azure ricerca cognitiva ha deciso che l'ambiente di esecuzione è l'ambiente *privato* specifico del servizio di ricerca. Questo può cambiare, a seconda delle risorse utilizzate dall'indicizzatore, del carico sul servizio di ricerca e di altri fattori e può avere esito negativo in un secondo momento. Per risolvere il problema:
  * Si consiglia vivamente di impostare la `executionEnvironment` proprietà su `private` per assicurarsi che non abbia esito negativo in futuro.

Le [quote e i limiti](search-limits-quotas-capacity.md) determinano il numero di risorse di collegamento privato condivise che possono essere create e dipendono dallo SKU del servizio di ricerca.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli endpoint privati:

- [Che cosa sono gli endpoint privati?](../private-link/private-endpoint-overview.md)
- [Configurazioni DNS necessarie per gli endpoint privati](../private-link/private-endpoint-dns.md)
