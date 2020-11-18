---
title: Gestire e trovare i dati BLOB di Azure con i tag degli indici BLOB (anteprima)
description: Informazioni su come usare i tag degli indici BLOB per categorizzare, gestire ed eseguire query per oggetti BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 3174dbd36d9bb39ce606ec12f88397f795e91526
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832433"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Gestire e trovare i dati BLOB di Azure con i tag degli indici BLOB (anteprima)

Poiché i set di dati ottengono dimensioni maggiori, la ricerca di un oggetto specifico in un mare di dati può risultare difficile. I tag di indice BLOB forniscono funzionalità di gestione e individuazione dei dati tramite attributi di tag di indice chiave-valore. È possibile categorizzare e trovare oggetti all'interno di un singolo contenitore o in tutti i contenitori nell'account di archiviazione. Quando si modificano i requisiti dei dati, gli oggetti possono essere categorizzati dinamicamente aggiornando i tag degli indici. Gli oggetti possono rimanere sul posto con l'organizzazione del contenitore corrente.

I tag di indice BLOB consentono di:

- Categorizzare dinamicamente i BLOB usando i tag di indice chiave-valore
- Trovare rapidamente BLOB con tag specifici in un intero account di archiviazione
- Specificare i comportamenti condizionali per le API BLOB in base alla valutazione dei tag di indice
- Usare i tag di indice per controlli avanzati su funzionalità come la [gestione del ciclo](storage-lifecycle-management-concepts.md) di vita di BLOB

Si consideri uno scenario in cui sono presenti milioni di BLOB nell'account di archiviazione, a cui è possibile accedere da molte applicazioni diverse. Si desidera trovare tutti i dati correlati da un singolo progetto. Non si è certi dell'ambito perché i dati possono essere distribuiti tra più contenitori con convenzioni di denominazione diverse. Tuttavia, le applicazioni caricano tutti i dati con tag basati sul progetto. Anziché cercare in milioni di BLOB e confrontare nomi e proprietà, è possibile usare `Project = Contoso` come criterio di individuazione. L'indice BLOB filtra tutti i contenitori nell'intero account di archiviazione per trovare rapidamente e restituire solo il set di BLOB 50 da `Project = Contoso` .

Per iniziare con esempi su come usare l'indice BLOB, vedere [usare i tag degli indici BLOB per gestire e trovare i dati](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Tag di indice BLOB e gestione dati

I prefissi dei nomi dei contenitori e dei BLOB sono categorizzazioni unidimensionali. I tag dell'indice BLOB consentono la categorizzazione multidimensionale per i [tipi di dati BLOB (Block, Append o Page)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). La categorizzazione multidimensionale viene indicizzata in modo nativo dall'archiviazione BLOB di Azure per poter trovare rapidamente i dati.

Considerare i seguenti cinque BLOB nell'account di archiviazione:

- *Container1/transaction.csv*
- *container2/campaign.docx*
- *Foto/bannerphoto.png*
- *archivi/completati/2019review.pdf*
- *log/2020/01/01/logfile.txt*


Questi BLOB sono separati usando un prefisso di *contenitore/cartella virtuale/nome BLOB*. È possibile impostare un attributo di tag index `Project = Contoso` su questi cinque BLOB per categorizzarli insieme mantenendo al tempo stesso l'organizzazione del prefisso corrente. L'aggiunta di tag index Elimina la necessità di spostare i dati esponendo la possibilità di filtrare e trovare i dati mediante l'indice.

## <a name="setting-blob-index-tags"></a>Impostazione di tag di indice BLOB

I tag dell'indice BLOB sono attributi chiave-valore che possono essere applicati a oggetti nuovi o esistenti all'interno dell'account di archiviazione. È possibile specificare i tag di indice durante il processo di caricamento usando le operazioni [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list)o [Copy Blob](/rest/api/storageservices/copy-blob) e l'intestazione facoltativa `x-ms-tags` . Se si dispone già di BLOB nell'account di archiviazione, chiamare [set BLOB Tags](/rest/api/storageservices/set-blob-tags) passando un documento XML formattato con i tag di indice nel corpo della richiesta.

> [!IMPORTANT]
> L'impostazione dei tag degli indici BLOB può essere eseguita dal [proprietario dei dati del BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) e da chiunque disponga di una firma di accesso condiviso con l'autorizzazione per accedere ai tag del BLOB ( `t` autorizzazione SAS).
>
> Inoltre, gli utenti RBAC con l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` autorizzazione possono eseguire questa operazione.

È possibile applicare un singolo tag al BLOB per descrivere quando i dati sono finiti nell'elaborazione.

> "DataElaborazione" = "2020-01-01"

È possibile applicare più tag al BLOB per essere più descrittivi dei dati.

> "Project" = "contoso"  
> "Classified" =' true '  
> "Status" = "non elaborato"  
> "Priority" = '01 '

Per modificare gli attributi del tag di indice esistenti, recuperare gli attributi del tag esistenti, modificare gli attributi del tag e sostituire con l'operazione di [impostazione dei tag BLOB](/rest/api/storageservices/set-blob-tags) . Per rimuovere tutti i tag di indice dal BLOB, chiamare l' `Set Blob Tags` operazione senza specificare attributi di tag. Poiché i tag degli indici BLOB sono una sottorisorsa per il contenuto dei dati BLOB, `Set Blob Tags` non modifica alcun contenuto sottostante e non modifica il valore ETag dell'Ultima modifica del BLOB. È possibile creare o modificare i tag di indice per tutti i BLOB di base correnti e le versioni precedenti. Tuttavia, non è possibile modificare i tag negli snapshot o nei BLOB eliminati temporaneamente.

Ai tag di indice BLOB si applicano i limiti seguenti:

- Ogni BLOB può avere fino a 10 tag di indice BLOB
- Le chiavi tag devono avere una lunghezza compresa tra 1 e 128 caratteri
- I valori dei tag devono essere compresi tra 0 e 256 caratteri
- Le chiavi e i valori dei tag distinguono
- Le chiavi e i valori dei tag supportano solo i tipi di dati stringa. Eventuali numeri, date, ore o caratteri speciali vengono salvati come stringhe
- Le chiavi e i valori dei tag devono rispettare le seguenti regole di denominazione:
  - Caratteri alfanumerici:
    - **a** - **z** (lettere minuscole)
    - **A** - **Z** (lettere maiuscole)
    - **0** - **9** (numeri)
  - Caratteri speciali validi: spazio, più, meno, punto, due punti, uguale a, carattere di sottolineatura, barra ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Recupero e visualizzazione di tag di indice BLOB

I tag degli indici BLOB vengono archiviati come sottorisorse insieme ai dati BLOB e possono essere recuperati in modo indipendente dal contenuto dei dati BLOB sottostante. I tag di indice BLOB per un singolo BLOB possono essere recuperati con l'operazione [Get BLOB Tags](/rest/api/storageservices/get-blob-tags) . L'operazione [List Blobs](/rest/api/storageservices/list-blobs) con il `include:tags` parametro restituirà anche tutti i BLOB all'interno di un contenitore insieme ai relativi tag di indice BLOB.

> [!IMPORTANT]
> Il recupero e l'elenco dei tag degli indici BLOB possono essere eseguiti dal [proprietario dei dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) e da chiunque disponga di una firma di accesso condiviso con l'autorizzazione per accedere ai tag del BLOB ( `t` autorizzazione SAS).
>
> Inoltre, gli utenti RBAC con l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` autorizzazione possono eseguire questa operazione.

Per tutti i BLOB con almeno un tag di indice BLOB, `x-ms-tag-count` viene restituito nelle operazioni [List Blobs](/rest/api/storageservices/list-blobs), [Get BLOB](/rest/api/storageservices/get-blob)e [Get Blob Properties](/rest/api/storageservices/get-blob-properties) che indicano il numero di tag di indice nel BLOB.

## <a name="finding-data-using-blob-index-tags"></a>Ricerca di dati tramite tag di indice BLOB

Il motore di indicizzazione espone gli attributi chiave-valore in un indice multidimensionale. Dopo aver impostato i tag di indice, sono presenti nel BLOB e possono essere recuperati immediatamente. L'aggiornamento dell'indice BLOB potrebbe richiedere del tempo. Dopo l'aggiornamento dell'indice BLOB, è possibile usare le funzionalità di individuazione e query native offerte dall'archiviazione BLOB.

L'operazione [trova BLOB per tag](/rest/api/storageservices/find-blobs-by-tags) consente di ottenere un set filtrato di BLOB i cui tag di indice corrispondono a un'espressione di query specificata. `Find Blobs by Tags` supporta l'applicazione di filtri a tutti i contenitori all'interno dell'account di archiviazione oppure è possibile definire l'ambito del filtro solo per un singolo contenitore. Poiché tutte le chiavi e i valori dei tag di indice sono stringhe, gli operatori relazionali utilizzano un ordinamento lessicografico.

> [!IMPORTANT]
> La ricerca di dati tramite tag di indice BLOB può essere eseguita dal [proprietario dei dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) e da chiunque disponga di una firma di accesso condiviso con l'autorizzazione per individuare i BLOB in base ai tag ( `f` autorizzazione SAS).
>
> Inoltre, gli utenti RBAC con l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` autorizzazione possono eseguire questa operazione.

I criteri seguenti si applicano al filtro dell'indice BLOB:

- I tasti tag devono essere racchiusi tra virgolette doppie (")
- I valori di tag e i nomi di contenitore devono essere racchiusi tra virgolette singole (')
- Il carattere @ è consentito solo per filtrare in base a un nome di contenitore specifico (ad esempio, `@container = 'ContainerName'` )
- I filtri vengono applicati con l'ordinamento lessicografico sulle stringhe
- Le stesse operazioni sull'intervallo con lati sulla stessa chiave non sono valide (ad esempio, `"Rank" > '10' AND "Rank" >= '15'` )
- Quando si usa REST per creare un'espressione di filtro, i caratteri devono essere codificati come URI

Nella tabella seguente sono elencati tutti gli operatori validi per `Find Blobs by Tags` :

|  Operatore  |  Descrizione  | Esempio |
|------------|---------------|---------|
|     =      |     Uguale     | `"Status" = 'In Progress'` |
|     >      |  Maggiore di | `"Date" > '2018-06-18'` |
|     >=     |  Maggiore o uguale a | `"Priority" >= '5'` |
|     <      |  Minore di   | `"Age" < '32'` |
|     <=     |  Minore o uguale a  | `"Company" <= 'Contoso'` |
|    AND     |  And logico  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Ambito per un contenitore specifico | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Acquisire familiarità con l'ordinamento di lessicografico durante l'impostazione e l'esecuzione di query sui tag.
>
> - I numeri vengono ordinati prima delle lettere. I numeri vengono ordinati in base alla prima cifra.
> - Le lettere maiuscole sono ordinate prima delle lettere minuscole.
> - I simboli non sono standard. Alcuni simboli vengono ordinati prima dei valori numerici. Gli altri simboli vengono ordinati prima o dopo le lettere.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operazioni BLOB condizionali con tag di indice BLOB

Nelle versioni REST 2019-10-10 e successive, la maggior parte delle [API del servizio BLOB](/rest/api/storageservices/operations-on-blobs) supporta ora un'intestazione condizionale, `x-ms-if-tags` , in modo che l'operazione riesca solo se viene soddisfatta la condizione di indice BLOB specificata. Se la condizione non viene soddisfatta, si otterrà `error 412: The condition specified using HTTP conditional header(s) is not met` .

L' `x-ms-if-tags` intestazione può essere combinata con le altre intestazioni condizionali http esistenti (If-Match, If-None-Match e così via). Se in una richiesta vengono fornite più intestazioni condizionali, è necessario che tutti valutino true affinché l'operazione abbia esito positivo. Tutte le intestazioni condizionali vengono combinate in modo efficace con AND logico.

La tabella seguente Mostra gli operatori validi per le operazioni condizionali:

|  Operatore  |  Descrizione  | Esempio |
|------------|---------------|---------|
|     =      |     Uguale a     | `"Status" = 'In Progress'` |
|     <>     |   Diverso da   | `"Status" <> 'Done'` |
|     >      |  Maggiore di | `"Date" > '2018-06-18'` |
|     >=     |  Maggiore o uguale a | `"Priority" >= '5'` |
|     <      |  Minore di   | `"Age" < '32'` |
|     <=     |  Minore o uguale a  | `"Company" <= 'Contoso'` |
|    AND     |  And logico  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OR     | OR logico   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Sono disponibili due operatori aggiuntivi, non uguali e logici, che sono consentiti nell' `x-ms-if-tags` intestazione condizionale per le operazioni BLOB, ma non esistono nell' `Find Blobs by Tags` operazione.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrazioni della piattaforma con tag di indice BLOB

I tag di indice BLOB non solo consentono di categorizzare, gestire e cercare i dati BLOB, ma anche di fornire l'integrazione con altre funzionalità di archiviazione BLOB, ad esempio la [gestione del ciclo](storage-lifecycle-management-concepts.md)di vita.

### <a name="lifecycle-management"></a>Gestione del ciclo di vita

Usando il `blobIndexMatch` come filtro delle regole nella gestione del ciclo di vita, è possibile spostare i dati in livelli più sporadici o eliminare i dati in base ai tag di indice applicati ai BLOB. È possibile disporre di una maggiore granularità nelle regole e spostare o eliminare BLOB solo se corrispondono ai criteri di tag specificati.

È possibile impostare una corrispondenza dell'indice BLOB come un filtro autonomo impostato in una regola del ciclo di vita per applicare azioni sui dati con tag. In alternativa, è possibile combinare un prefisso e un indice BLOB per abbinare set di dati più specifici. La specifica di più filtri in una regola del ciclo di vita applica un'operazione AND logica. L'azione viene applicata solo se *tutti i* criteri di filtro corrispondono.

La regola di gestione del ciclo di vita di esempio seguente si applica ai BLOB in blocchi in un contenitore denominato *videofiles*. La regola archivia i BLOB per archiviare l'archiviazione solo se i dati corrispondono ai criteri dei tag di indice BLOB di `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portale](#tab/azure-portal)

![Esempio di regola di corrispondenza dell'indice BLOB per la gestione del ciclo di vita in portale di Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>Autorizzazioni e autorizzazione

È possibile autorizzare l'accesso ai tag degli indici BLOB usando uno degli approcci seguenti:

- Uso del controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per concedere le autorizzazioni a un'entità di sicurezza Azure Active Directory (Azure AD). Usare Azure AD per una maggiore sicurezza e semplicità d'uso. Per altre informazioni sull'uso di Azure AD con le operazioni BLOB, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](../common/storage-auth-aad.md).
- Uso di una firma di accesso condiviso (SAS) per delegare l'accesso all'indice BLOB. Per altre informazioni sulle firme di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).
- Uso delle chiavi di accesso dell'account per autorizzare le operazioni con la chiave condivisa. Per altre informazioni, vedere [Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).

I tag di indice BLOB sono una sottorisorsa per i dati BLOB. Un utente con autorizzazioni o un token di firma di accesso condiviso per la lettura o la scrittura di BLOB potrebbe non avere accesso ai tag degli indici BLOB.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Ai chiamanti che usano un' [identità Azure ad](../common/storage-auth-aad.md) possono essere concesse le autorizzazioni seguenti per operare sui tag degli indici BLOB.

| Operazioni sui tag degli indici BLOB                                          | Azione RBAC di Azure                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Imposta tag BLOB](/rest/api/storageservices/set-blob-tags)           | Microsoft. storage/storageAccounts/blobServices/Containers/BLOB/Tag/scrittura    |
| [Ottieni Tag BLOB](/rest/api/storageservices/get-blob-tags)           | Microsoft. storage/storageAccounts/blobServices/Containers/BLOB/Tag/lettura     |
| [Trovare i BLOB in base ai tag](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. storage/storageAccounts/blobServices/Containers/BLOB/filtro/azione |

Per le operazioni sui tag di indice sono necessarie autorizzazioni aggiuntive, separate dai dati BLOB sottostanti. Al ruolo [proprietario dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) vengono concesse le autorizzazioni per tutte e tre le operazioni sui tag di indice BLOB. Al [lettore di dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) vengono concesse solo `Find Blobs by Tags` le autorizzazioni per `Get Blob Tags` le operazioni e.

### <a name="sas-permissions"></a>Autorizzazioni SAS

Ai chiamanti che usano una [firma di accesso condiviso](../common/storage-sas-overview.md) possono essere concesse autorizzazioni con ambito per operare sui tag degli indici BLOB.

#### <a name="blob-sas"></a>SAS BLOB

Per consentire l'accesso ai tag degli indici BLOB, è possibile concedere le autorizzazioni seguenti in una firma di accesso condiviso del BLOB. Solo le autorizzazioni di lettura e scrittura di BLOB non sono sufficienti per consentire la lettura o la scrittura dei tag di indice.

| Autorizzazione | Simbolo URI | Operazioni consentite                |
|------------|------------|-----------------------------------|
| Tag di indice |     u      | Ottenere e impostare i tag di indice per un BLOB |

#### <a name="container-sas"></a>Firma di accesso condiviso del contenitore

Le autorizzazioni seguenti possono essere concesse in una firma di accesso condiviso del contenitore per consentire l'applicazione di filtri ai tag BLOB. L' `Blob List` autorizzazione non è sufficiente per consentire il filtraggio dei BLOB in base ai tag di indice.

| Autorizzazione | Simbolo URI | Operazioni consentite         |
|------------|------------|----------------------------|
| Tag di indice |     f      | Trovare BLOB con tag di indice |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Scelta tra i metadati e i tag dell'indice BLOB

I tag e i metadati degli indici BLOB consentono di archiviare proprietà arbitrarie di valori chiave definite dall'utente insieme a una risorsa BLOB. Entrambe le impostazioni possono essere recuperate e impostate direttamente, senza restituire o modificare il contenuto del BLOB. È possibile usare sia i metadati che i tag di indice.

Solo i tag di indice vengono automaticamente indicizzati e resi disponibili per la ricerca da parte del servizio di archiviazione BLOB nativo. I metadati non possono essere indicizzati o cercati in modo nativo. È necessario usare un servizio separato, ad esempio [ricerca di Azure](../../search/search-blob-ai-integration.md). I tag dell'indice BLOB hanno autorizzazioni aggiuntive per la lettura, il filtraggio e la scrittura separati dai dati BLOB sottostanti. I metadati utilizzano le stesse autorizzazioni del BLOB e vengono restituiti come intestazioni HTTP dalle operazioni [Get BLOB](/rest/api/storageservices/get-blob) e [Get Blob Properties](/rest/api/storageservices/get-blob-properties) . I tag dell'indice BLOB vengono crittografati a riposo usando una [chiave gestita da Microsoft](../common/storage-service-encryption.md). I metadati vengono crittografati a riposo usando la stessa chiave di crittografia specificata per i dati BLOB.

La tabella seguente riepiloga le differenze tra i metadati e i tag degli indici BLOB:

|              |   Metadati   |   Tag di indice BLOB  |
|--------------|--------------|--------------------|
| **Limiti**      | Nessun limite numerico, totale 8 KB, senza distinzione tra maiuscole e minuscole | 10 tag per BLOB Max, 768 byte per tag, maiuscole/minuscole |
| **Aggiornamenti**    | Non consentita nel livello archivio, `Set Blob Metadata` sostituisce tutti i metadati esistenti, `Set Blob Metadata` modifica l'ora dell'Ultima modifica del BLOB | Consentito per tutti i livelli di accesso, `Set Blob Tags` sostituisce tutti i tag esistenti, `Set Blob Tags` non modifica il tempo Ultima modifica del BLOB |
| **Archiviazione**     | Archiviati con i dati BLOB | Sottorisorsa dei dati BLOB |
| **Indicizzazione & query** | Deve usare un servizio separato, ad esempio ricerca di Azure | Indicizzazione ed esecuzione di query sulle funzionalità incorporate nell'archivio BLOB |
| **Crittografia** | Crittografati a riposo con la stessa chiave di crittografia usata per i dati BLOB | Crittografati a riposo con una chiave di crittografia gestita da Microsoft |
| **Prezzi** | Dimensioni dei metadati inclusi nei costi di archiviazione per un BLOB | Costo fisso per tag di indice |
| **Risposta intestazione** | Metadati restituiti come intestazioni in `Get Blob` e `Get Blob Properties` | Numero di tag restituito da `Get Blob` o `Get Blob Properties` , tag restituiti solo da `Get Blob Tags` e `List Blobs` |
| **Autorizzazioni**  | Le autorizzazioni di lettura o scrittura per i dati BLOB si estendono ai metadati | Sono necessarie autorizzazioni aggiuntive per leggere, filtrare o scrivere tag di indice |
| **Denominazione** | I nomi dei metadati devono essere conformi alle regole di denominazione per gli identificatori C# | I tag di indice BLOB supportano una più ampia gamma di caratteri alfanumerici |

## <a name="pricing"></a>Prezzi

I prezzi per gli indici BLOB sono disponibili in anteprima pubblica e sono soggetti a modifiche per la disponibilità generale. Viene addebitato il numero medio mensile di tag di indice all'interno di un account di archiviazione. Il motore di indicizzazione non prevede alcun costo. Le richieste a `Set Blob Tags` , `Get Blob Tags` e `Find Blobs by Tags` vengono addebitate in base ai rispettivi tipi di operazione. Per ulteriori informazioni, vedere l'articolo relativo [ai prezzi per BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="regional-availability-and-storage-account-support"></a>Supporto per disponibilità e account di archiviazione a livello di area

I tag di indice BLOB sono disponibili solo negli account per utilizzo generico V2 (GPv2) con spazio dei nomi gerarchico (HNS) disabilitato. Gli account per utilizzo generico (utilizzo generico V1) non sono supportati, ma è possibile aggiornare qualsiasi account utilizzo generico V1 a un account GPv2.

I tag di indice non sono supportati negli account di archiviazione Premium. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Nell'anteprima pubblica, i tag degli indici BLOB sono disponibili solo nelle aree seguenti:

- Canada centrale
- Canada orientale
- Francia centrale
- Francia meridionale

Per iniziare, vedere [usare i tag degli indici BLOB per gestire e trovare i dati](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Prima di poter usare l'anteprima dell'indice BLOB negli account di archiviazione, è necessario registrare la sottoscrizione. Vedere la sezione [condizioni e problemi noti](#conditions-and-known-issues) di questo articolo.

### <a name="register-your-subscription-preview"></a>Registrare la sottoscrizione (anteprima)

Poiché i tag dell'indice BLOB sono disponibili solo in anteprima pubblica, è necessario registrare la sottoscrizione prima di poter usare la funzionalità. Per inviare una richiesta, eseguire i comandi PowerShell o dell'interfaccia della riga di comando seguenti.

#### <a name="register-by-using-powershell"></a>Eseguire la registrazione tramite PowerShell

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrare usando l'interfaccia della riga di comando di Azure

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Condizioni e problemi noti

Questa sezione descrive i problemi noti e le condizioni nell'anteprima pubblica dei tag degli indici BLOB. Questa funzionalità non deve essere usata per i carichi di lavoro di produzione fino a quando non raggiunge la disponibilità generale (GA) perché il comportamento potrebbe cambiare.

- Per l'anteprima, è necessario prima registrare la sottoscrizione prima di poter usare l'indice BLOB per l'account di archiviazione nelle aree di anteprima.
- Solo gli account GPv2 sono supportati nell'anteprima. Gli account Gen2 di datalake abilitati per BLOB, BlockBlobStorage e HNS non sono supportati. Gli account utilizzo generico V1 non saranno supportati.
- Il caricamento di BLOB di pagine con tag di indice non rende persistenti i tag. Impostare i tag dopo il caricamento di un BLOB di pagine.
- Quando il filtro è limitato a un singolo contenitore, `@container` può essere passato solo se tutti i tag di indice nell'espressione di filtro sono controlli di uguaglianza (chiave = valore).
- Quando si utilizza l'operatore Range con la `AND` condizione, è possibile specificare solo lo stesso nome di chiave del tag di indice ( `"Age" > '013' AND "Age" < '100'` ).
- Il controllo delle versioni e l'indice BLOB non sono supportati. I tag dell'indice BLOB vengono conservati per le versioni, ma non vengono passati al motore dell'indice BLOB.
- Non è disponibile alcuna API per determinare se i tag di indice sono indicizzati.
- Il failover dell'account non è supportato. È possibile che l'indice BLOB non venga aggiornato correttamente dopo il failover.
- La gestione del ciclo di vita supporta solo i controlli di uguaglianza con corrispondenza dell'indice BLOB.
- `Copy Blob` non copia i tag degli indici BLOB dal BLOB di origine al nuovo BLOB di destinazione. È possibile specificare i tag che si desidera applicare al BLOB di destinazione durante l'operazione di copia.
- `Copy Blob` (Copia asincrona) da un altro account di archiviazione con tag applicati nel BLOB di destinazione, il motore dell'indice BLOB non restituisce il BLOB e i relativi tag nel set di filtri. Usare `Copy Blob` da URL (copia di sincronizzazione).
- I tag vengono mantenuti durante la creazione dello snapshot. Tuttavia, la promozione di uno snapshot non è supportata e può causare un set di tag vuoto.

## <a name="faq"></a>Domande frequenti

**L'indice BLOB può aiutarmi a filtrare ed eseguire query sul contenuto all'interno di BLOB?**

No, se è necessario eseguire ricerche nei dati BLOB, usare l'accelerazione query o ricerca di Azure.

**Sono previsti requisiti per i valori dei tag di indice?**

I tag dell'indice BLOB supportano solo i tipi di dati stringa e l'esecuzione di query restituisce i risultati con l'ordinamento lessicografico. Per i numeri, azzerare il numero. Per date e ore, archiviare come formato conforme a ISO 8601.

**Sono correlati tag di indice BLOB e Azure Resource Manager Tag?**

No, Gestione risorse tag consentono di organizzare le risorse del piano di controllo, ad esempio le sottoscrizioni, i gruppi di risorse e gli account di archiviazione. I tag di indice forniscono la gestione e l'individuazione di BLOB nel piano dati.

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di come usare l'indice BLOB, vedere [usare l'indice BLOB per gestire e trovare i dati](storage-blob-index-how-to.md).

Informazioni sulla [gestione del ciclo](storage-lifecycle-management-concepts.md) di vita e sull'impostazione di una regola con corrispondenza dell'indice BLOB.
