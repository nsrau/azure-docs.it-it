---
title: Gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB (anteprima)
description: Informazioni su come usare i tag degli indici BLOB per categorizzare, gestire ed eseguire query per oggetti BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.custom: references_regions
ms.openlocfilehash: db23d3b5c532a1539936b51222345c98679c554c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91817531"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-preview"></a>Gestire e trovare i dati BLOB di Azure con indice BLOB (anteprima)

Poiché i set di dati ottengono dimensioni maggiori e maggiori, la ricerca di un oggetto specifico in un mare di dati può risultare difficile e frustrante. L'indice BLOB offre funzionalità di gestione e individuazione dei dati usando attributi di tag di indice chiave-valore che consentono di categorizzare e trovare oggetti all'interno di un singolo contenitore o di tutti i contenitori nell'account di archiviazione. In seguito, in base ai requisiti di modifica dei dati, gli oggetti possono essere categorizzati dinamicamente aggiornando i tag degli indici rimanendo sul posto con l'organizzazione del contenitore corrente. L'uso di un indice BLOB può semplificare lo sviluppo consolidando i dati BLOB e gli attributi degli indici associati nello stesso servizio. consente di creare applicazioni efficienti e scalabili usando le funzionalità native.

Indice BLOB consente di:

- Categorizzare dinamicamente i BLOB usando i tag di indice chiave-valore per la gestione dei dati
- Trovare rapidamente BLOB con tag specifici in un singolo contenitore o in un intero account di archiviazione
- Specificare i comportamenti condizionali per le API BLOB in base alla valutazione dei tag di indice
- Usare i tag di indice per controlli avanzati sulle funzionalità della piattaforma BLOB come la [gestione del ciclo](storage-lifecycle-management-concepts.md) di vita

Si consideri lo scenario in cui si hanno milioni di BLOB nell'account di archiviazione scritti e a cui si accede da molte applicazioni diverse. Si desidera trovare tutti i dati correlati da un singolo progetto, ma non si è certi dell'ambito perché i dati possono essere distribuiti in più contenitori con convenzioni di denominazione BLOB diverse. Tuttavia, si sa che le applicazioni caricano tutti i dati con tag basati sul rispettivo progetto e identificano la descrizione. Anziché cercare in milioni di BLOB e confrontare nomi e proprietà, è possibile usare semplicemente `Project = Contoso` come criterio di individuazione. L'indice BLOB filtra tutti i contenitori nell'intero account di archiviazione per trovare rapidamente e restituire solo il set di BLOB 50 da `Project = Contoso` .

Per iniziare con esempi su come usare l'indice BLOB, vedere usare l' [Indice BLOB per gestire e trovare i dati](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Tag di indice BLOB e gestione dati

I prefissi dei nomi di BLOB e contenitori sono una categorizzazione unidimensionale per i dati archiviati. L'indice BLOB consente ora la categorizzazione MULTIDIMENSIONE per tutti i [tipi di dati BLOB (blocco, Accodamento o pagina)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) con i tag degli attributi applicati. Questa categorizzazione multidimensionale è indicizzata in modo nativo e viene resa disponibile per eseguire rapidamente query e trovare i dati.

Considerare i seguenti cinque BLOB nell'account di archiviazione:

- *Container1/transaction.csv*
- *container2/campaign.docx*
- *Foto/bannerphoto.png*
- *archivi/completati/2019review.pdf*
- *log/2020/01/01/logfile.txt*


Questi BLOB sono attualmente separati usando un prefisso di *contenitore/cartella virtuale/nome BLOB*. Con l'indice BLOB, è possibile impostare un attributo di tag index `Project = Contoso` su questi cinque BLOB per categorizzarli insieme mantenendo al tempo stesso l'organizzazione del prefisso corrente. In questo modo si elimina la necessità di spostare i dati esponendo la possibilità di filtrare e trovare i dati usando l'indice multidimensionale della piattaforma di archiviazione.

## <a name="setting-blob-index-tags"></a>Impostazione di tag di indice BLOB

I tag dell'indice BLOB sono attributi chiave-valore che possono essere applicati a oggetti nuovi o esistenti all'interno dell'account di archiviazione. È possibile specificare i tag di indice durante il processo di caricamento usando le operazioni PutBlob, PutBlockList o CopyBlob e l'intestazione x-ms-tag facoltativa. Se si dispone già di BLOB nell'account di archiviazione, è possibile chiamare SetBlobTags con un documento XML formattato che specifica gli attributi del tag di indice BLOB nel corpo della richiesta.

Si considerino i tag di esempio seguenti che possono essere impostati

È possibile applicare un singolo tag al BLOB per descrivere quando i dati sono finiti nell'elaborazione.

> "DataElaborazione" = "2020-01-01"

È possibile applicare più tag al BLOB per essere più descrittivi dei dati.

> "Project" = "contoso"  
> "Classified" =' true '  
> "Status" = "non elaborato"  
> "Priority" = '01 '

Per modificare gli attributi del tag di indice esistenti, è innanzitutto necessario recuperare gli attributi del tag esistenti, modificare gli attributi dei tag e sostituire con l'operazione SetBlobTags. Per rimuovere tutti i tag index dal BLOB, chiamare l'operazione SetBlobTags senza specificare attributi tag. Poiché i tag degli indici BLOB sono una risorsa secondaria per il contenuto dei dati BLOB, SetBlobTags non modifica alcun contenuto sottostante e non modifica il valore Last-Modified-Time o eTag (tag di entità) del BLOB. È possibile creare o modificare i tag di indice per tutti i BLOB di base correnti e le versioni precedenti. Tuttavia, non è possibile modificare i tag negli snapshot o nei BLOB eliminati temporaneamente.

Ai tag di indice BLOB si applicano i limiti seguenti:
- Ogni BLOB può avere fino a 10 tag di indice BLOB
- Le chiavi tag devono avere una lunghezza compresa tra 1 e 128 caratteri
- I valori dei tag devono essere compresi tra 0 e 256 caratteri
- Le chiavi e i valori dei tag distinguono
- Le chiavi e i valori dei tag supportano solo i tipi di dati stringa. eventuali numeri, date, ore o caratteri speciali verranno salvati come stringhe
- Le chiavi e i valori dei tag devono rispettare le seguenti regole di denominazione:
  - Caratteri alfanumerici:
    - **a** - **z** (lettere minuscole)
    - **A** - **Z** (lettere maiuscole)
    - **0** - **9** (numeri)
  - Caratteri speciali validi: spazio, più, meno, punto, due punti, uguale a, carattere di sottolineatura, barra ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Recupero e visualizzazione di tag di indice BLOB

I tag degli indici BLOB vengono archiviati come una risorsa secondaria insieme ai dati BLOB e possono essere recuperati in modo indipendente dal contenuto dei dati BLOB sottostante. Una volta impostati, è possibile recuperare e rivedere immediatamente i tag dell'indice BLOB per un singolo BLOB con l'operazione GetBlobTags. L'operazione ListBlobs con il `include:tags` parametro restituirà anche tutti i BLOB all'interno di un contenitore insieme ai tag di indice BLOB applicati.

Per tutti i BLOB con almeno 1 tag di indice BLOB, il valore x-ms-tag-Count viene restituito nelle operazioni ListBlobs, GetBlob e GetBlobProperties che indicano il numero di tag di indice BLOB presenti nel BLOB.

## <a name="finding-data-using-blob-index-tags"></a>Ricerca di dati tramite tag di indice BLOB

Con i tag di indice BLOB impostati sui BLOB, il motore di indicizzazione espone tali attributi chiave/valore in un indice multidimensionale. Sebbene i tag di indice esistano nel BLOB e possano essere recuperati immediatamente, è possibile che l'indice BLOB venga aggiornato con gli attributi di tag degli indici aggiornati. Una volta aggiornato l'indice BLOB, è ora possibile sfruttare le funzionalità di individuazione e query native offerte dall'archiviazione BLOB.

L'operazione FindBlobsByTags consente di ottenere un set di BLOB restituito filtrato i cui tag di indice corrispondono a un'espressione di query dell'indice BLOB specificata. L'indice BLOB supporta il filtraggio in tutti i contenitori all'interno dell'account di archiviazione oppure è possibile definire l'ambito del filtro per un solo contenitore. Poiché tutte le chiavi e i valori dei tag di indice BLOB sono stringhe, gli operatori relazionali supportati utilizzano un ordinamento lessicografico sui valori dei tag di indice.

I criteri seguenti si applicano al filtro dell'indice BLOB:
- I tasti tag devono essere racchiusi tra virgolette doppie (")
- I valori di tag e i nomi di contenitore devono essere racchiusi tra virgolette singole (')
- Il carattere @ è consentito solo per filtrare in base a un nome di contenitore specifico @container , ad esempio =' ContainerName '
- I filtri vengono applicati con l'ordinamento lessicografico sulle stringhe
- Le stesse operazioni sull'intervallo con lati sulla stessa chiave non sono valide, ad esempio "Rank" > "10" e "Rank" >= '15 ')
- Quando si usa REST per creare un'espressione di filtro, i caratteri devono essere codificati come URI

La tabella seguente mostra tutti gli operatori validi per FindBlobsByTags:

|  Operatore  |  Descrizione  | Esempio |
|------------|---------------|---------|
|     =      |     Uguale     | "Status" = "in corso" |
|     >      |  Maggiore di | "Date" >' 2018-06-18' |
|     >=     |  Maggiore o uguale a | "Priority" >=' 5' |
|     <      |  Minore di   | "Age" < "32" |
|     <=     |  Minore o uguale a  | "Company" <= "contoso" |
|    AND     |  And logico  | "Rank" >=' 010' è Rank ' <' 100' |
| @container | Ambito per un contenitore specifico | @container =' videofiles ' è status ' =' done ' |

> [!NOTE]
> Acquisire familiarità con l'ordinamento di lessicografico durante l'impostazione e l'esecuzione di query sui tag.
> - I numeri vengono ordinati prima delle lettere. I numeri vengono ordinati in base alla prima cifra.
> - Le lettere maiuscole sono ordinate prima delle lettere minuscole.
> - I simboli non sono standard. Alcuni simboli vengono ordinati prima dei valori numerici. Gli altri simboli vengono ordinati prima o dopo le lettere.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operazioni BLOB condizionali con tag di indice BLOB
Nelle versioni REST 2019-10-10 e successive, la maggior parte delle [API del servizio BLOB](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) supporta ora un'intestazione condizionale, x-ms-if-Tags, in modo che l'operazione riesca solo se viene soddisfatta la condizione di indice BLOB specificata. Se la condizione non viene soddisfatta, si otterrà `error 412: The condition specified using HTTP conditional header(s) is not met` .

L'intestazione x-ms-if-tag può essere combinata con le altre intestazioni condizionali HTTP esistenti (If-Match, If-None-Match e così via).  Se in una richiesta vengono fornite più intestazioni condizionali, è necessario che tutti valutino true affinché l'operazione abbia esito positivo.  Tutte le intestazioni condizionali vengono combinate in modo efficace con AND logico.

La tabella seguente mostra tutti gli operatori validi per le operazioni condizionali:

|  Operatore  |  Descrizione  | Esempio |
|------------|---------------|---------|
|     =      |     Uguale     | "Status" = "in corso" |
|     <>     |   Diverso da   | "Stato"  <> ' operazione eseguità  |
|     >      |  Maggiore di | "Date" >' 2018-06-18' |
|     >=     |  Maggiore o uguale a | "Priority" >=' 5' |
|     <      |  Minore di   | "Age" < "32" |
|     <=     |  Minore o uguale a  | "Company" <= "contoso" |
|    AND     |  And logico  | "Rank" >=' 010' è Rank ' <' 100' |
|     OR     | OR logico   | "Status" = "Done" o "Priority" >= "05" |

> [!NOTE]
> Sono disponibili due operatori aggiuntivi, non uguali e logici, che sono consentiti nell'intestazione x-ms-if-Tags condizionale per l'operazione BLOB, ma non esistono nell'operazione FindBlobsByTags.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrazioni della piattaforma con tag di indice BLOB

I tag dell'indice BLOB non solo consentono di categorizzare, gestire ed eseguire ricerche nei dati BLOB, ma anche di fornire integrazioni con altre funzionalità del servizio BLOB, ad esempio la [gestione del ciclo](storage-lifecycle-management-concepts.md)di vita.

### <a name="lifecycle-management"></a>Gestione del ciclo di vita

Usando il nuovo blobIndexMatch come filtro delle regole nella gestione del ciclo di vita, è possibile spostare i dati in livelli più sporadici o eliminare i dati in base ai tag di indice applicati ai BLOB. In questo modo è possibile disporre di una maggiore granularità nelle regole e spostare o eliminare solo i dati se corrispondono ai criteri di tag specificati.

È possibile impostare una corrispondenza dell'indice BLOB come un filtro autonomo impostato in una regola del ciclo di vita per applicare azioni sui dati con tag. In alternativa, è possibile combinare sia una corrispondenza di prefisso sia una corrispondenza dell'indice BLOB per abbinare set di dati più specifici. L'applicazione di più filtri a una regola del ciclo di vita è un'operazione logica e tale che l'azione verrà applicata solo se tutti i criteri di filtro corrispondono.

La regola di gestione del ciclo di vita di esempio seguente si applica ai BLOB in blocchi nel contenitore ' videofiles ' e ai BLOB in livelli per archiviare l'archiviazione solo se i dati corrispondono ai criteri dei tag di indice BLOB di ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

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

È possibile autorizzare l'accesso all'indice BLOB usando uno degli approcci seguenti:

- Usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per concedere le autorizzazioni a un'entità di sicurezza Azure Active Directory (Azure AD). Microsoft consiglia di usare Azure AD per una maggiore sicurezza e semplicità d'uso. Per altre informazioni sull'uso di Azure AD con le operazioni BLOB, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](../common/storage-auth-aad.md).
- Usando una firma di accesso condiviso (SAS) per delegare l'accesso all'indice BLOB. Per altre informazioni sulle firme di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).
- Utilizzando le chiavi di accesso dell'account per autorizzare le operazioni con la chiave condivisa. Per altre informazioni, vedere [Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).

I tag di indice BLOB sono una risorsa secondaria per i dati BLOB. Un utente con autorizzazioni o un token di firma di accesso condiviso per la lettura o la scrittura di BLOB potrebbe non avere accesso ai tag degli indici BLOB.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Ai chiamanti che usano un' [identità Azure ad](../common/storage-auth-aad.md) possono essere concesse le autorizzazioni seguenti per operare sui tag degli indici BLOB.

|   Operazioni BLOB  |  Azione RBAC di Azure   |
|--------------------|----------------|
| Trovare i BLOB in base ai tag | Microsoft. storage/storageAccounts/blobServices/Containers/BLOB/filtro/azione |
| Imposta tag BLOB      | Microsoft. storage/storageAccounts/blobServices/Containers/BLOB/Tag/scrittura |
| Ottieni Tag BLOB      | Microsoft. storage/storageAccounts/blobServices/Containers/BLOB/Tag/lettura |

Per il funzionamento dei tag sono necessarie autorizzazioni aggiuntive separate dai dati BLOB sottostanti. Al ruolo proprietario dati BLOB di archiviazione verranno concesse tutte e tre queste autorizzazioni. Al lettore di dati BLOB di archiviazione verranno concesse le autorizzazioni trova BLOB in base ai tag e ottieni Tag BLOB.

### <a name="sas-permissions"></a>Autorizzazioni SAS
Ai chiamanti che usano una [firma di accesso condiviso](../common/storage-sas-overview.md) possono essere concesse autorizzazioni con ambito per operare sui tag BLOB.

#### <a name="blob-sas"></a>SAS BLOB
In una firma di accesso condiviso del servizio BLOB è possibile concedere le autorizzazioni seguenti per consentire l'accesso ai tag degli indici BLOB. Solo le autorizzazioni di lettura e scrittura del BLOB non sono sufficienti per consentire la lettura o la scrittura dei tag di indice.

|  Autorizzazione  |  Simbolo URI  | Operazioni consentite |
|--------------|--------------|--------------------|
|  Tag di indice  |      t      | Ottenere e impostare i tag degli indici BLOB per un BLOB |

#### <a name="container-sas"></a>Firma di accesso condiviso del contenitore
È possibile concedere le seguenti autorizzazioni nella firma di accesso condiviso del servizio contenitore per consentire l'applicazione di filtri ai tag BLOB.  L'autorizzazione per l'elenco di BLOB non è sufficiente per consentire il filtraggio dei BLOB in base ai tag di indice.

|  Autorizzazione  |  Simbolo URI  | Operazioni consentite |
|--------------|--------------|--------------------|
| Tag di indice   |      f      | Trovare BLOB con tag di indice BLOB |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Scelta tra i metadati e i tag dell'indice BLOB
Sia i tag che i metadati dell'indice BLOB consentono di archiviare proprietà chiave/valore definite dall'utente arbitrarie insieme a una risorsa BLOB. Entrambe le impostazioni possono essere recuperate e impostate direttamente, senza restituire o modificare il contenuto del BLOB. È possibile utilizzare sia i metadati che i tag di indice.

Tuttavia, solo i tag degli indici BLOB vengono automaticamente indicizzati e resi disponibili per query dal servizio BLOB nativo. I metadati non possono essere indicizzati e sottoposti a query in modo nativo a meno che non si utilizzi un servizio separato, ad esempio [ricerca di Azure](../../search/search-blob-ai-integration.md). I tag dell'indice BLOB dispongono inoltre di autorizzazioni aggiuntive per la lettura, il filtro e la scrittura separate dai dati BLOB sottostanti. I metadati utilizzano le stesse autorizzazioni del BLOB e vengono restituiti come intestazioni HTTP nelle operazioni GetBlob o GetBlobProperties. I tag dell'indice BLOB vengono crittografati a riposo usando una [chiave gestita da Microsoft,](../common/storage-service-encryption.md) mentre i metadati vengono crittografati a riposo usando la stessa chiave di crittografia specificata per i dati BLOB.

La tabella seguente riepiloga le differenze tra i metadati e i tag degli indici BLOB:

|              |   Metadati   |   Tag di indice BLOB  |
|--------------|--------------|--------------------|
| **Limiti**      | Nessun limite numerico; 8 KB totali; senza distinzione tra maiuscole e minuscole | 10 tag per BLOB max; 768 byte per tag; distinzione maiuscole/minuscole |
| **Aggiornamenti**    | Non consentito nel livello archivio. SetBlobMetadata sostituisce tutti i metadati esistenti; SetBlobMetadata modifica l'ora dell'Ultima modifica del BLOB | Consentito per tutti i livelli di accesso; SetBlobTags sostituisce tutti i tag esistenti. SetBlobTags non modifica la data e l'ora dell'Ultima modifica del BLOB |
| **Storage**     | Archiviati con i dati BLOB | Risorse secondarie per i dati BLOB |
| **Indicizzazione & query** | N/A in modalità nativa; deve usare un servizio separato, ad esempio ricerca di Azure | Sì, funzionalità di indicizzazione e query native incorporate nell'archivio BLOB |
| **Crittografia** | Crittografati a riposo con la stessa chiave di crittografia usata per i dati BLOB | Crittografati a riposo con una chiave di crittografia gestita da Microsoft |
| **Prezzi** | Dimensioni dei metadati inclusi nei costi di archiviazione per un BLOB | Costo fisso per tag di indice |
| **Risposta intestazione** | Metadati restituiti come intestazioni in GetBlob e GetBlobProperties | TagCount restituito in GetBlob o GetBlobProperties; Tag restituiti solo in GetBlobTags e ListBlobs |
| **Autorizzazioni**  | Le autorizzazioni di lettura o scrittura per i dati BLOB si estendono ai metadati | Sono necessarie autorizzazioni aggiuntive per leggere/filtrare o scrivere tag |
| **Denominazione** | I nomi dei metadati devono essere conformi alle regole di denominazione per gli identificatori C# | I tag di indice BLOB supportano una più ampia gamma di caratteri alfanumerici |

## <a name="pricing"></a>Prezzi
I prezzi per gli indici BLOB sono attualmente in anteprima pubblica e sono soggetti a modifiche per la disponibilità generale. Ai clienti viene addebitato il numero totale di tag di indice BLOB in un account di archiviazione, calcolati in base al mese. Il motore di indicizzazione non prevede alcun costo. Le richieste a SetBlobTags, GetBlobTags e FindBlobsByTags vengono addebitate in base ai rispettivi tipi di operazioni. Per ulteriori informazioni, vedere l'articolo relativo [ai prezzi per BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="regional-availability-and-storage-account-support"></a>Supporto per disponibilità e account di archiviazione a livello di area

L'indice BLOB è attualmente disponibile solo negli account per utilizzo generico V2 (GPv2) con spazio dei nomi gerarchico (HNS) disabilitato. Gli account per utilizzo generico (utilizzo generico V1) non sono supportati, ma è possibile aggiornare qualsiasi account utilizzo generico V1 a un account GPv2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Nell'anteprima pubblica, l'indice BLOB è attualmente disponibile solo nelle aree Select seguenti:
- Canada centrale
- Canada orientale
- Francia centrale
- Francia meridionale

Per iniziare, vedere [usare l'indice BLOB per gestire e trovare i dati](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Vedere la sezione condizioni di questo articolo. Per iscriversi all'anteprima, registrare la sottoscrizione prima di poter usare l'indice BLOB negli account di archiviazione.

### <a name="register-your-subscription-preview"></a>Registrare la sottoscrizione (anteprima)
Poiché l'indice BLOB è solo in anteprima pubblica, è necessario registrare la sottoscrizione prima di poter usare la funzionalità. Per inviare una richiesta, eseguire i comandi PowerShell o dell'interfaccia della riga di comando seguenti.

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

## <a name="conditions-and-known-issues-preview"></a>Condizioni e problemi noti (anteprima)
Questa sezione descrive i problemi noti e le condizioni nell'anteprima pubblica corrente dell'indice BLOB. Come per la maggior parte delle anteprime, questa funzionalità non deve essere usata per i carichi di lavoro di produzione fino a quando non raggiunge la GA perché i comportamenti possono cambiare.

- Per l'anteprima, è necessario prima registrare la sottoscrizione prima di poter usare l'indice BLOB per l'account di archiviazione nelle aree di anteprima.
- Nella versione di anteprima sono attualmente supportati solo gli account GPv2. Gli account datalake Gen2 di BLOB, BlockBlobStorage e HNS abilitati non sono attualmente supportati dall'indice BLOB. Gli account utilizzo generico V1 non saranno supportati.
- Il caricamento di BLOB di pagine con tag di indice non rende attualmente persistenti i tag. È necessario impostare i tag dopo il caricamento di un BLOB di pagine.
- Quando il filtro è limitato a un singolo contenitore, @container può essere passato solo se tutti i tag di indice nell'espressione di filtro sono controlli di uguaglianza (chiave = valore).
- Quando si usa l'operatore Range con la condizione AND, è possibile specificare solo lo stesso nome di chiave del tag di indice (Age >' 013' ed Age <' 100').
- Il controllo delle versioni e l'indice BLOB non sono attualmente supportati. I tag dell'indice BLOB vengono conservati per le versioni, ma non vengono attualmente passati al motore dell'indice BLOB.
- Il failover dell'account non è al momento supportato. È possibile che l'indice BLOB non venga aggiornato correttamente dopo il failover.
- La gestione del ciclo di vita supporta attualmente solo i controlli di uguaglianza con corrispondenza dell'indice BLOB.
- CopyBlob non copia i tag degli indici BLOB dal BLOB di origine al nuovo BLOB di destinazione. È possibile specificare i tag che si desidera applicare al BLOB di destinazione durante l'operazione di copia.
- CopyBlob (copia asincrona) da un altro account di archiviazione con tag applicati nel BLOB di destinazione attualmente il motore dell'indice BLOB non restituisce il BLOB e i relativi tag nel set di filtri. È consigliabile usare CopyBlob da URL (copia di sincronizzazione) nel frattempo.
- I tag vengono mantenuti durante la creazione dello snapshot. Tuttavia, la promozione di uno snapshot non è attualmente supportata e può causare un set di tag vuoto.

## <a name="faq"></a>Domande frequenti

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>L'indice BLOB può aiutarmi a filtrare ed eseguire query sul contenuto all'interno di BLOB?
No, i tag degli indici BLOB consentono di trovare i BLOB che si stanno cercando. Se è necessario eseguire ricerche nei BLOB, usare l'accelerazione query o ricerca di Azure.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Esistono particolari considerazioni sui valori dei tag degli indici BLOB?
I tag dell'indice BLOB supportano solo i tipi di dati stringa e l'esecuzione di query restituisce i risultati con l'ordinamento lessicografico. Per i numeri, è consigliabile azzerare il numero. Per date e ore, è consigliabile archiviare come formato conforme a ISO 8601.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Sono correlati tag di indice BLOB e Azure Resource Manager Tag?
No, Gestione risorse tag consentono di organizzare le risorse del piano di controllo, ad esempio le sottoscrizioni, i gruppi di risorse e gli account di archiviazione. I tag di indice BLOB forniscono la gestione e l'individuazione degli oggetti sulle risorse del piano dati, ad esempio i BLOB in un account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di come usare l'indice BLOB, vedere [usare l'indice BLOB per gestire e trovare i dati](storage-blob-index-how-to.md).

Informazioni sulla [gestione del ciclo](storage-lifecycle-management-concepts.md) di vita e sull'impostazione di una regola con corrispondenza dell'indice BLOB.

