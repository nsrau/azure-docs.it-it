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
ms.openlocfilehash: b81d3f74c20f42620ceeae08bec5d484909377a7
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167475"
---
# <a name="indexer-connections-through-a-private-endpoint-azure-cognitive-search"></a>Connessioni di indicizzatore tramite un endpoint privato (Azure ricerca cognitiva)

Molte risorse di Azure, ad esempio gli account di archiviazione di Azure, possono essere configurate in modo da accettare le connessioni da un elenco specifico di reti virtuali e rifiutare le connessioni esterne che provengono da una rete pubblica. Se si usa un indicizzatore per indicizzare i dati in Azure ricerca cognitiva e l'origine dati si trova in una rete privata, è possibile creare una [connessione all'endpoint privato](../private-link/private-endpoint-overview.md) (in uscita) per raggiungere i dati.

Per usare questo metodo di connessione dell'indicizzatore, esistono due requisiti:

+ La risorsa di Azure che fornisce contenuto o codice deve essere registrata in precedenza con il [servizio di collegamento privato di Azure](https://azure.microsoft.com/services/private-link/).

+ Il servizio ricerca cognitiva di Azure deve essere di base o superiore (non disponibile sul livello gratuito). Inoltre, se l'indicizzatore ha un numero di competenze, il livello deve essere standard 2 (S2) o superiore. Per altre informazioni, vedere [limiti del servizio](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>API di gestione delle risorse di collegamento privato condivise

Gli endpoint privati delle risorse protette create tramite le API di Azure ricerca cognitiva vengono definiti risorse di *collegamento privato condiviso* perché l'accesso a una risorsa, ad esempio un account di archiviazione, è stato caricato nel [servizio di collegamento privato di Azure](https://azure.microsoft.com/services/private-link/).

Tramite l'API REST di gestione, Azure ricerca cognitiva fornisce un'operazione [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) che è possibile usare per configurare l'accesso da un indicizzatore di Azure ricerca cognitiva.

È possibile creare connessioni di endpoint privati ad alcune risorse solo con la versione di anteprima dell'API di gestione di ricerca ( `2020-08-01-Preview` o versioni successive), indicata con il tag "Preview" nella tabella seguente. Le risorse senza tag "Preview" possono essere create usando l'anteprima o la versione API disponibile a livello generale ( `2020-08-01` o versioni successive).

Di seguito è riportato l'elenco delle risorse di Azure a cui è possibile creare endpoint privati in uscita da Azure ricerca cognitiva. I `groupId` valori elencati nella tabella seguente devono essere usati esattamente come scritti (con distinzione tra maiuscole e minuscole) nell'API per creare una risorsa di collegamento privato condivisa.

| Risorsa di Azure | ID gruppo |
| --- | --- |
| Archiviazione di Azure-BLOB (o) ADLS gen 2 | `blob`|
| Archiviazione di Azure-tabelle | `table`|
| API Azure Cosmos DB-SQL | `Sql`|
| database SQL di Azure | `sqlServer`|
| Database di Azure per MySQL (anteprima) | `mysqlServer`|
| Insieme di credenziali chiave di Azure | `vault` |
| Funzioni di Azure (anteprima) | `sites` |

È anche possibile eseguire una query sull'elenco delle risorse di Azure per cui sono supportate le connessioni agli endpoint privati in uscita usando l' [API di elenco supportata](/rest/api/searchmanagement/privatelinkresources/listsupported).

Nella parte restante di questo articolo viene usata una combinazione di [ARMClient](https://github.com/projectkudu/ARMClient) e [postazione](https://www.postman.com/) per illustrare le chiamate all'API REST.

> [!NOTE]
> In questo articolo si presuppone che il nome del servizio di ricerca sia __Contoso-Search__ esistente nel gruppo di risorse __Contoso__ di una sottoscrizione con ID sottoscrizione __00000000-0000-0000-0000-000000000000__. L'ID risorsa di questo servizio di ricerca sarà `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

Il resto degli esempi mostrerà come è possibile configurare il servizio __Contoso-Search__ in modo che gli indicizzatori possano accedere ai dati dall'account di archiviazione protetto `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Protezione dell'account di archiviazione

Configurare l'account di archiviazione per [consentire l'accesso solo da subnet specifiche](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Nel portale di Azure, se si seleziona questa opzione e si lascia vuoto il set, significa che non è consentito alcun traffico da alcuna rete virtuale.

   ![Accesso alla rete virtuale](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Accesso alla rete virtuale")

> [!NOTE]
> L' [approccio di servizio Microsoft attendibile](../storage/common/storage-network-security.md#trusted-microsoft-services) può essere usato per ignorare le restrizioni di rete virtuale o IP in un account di archiviazione di questo tipo e può consentire al servizio di ricerca di accedere ai dati nell'account di archiviazione, come descritto in [indicizzatore accesso ad archiviazione di Azure con l'eccezione del servizio attendibile ](search-indexer-howto-access-trusted-service-exception.md). Tuttavia, quando si usa questo approccio, la comunicazione tra ricerca cognitiva di Azure e l'account di archiviazione avviene tramite l'indirizzo IP pubblico dell'account di archiviazione, tramite la rete dorsale Microsoft sicura.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Passaggio 1: creare una risorsa di collegamento privato condiviso nell'account di archiviazione

Eseguire la chiamata API seguente per richiedere ricerca cognitiva di Azure per creare una connessione all'endpoint privato in uscita all'account di archiviazione

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Il contenuto del `create-pe.json` file (che rappresenta il corpo della richiesta all'API) è il seguente:

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

`202 Accepted`Viene restituita una risposta in seguito alla riuscita. il processo di creazione di un endpoint privato in uscita è un'operazione a esecuzione prolungata (asincrona). Comporta la distribuzione delle risorse seguenti:

1. Un endpoint privato allocato con un indirizzo IP privato in uno `"Pending"` stato. L'indirizzo IP privato viene ottenuto dallo spazio degli indirizzi allocato alla rete virtuale dell'ambiente di esecuzione dell'indicizzatore privato specifico del servizio di ricerca. Dopo l'approvazione dell'endpoint privato, qualsiasi comunicazione da Azure ricerca cognitiva all'account di archiviazione ha origine dall'indirizzo IP privato e da un canale di collegamento privato protetto.
2. Zona DNS privata per il tipo di risorsa, in base a `groupId` . In questo modo, qualsiasi ricerca DNS per la risorsa privata utilizzerà l'indirizzo IP associato all'endpoint privato.

Assicurarsi di specificare il corretto `groupId` per il tipo di risorsa per cui si sta creando l'endpoint privato. Eventuali mancate corrispondenze comporteranno un messaggio di risposta non riuscito.

Come tutte le operazioni asincrone di Azure, la `PUT` chiamata restituisce un `Azure-AsyncOperation` valore di intestazione che sarà simile al seguente:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

È possibile eseguire il polling di questo URI periodicamente per ottenere lo stato dell'operazione. Prima di procedere, è consigliabile attendere finché lo stato dell'operazione relativa alla risorsa di collegamento privato condiviso non raggiunge uno stato terminale (ovvero `succeeded` ).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Passaggio 2a: approvare la connessione all'endpoint privato per l'account di archiviazione

> [!NOTE]
> Questa sezione USA portale di Azure per esaminare il flusso di approvazione di un endpoint privato per l'archiviazione. È anche possibile usare l' [API REST](/rest/api/storagerp/privateendpointconnections) disponibile tramite il provider di risorse di archiviazione (RP).
>
> Altri provider, ad esempio CosmosDB, Azure SQL Server e così via, offrono anche API RP simili per gestire le connessioni agli endpoint privati.

Passare alla scheda "**connessioni a endpoint privati**" dell'account di archiviazione in portale di Azure. Deve essere presente una richiesta per una connessione all'endpoint privato, con il messaggio di richiesta della chiamata API precedente (dopo che l'operazione asincrona ha __avuto esito positivo__).

   ![Approvazione dell'endpoint privato](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Approvazione dell'endpoint privato")

Selezionare l'endpoint privato creato da Azure ricerca cognitiva (usare la colonna "endpoint privato" per identificare la connessione all'endpoint privato con il nome specificato nell'API precedente) e scegliere "approva", con un messaggio appropriato (il messaggio non è significativo). Verificare che la connessione all'endpoint privato venga visualizzata nel modo seguente (è possibile da 1-2 minuti per aggiornare lo stato nel portale)

![Approvato endpoint privato](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Approvato endpoint privato")

Una volta approvata la richiesta di connessione all'endpoint privato, significa che il traffico è *in grado* di scorrere l'endpoint privato. Dopo che l'endpoint privato è stato approvato, Azure ricerca cognitiva creerà i mapping della zona DNS necessari nella zona DNS creata.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Passaggio 2b: eseguire una query sullo stato della risorsa collegamento privato condiviso

 Per confermare che la risorsa di collegamento privato condiviso è stata aggiornata dopo l'approvazione, ottenerne lo stato usando l' [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Se la proprietà `properties.provisioningState` della risorsa è `Succeeded` e `properties.status` è `Approved` , significa che la risorsa collegamento privato condiviso è funzionante e gli indicizzatori possono essere configurati per la comunicazione tramite l'endpoint privato.

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

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Passaggio 3: configurare l'indicizzatore per l'esecuzione nell'ambiente privato

> [!NOTE]
> Questo passaggio può essere eseguito anche prima che la connessione all'endpoint privato venga approvata. Fino a quando non viene approvata la connessione all'endpoint privato, qualsiasi indicizzatore che tenti di comunicare con una risorsa protetta, ad esempio l'account di archiviazione, finirà in uno stato di errore temporaneo. Non sarà possibile creare nuovi indicizzatori. Non appena viene approvata la connessione all'endpoint privato, gli indicizzatori saranno in grado di accedere all'account di archiviazione privato.

1. [Creare un'origine dati](/rest/api/searchservice/create-data-source) che punti all'account di archiviazione protetto e un contenitore appropriato nell'account di archiviazione. Di seguito viene illustrata la richiesta in post.
![Crea origine dati](media\search-indexer-howto-secure-access\create-ds.png "Creazione di origini dati")

1. Analogamente, [creare un indice](/rest/api/searchservice/create-index) e, facoltativamente, [creare un skillt](/rest/api/searchservice/create-skillset) usando l'API REST.

1. [Creare un indicizzatore](/rest/api/searchservice/create-indexer) che punti all'origine dei dati, all'indice e alle competenze create in precedenza. Inoltre, forzare l'esecuzione dell'indicizzatore nell'ambiente di esecuzione privato, impostando la proprietà di configurazione dell'indicizzatore `executionEnvironment` su `"Private"` .
![Creare un indicizzatore](media\search-indexer-howto-secure-access\create-idr.png "Creazione dell'indicizzatore")

L'indicizzatore deve essere creato correttamente e deve essere in corso l'indicizzazione del contenuto dell'account di archiviazione tramite la connessione all'endpoint privato. È possibile monitorare lo stato dell'indicizzatore usando l' [API dello stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Se sono già presenti indicizzatori, è possibile aggiornarli semplicemente tramite l' [API put](/rest/api/searchservice/create-indexer) per impostare `executionEnvironment` su `"Private"` .

## <a name="troubleshooting-issues"></a>Risoluzione dei problemi

- Quando si crea un indicizzatore, se la creazione ha esito negativo con un messaggio di errore simile a "le credenziali dell'origine dati non sono valide", significa che la connessione all'endpoint privato non è stata *approvata* o non è funzionante.
Ottenere lo stato della risorsa di collegamento privato condiviso usando l' [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get). Se è stato *approvato* , controllare la `properties.provisioningState` della risorsa. In tal caso `Incomplete` , significa che alcune delle dipendenze sottostanti per la risorsa non sono state sottoposte a provisioning: eseguire `PUT` nuovamente la richiesta per "ricreare" la risorsa di collegamento privata condivisa che dovrebbe risolvere il problema. Potrebbe essere necessaria una nuova approvazione: controllare lo stato della risorsa ancora una volta per verificarlo.
- Se l'indicizzatore viene creato senza impostarne `executionEnvironment` , la creazione dell'indicizzatore potrebbe avere esito positivo, ma la cronologia di esecuzione indicherà che l'esecuzione dell'indicizzatore non è riuscita. È necessario [aggiornare l'indicizzatore](/rest/api/searchservice/update-indexer) per specificare l'ambiente di esecuzione.
- Se l'indicizzatore viene creato senza impostare `executionEnvironment` e viene eseguito correttamente, significa che Azure ricerca cognitiva ha deciso che il relativo ambiente di esecuzione è l'ambiente "privato" specifico del servizio di ricerca. Tuttavia, questa modifica può variare in base a diversi fattori, ad esempio le risorse utilizzate dall'indicizzatore, il carico sul servizio di ricerca e così via, e può avere esito negativo in un secondo momento. si consiglia vivamente di impostare il `executionEnvironment` come `"Private"` per assicurarsi che non si verifichino errori in futuro.
- Le [quote e i limiti](search-limits-quotas-capacity.md) determinano il numero di risorse di collegamento privato condivise che possono essere create e dipendono dallo SKU del servizio di ricerca.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli endpoint privati:

- [Che cosa sono gli endpoint privati?](../private-link/private-endpoint-overview.md)
- [Configurazioni DNS necessarie per gli endpoint privati](../private-link/private-endpoint-dns.md)