---
title: Riferimento ai dati di monitoraggio archiviazione di Azure | Microsoft Docs
description: Informazioni di riferimento su log e metriche per il monitoraggio dei dati da archiviazione di Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.openlocfilehash: 7ba66441a87e3e02483ae27400f9900d2d052af4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118159"
---
# <a name="azure-storage-monitoring-data-reference"></a>Riferimento ai dati di monitoraggio archiviazione di Azure

Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio per archiviazione di Azure, vedere [monitoraggio di archiviazione di Azure](monitor-storage.md) .

## <a name="metrics"></a>Metriche

Le tabelle seguenti elencano le metriche della piattaforma raccolte per archiviazione di Azure. 

### <a name="capacity-metrics"></a>Metriche della capacità

I valori delle metriche relative alla capacità vengono inviati a Monitoraggio di Azure ogni ora. I valori vengono aggiornati ogni giorno. L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche relative alla capacità è un'ora (PT1H).

Archiviazione di Azure fornisce le metriche seguenti relative alla capacità in Monitoraggio di Azure.

#### <a name="account-level"></a>Livello di account

| Metrica | Descrizione |
| ------------------- | ----------------- |
| UsedCapacity | Quantità di risorse di archiviazione usata dall'account di archiviazione. Per gli account di archiviazione Standard, corrisponde alla somma della capacità usata da BLOB, tabelle, file e code. Per gli account di archiviazione Premium e gli account di archiviazione BLOB corrisponde a BlobCapacity. <br/><br/> Unità: byte <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |

#### <a name="blob-storage"></a>Archiviazione BLOB

| Metrica | Descrizione |
| ------------------- | ----------------- |
| BlobCapacity | Totale di risorse di archiviazione BLOB usato nell'account di archiviazione. <br/><br/> Unità: byte <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 <br/> Dimensioni: **BlobType**e **BlobTier** ([definizione](#metrics-dimensions)) |
| BlobCount    | Numero di oggetti BLOB archiviati nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 <br/> Dimensioni: **BlobType**e **BlobTier** ([definizione](#metrics-dimensions)) |
| ContainerCount    | Numero di contenitori nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| IndexCapacity     | Quantità di memoria usata dall'indice gerarchico di ADLS Gen2 <br/><br/> Unità: byte <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |

#### <a name="table-storage"></a>Archiviazione - Tabelle

| Metrica | Descrizione |
| ------------------- | ----------------- |
| TableCapacity | Quantità di archiviazione tabelle usata dall'account di archiviazione. <br/><br/> Unità: byte <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| TableCount   | Numero di tabelle nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| TableEntityCount | Numero di entità tabella nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |

#### <a name="queue-storage"></a>Archiviazione - Code

| Metrica | Descrizione |
| ------------------- | ----------------- |
| QueueCapacity | Quantità di archiviazione code usata dall'account di archiviazione. <br/><br/> Unità: byte <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| QueueCount   | Numero di code nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| QueueMessageCount | Numero di messaggi non scaduti nella coda nell'account di archiviazione. <br/><br/>Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |

#### <a name="file-storage"></a>Archiviazione file

| Metrica | Descrizione |
| ------------------- | ----------------- |
| FileCapacity | Quantità di archiviazione file usata dall'account di archiviazione. <br/><br/> Unità: byte <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| FileCount   | Numero di file nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |
| FileShareCount | Numero di condivisioni file nell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: media <br/> Esempio di valore: 1024 |

### <a name="transaction-metrics"></a>Metriche di transazione

Le metriche di transazione vengono generate su per tutte le richieste a un account di archiviazione da Archiviazione di Azure a Monitoraggio di Azure. In caso non siano presenti attività nell'account di archiviazione, non ci saranno dati per le metriche di transazione nel periodo. Tutte le metriche di transazione sono disponibili a livello di account e di servizio (archiviazione BLOB, archiviazione tabelle, File di Azure e archiviazione code). L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. Gli intervalli di tempo supportati per tutte le metriche di transazioni sono PT1H e PT1M.

Archiviazione di Azure fornisce le metriche seguenti relative alle transazioni in Monitoraggio di Azure.

| Metrica | Descrizione |
| ------------------- | ----------------- |
| Transazioni | Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensioni applicabili: ResponseType, GeoType, ApiName e Authentication ([definizione](#metrics-dimensions))<br/> Esempio di valore: 1024 |
| Dati in ingresso | Quantità di dati in ingresso. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. <br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Esempio di valore: 1024 |
| Egress | Quantità di dati in uscita. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. <br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Esempio di valore: 1024 |
| SuccessServerLatency | Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency. <br/><br/> Unità: millisecondi <br/> Tipo di aggregazione: media <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Esempio di valore: 1024 |
| SuccessE2ELatency | Latenza end-to-end media di richieste con esito positivo effettuate in un servizio di archiviazione o nell'operazione API specificata. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta. <br/><br/> Unità: millisecondi <br/> Tipo di aggregazione: media <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Esempio di valore: 1024 |
| Disponibilità | Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore totale delle richieste fatturabili e dividendolo per il numero di richieste applicabili, incluse le richieste che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata. <br/><br/> Unità: percentuale <br/> Tipo di aggregazione: media <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Esempio di valore: 99,99 |

<a id="metrics-dimensions" />

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Archiviazione di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure.

| Nome della dimensione | Descrizione |
| ------------------- | ----------------- |
| **BlobType** | Tipo di BLOB solo per le metriche relative ai BLOB. I valori supportati sono **BlockBlob**, **PageBlob**e **Azure Data Lake storage**. Il BLOB di aggiunta è incluso in BlockBlob. |
| **BlobTier** | Archiviazione di Azure offre diversi livelli di accesso, che consentono di archiviare i dati degli oggetti BLOB nel modo più conveniente possibile. Vedere altri dettagli nel [livello BLOB di archiviazione di Azure](../blobs/storage-blob-storage-tiers.md). I valori supportati includono: <br/> <li>A **caldo**: livello frequente</li> <li>**Cool**: livello cool</li> <li>**Archivio**: livello archivio</li> <li>**Premium**: livello Premium per BLOB in blocchi</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: tipi di livello per il BLOB di pagine Premium</li> <li>**Standard**: tipo di livello per il BLOB di pagine standard</li> <li>**Untiered**: tipo di livello per l'account di archiviazione per utilizzo generico V1</li> |
| **GeoType** | Transazione da un cluster primario o secondario. I valori disponibili includono **primario** e **secondario**. È applicabile all'archiviazione con ridondanza geografica e accesso in lettura durante la lettura di oggetti da un tenant secondario. |
| **ResponseType** | Tipo di risposta della transazione. I valori disponibili includono: <br/><br/> <li>**ServerOtherError**: tutti gli altri errori sul lato server, ad eccezione di quelli descritti </li> <li>**ServerBusyError**: richiesta autenticata che ha restituito un codice di stato HTTP 503. </li> <li>**ServerTimeoutError**: richiesta autenticata con timeout che ha restituito un codice di stato HTTP 500. Il timeout si è verificato a causa di un errore del server. </li> <li>**AuthorizationError**: richiesta autenticata che ha avuto esito negativo a causa di un accesso non autorizzato ai dati o di un errore di autorizzazione. </li> <li>**NetworkError**: richiesta autenticata che ha avuto esito negativo a causa di errori di rete. Questo errore si verifica in genere quando un cliente chiude prematuramente una connessione prima della scadenza del timeout. </li><li>**ClientAccountBandwidthThrottlingError**: la richiesta è limitata alla larghezza di banda per superare i [limiti di scalabilità degli account di archiviazione](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: la richiesta è limitata alla frequenza di richieste per il superamento dei [limiti di scalabilità degli account di archiviazione](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: altro errore di limitazione lato client. ClientAccountBandwidthThrottlingError e ClientAccountRequestThrottlingError sono esclusi.</li> <li>**ClientTimeoutError**: richiesta autenticata con timeout che ha restituito un codice di stato HTTP 500. Se il timeout di rete o il timeout della richiesta del client è impostato su un valore inferiore rispetto a quanto previsto dal servizio di archiviazione, si tratta di un timeout previsto. In caso contrario, viene segnalato come ServerTimeoutError. </li> <li>**ClientOtherError**: tutti gli altri errori lato client tranne quelli descritti. </li> <li>**Operazione riuscita**: richiesta riuscita</li> <li> **SuccessWithThrottling**: richiesta riuscite quando un client SMB viene limitato al primo tentativo ma ha esito positivo dopo i tentativi.</li> |
| **ApiName** | Nome dell'operazione. Ad esempio: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Per i nomi di tutte le operazioni, vedere questo [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **autenticazione** | Tipo Authentication usato nelle transazioni. I valori disponibili includono: <br/> <li>**AccountKey**: la transazione viene autenticata con la chiave dell'account di archiviazione.</li> <li>**SAS**: la transazione viene autenticata con firme di accesso condiviso.</li> <li>**OAuth**: la transazione viene autenticata con i token di accesso OAuth.</li> <li>**Anonimo**: la transazione viene richiesta in modo anonimo. Non include le richieste preliminari.</li> <li>**AnonymousPreflight**: la transazione è una richiesta preliminare.</li> |

Per le dimensioni che supportano metriche, è necessario specificare il valore relativo alla dimensione per visualizzare i valori delle metriche corrispondenti. Se, ad esempio, si esamina il valore **Transactions** per le risposte con esito positivo, è necessario filtrare la dimensione **ResponseType** con **Success**. In alternativa, se si esamina il valore **BlobCount** per il BLOB in blocchi, è necessario filtrare la dimensione **BlobType** con **BlockBlob**.

## <a name="resource-logs-preview"></a>Log delle risorse (anteprima)

> [!NOTE]
> Log di archiviazione di Azure in monitoraggio di Azure è disponibile in anteprima pubblica ed è disponibile per l'anteprima dei test in tutte le aree del cloud pubblico. Per eseguire la registrazione nell'anteprima, vedere [Questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Questa anteprima Abilita i log per i BLOB (inclusi Azure Data Lake Storage Gen2), i file, le code, le tabelle, gli account di archiviazione Premium negli account di archiviazione per utilizzo generico V1 e per utilizzo generico V2. gli account di archiviazione di tipo classico non sono supportati.

La tabella seguente elenca le proprietà dei log delle risorse di archiviazione di Azure quando vengono raccolti nei log di monitoraggio di Azure o nell'archiviazione di Azure. Le proprietà descrivono l'operazione, il servizio e il tipo di autorizzazione usato per eseguire l'operazione.

### <a name="fields-that-describe-the-operation"></a>Campi che descrivono l'operazione

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| Proprietà | Descrizione |
|:--- |:---|
|**time** | Ora UTC (Universal Time coordinata) in cui la richiesta è stata ricevuta dalla risorsa di archiviazione. Ad esempio: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | ID risorsa dell'account di archiviazione. Ad esempio: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | Categoria dell'operazione richiesta. Ad esempio: `StorageRead` , `StorageWrite` o `StorageDelete` .|
|**operationName** | Tipo di operazione REST eseguita. <br> Per un elenco completo delle operazioni, vedere l' [argomento analisi archiviazione operazioni registrate e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | La versione del servizio di archiviazione specificata quando è stata effettuata la richiesta. Equivale al valore dell'intestazione **x-ms-version** . Ad esempio: `2017-04-17`.|
|**schemaVersion** | Versione dello schema del log. Ad esempio: `1.0`.|
|**statusCode** | Codice di stato HTTL della richiesta. Se la richiesta viene interrotta, questo valore potrebbe essere impostato su `Unknown` . <br> Ad esempio: `206` |
|**statusText** | Stato dell'operazione richiesta.  Per un elenco completo dei messaggi di stato, vedere l' [argomento analisi archiviazione operazioni registrate e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Nella versione 2017-04-17 e successive, il messaggio di stato `ClientOtherError` non viene utilizzato. Al contrario, questo campo contiene un codice di errore. Ad esempio: `SASSuccess`  |
|**durationMs** | Tempo totale, espresso in millisecondi, per eseguire l'operazione richiesta. È incluso il tempo necessario per leggere la richiesta in ingresso e inviare la risposta al richiedente. Ad esempio: `12`.|
|**callerIpAddress** | Indirizzo IP del richiedente, incluso il numero di porta. Ad esempio: `192.100.0.102:4362`. |
|**correlationId** | ID utilizzato per correlare i log tra le risorse. Ad esempio: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**location** | Percorso dell'account di archiviazione. Ad esempio: `North Europe`. |
|**protocol**|Protocollo utilizzato nell'operazione. Ad esempio: `HTTP` , `HTTPS` , `SMB` o`NFS`|
| **URI** | URI (Uniform Resource Identifier) richiesto. Ad esempio: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campi che descrivono come è stata autenticata l'operazione

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| Proprietà | Descrizione |
|:--- |:---|
|**identità/tipo** | Tipo di autenticazione utilizzato per effettuare la richiesta. Ad esempio: `OAuth` , `SAS Key` , `Account Key` o`Anonymous` |
|**Identity/tokenHash**|Questo campo è riservato solo per uso interno. |
|**autorizzazione/azione** | Azione assegnata alla richiesta. Ad esempio: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**autorizzazione/roleAssignmentId** | ID dell'assegnazione di ruolo. Ad esempio: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorizzazione/roleDefinitionId** | ID di definizione del ruolo. Ad esempio: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**entità/ID** | ID dell'entità di sicurezza. Ad esempio: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**entità/tipo** | Tipo di entità di sicurezza. Ad esempio: `ServicePrincipal`. |
|**richiedente/appID** | ID applicazione del Open Authorization (OAuth) utilizzato come richiedente. <br> Ad esempio: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**richiedente/destinatario** | Destinatari OAuth della richiesta. Ad esempio: `https://storage.azure.com`. |
|**richiedente/objectId** | ID oggetto OAuth del richiedente. In caso di autenticazione Kerberos, rappresenta l'identificatore di oggetto dell'utente autenticato Kerberos. Ad esempio: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**richiedente/tenantId** | ID tenant OAuth dell'identità. Ad esempio: `72f988bf-86f1-41af-91ab-222222222222`.|
|**richiedente/tokenIssuer** | Emittente del token OAuth. Ad esempio: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**richiedente/UPN** | Nome dell'entità utente (UPN) del richiedente. Ad esempio: `someone@contoso.com`. |
|**richiedente/nome utente** | Questo campo è riservato solo per uso interno.|

### <a name="fields-that-describe-the-service"></a>Campi che descrivono il servizio

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| Proprietà | Descrizione |
|:--- |:---|
|**accountName** | Nome dell'account di archiviazione. Ad esempio: `mystorageaccount`.  |
|**requestUrl** | URL richiesto. Ad esempio: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | Valore dell' **intestazione User-Agent** , racchiuso tra virgolette. Ad esempio: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Valore dell'intestazione del **Referrer** . Ad esempio: `http://contoso.com/about.html`.|
|**clientRequestId** | Valore dell'intestazione **x-MS-client-Request-ID** della richiesta. Ad esempio: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**ETag** | Identificatore ETag per l'oggetto restituito, tra virgolette. Ad esempio: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Tempo totale espresso in millisecondi per eseguire l'operazione richiesta. Questo valore non include la latenza di rete (il tempo per leggere la richiesta in ingresso e inviare la risposta al richiedente). Ad esempio: `22`. |
|**serviceType** | Servizio associato alla richiesta. ad esempio `blob`, `table`, `files` o `queue`. |
|**operationCount** | Numero di tutte le operazioni registrate incluse nella richiesta. Questo conteggio inizia con un indice di `0` . Alcune richieste richiedono più di un'operazione, ad esempio una richiesta di copia di un BLOB. La maggior parte delle richieste esegue una sola operazione. Ad esempio: `1`. |
|**requestHeaderSize** | Dimensione dell'intestazione della richiesta espressa in byte. Ad esempio: `578`. <br>Se una richiesta ha esito negativo, questo valore potrebbe essere vuoto. |
|**requestBodySize** | Dimensioni dei pacchetti di richiesta, espressi in byte, letti dal servizio di archiviazione. <br> Ad esempio: `0`. <br>Se una richiesta ha esito negativo, questo valore potrebbe essere vuoto.  |
|**responseHeaderSize** | Dimensione dell'intestazione della risposta espressa in byte. Ad esempio: `216`. <br>Se una richiesta ha esito negativo, questo valore potrebbe essere vuoto.  |
|**responseBodySize** | Dimensioni dei pacchetti della risposta scritti dal servizio di archiviazione, in byte. Se una richiesta ha esito negativo, questo valore può essere vuoto. Ad esempio: `216`.  |
|**requestMd5** | Valore dell'intestazione **Content-MD5** o **x-ms-content-MD5** nella richiesta. Il valore hash MD5 specificato in questo campo rappresenta il contenuto nella richiesta. Ad esempio: `788815fd0198be0d275ad329cafd1830`. <br>Questo campo può essere vuoto.  |
|**serverMd5** | Valore dell'hash MD5 calcolato dal servizio di archiviazione. Ad esempio: `3228b3cf1069a5489b298446321f8521`. <br>Questo campo può essere vuoto.  |
|**lastModifiedTime** | Ora dell'Ultima modifica (LMT) per l'oggetto restituito.  Ad esempio: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Questo campo è vuoto per le operazioni che possono restituire più oggetti. |
|**conditionsUsed** | Elenco delimitato da punti e virgola di coppie chiave-valore che rappresentano una condizione. Le condizioni possono essere le seguenti: <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> Ad esempio: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Valore dell'intestazione Content-Length per la richiesta inviata al servizio di archiviazione. Se la richiesta ha avuto esito positivo, questo valore è uguale a requestBodySize. Se una richiesta ha esito negativo, questo valore potrebbe non essere uguale a requestBodySize o potrebbe essere vuoto. |
|**tlsVersion** | Versione di TLS utilizzata nella connessione della richiesta. Ad esempio: `TLS 1.2`. |
|**smbTreeConnectID** | **TREECONNECTID** SMB (Server Message Block) stabilito al momento della connessione dell'albero. Ad esempio: `0x3` |
|**smbPersistentHandleID** | ID handle permanente da una richiesta di creazione SMB2 che sopravvive alle riconnessioni di rete.  Viene fatto riferimento in [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 come **SMB2_FILEID. Persistente**. Ad esempio: `0x6003f` |
|**smbVolatileHandleID** | ID handle volatile da una richiesta di creazione SMB2 riciclata in riconnessioni di rete.  Viene fatto riferimento in [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 come **SMB2_FILEID. Volatile**. Ad esempio: `0xFFFFFFFF00000065` |
|**smbMessageID** | **MessageID**relativo alla connessione. Ad esempio: `0x3b165` |
|**smbCreditsConsumed** | In ingresso o in uscita utilizzati dalla richiesta, in unità di 64K. Ad esempio: `0x3` |
|**smbCommandDetail** | Ulteriori informazioni su questa richiesta specifica anziché sul tipo generale di richiesta. Ad esempio: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | **Fileid** associato al file o alla directory.  Approssimativamente analogo a un FileId NTFS. Ad esempio: `0x9223442405598953` |
|**smbSessionID** | **SESSIONID** SMB2 stabilito al momento dell'installazione della sessione. Ad esempio: `0x8530280128000049` |
|**UInt32 smbCommandMajor** | Valore nel **comando SMB2_HEADER.** Attualmente, si tratta di un numero compreso tra 0 e 18 inclusi. Ad esempio: `0x6` |
|**smbCommandMinor** | Sottoclasse di **SmbCommandMajor**, laddove appropriato. Ad esempio: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Vedere anche

- Per una descrizione del monitoraggio di archiviazione di Azure, vedere [monitoraggio di archiviazione di Azure](monitor-storage.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [monitoraggio delle risorse di Azure con](../../azure-monitor/insights/monitor-azure-resource.md) monitoraggio di Azure.