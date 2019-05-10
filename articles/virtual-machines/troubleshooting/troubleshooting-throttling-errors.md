---
title: Risoluzione degli errori di limitazione delle richieste in Azure | Microsoft Docs
description: Errori di limitazione delle richieste, tentativi e backoff in Calcolo di Azure.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: efa10f5beae64105857b00b186683d491edb00f5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233774"
---
# <a name="troubleshooting-api-throttling-errors"></a>Risoluzione degli errori di limitazione delle richieste delle API 

È possibile applicare la limitazione delle richieste di Calcolo di Azure a livello di sottoscrizione e di area per migliorare le prestazioni complessive del servizio. Viene verificato che tutte le chiamate al provider di risorse di calcolo di Azure che gestisce le risorse nello spazio dei nomi Microsoft.Compute non superino la frequenza massima consentita per le richieste API. Questo documento descrive la limitazione delle richieste API, fornendo informazioni dettagliate su come risolvere i problemi di limitazione delle richieste e procedure consigliate per evitare le limitazioni.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Limitazione delle richieste di Azure Resource Manager e dei provider di risorse  

Come porta di accesso per Azure, Azure Resource Manager esegue l'autenticazione, la convalida di primo grado e la limitazione di tutte le richieste API in ingresso. I limiti per la frequenza delle chiamate di Azure Resource Manager e le intestazioni HTTP delle risposte di diagnostica correlate sono descritti [qui](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits).
 
Quando un client API di Azure riceve un errore di limitazione delle richieste, lo stato HTTP è 429 - Numero eccessivo di richieste. Per stabilire se la limitazione delle richieste viene applicata da Azure Resource Manager o da un provider di risorse sottostante come il provider di risorse di calcolo, esaminare `x-ms-ratelimit-remaining-subscription-reads` per le richieste GET e le intestazioni della risposta `x-ms-ratelimit-remaining-subscription-writes` per le richieste non GET. Se il conteggio delle chiamate rimanenti è prossimo a 0, è stato raggiunto il limite di chiamate generale della sottoscrizione definito da Azure Resource Manager. Le attività di tutti i client della sottoscrizione vengono conteggiate insieme. In caso contrario, la limitazione proviene dal provider di risorse di destinazione (quello interessato dal segmento `/providers/<RP>` dell'URL della richiesta). 

## <a name="call-rate-informational-response-headers"></a>Intestazioni di risposta informativa sulla frequenza delle chiamate 

| Intestazione                            | Formato del valore                           | Esempio                               | Descrizione                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Numero di chiamate API rimanenti per i criteri di limitazione delle richieste che coprono il contenitore di risorse o il gruppo di operazioni che include la destinazione della richiesta                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Numero di chiamate "addebitato" per questa richiesta HTTP rispetto al limite dei criteri applicabili. Generalmente è 1. Le richieste batch, ad esempio per il ridimensionamento di un set di scalabilità di macchine virtuali, possono comportare l'addebito di un numero superiore. |


Si noti che una richiesta API può essere sottoposta a più criteri di limitazione delle richieste. Sarà presente un'intestazione `x-ms-ratelimit-remaining-resource` distinta per ogni criterio. 

Di seguito è riportato un esempio di risposta per l'eliminazione di una richiesta di set di scalabilità di macchine virtuali.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Dettagli dell'errore di limitazione delle richieste

Lo stato HTTP 429 viene generalmente usato per rifiutare una richiesta perché è stato raggiunto un limite di frequenza delle chiamate. Una tipica risposta di errore di limitazione delle richieste dal provider di risorse di calcolo avrà un aspetto simile all'esempio seguente (sono visualizzate solo le intestazioni pertinenti):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Il criterio con un conteggio delle chiamate rimanenti pari a 0 è quello che determina la restituzione dell'errore di limitazione delle richieste. In questo caso, si tratta di `HighCostGet30Min`. Il formato complessivo del corpo della risposta è il formato di errore generale dell'API di Azure Resource Manager (conforme a OData). Il codice di errore principale, `OperationNotAllowed`, è quello usato dal provider di risorse di calcolo per segnalare gli errori di limitazione delle richieste (tra gli altri tipi di errori client). La proprietà `message` degli errori interni contiene una struttura JSON serializzata con i dettagli della violazione relativa alla limitazione delle richieste.

Come illustrato in precedenza, tutti gli errori di limitazione delle richieste includono l'intestazione `Retry-After`, che fornisce il numero minimo di secondi che il client deve attendere prima di ritentare la richiesta. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Analizzatore della frequenza delle chiamate API e degli errori di limitazione
È disponibile una versione di anteprima di una funzionalità di risoluzione dei problemi per l'API del provider di risorse di calcolo. Questi cmdlet di PowerShell forniscono statistiche sulla frequenza delle richieste API per ogni intervallo di tempo per ogni operazione e sulle violazioni delle limitazioni per ogni gruppo di operazioni (criteri):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Le statistiche sulle chiamate dell'API possono offrire informazioni dettagliate molto utili in merito al comportamento dei client di una sottoscrizione e facilitare l'identificazione dei modelli di chiamata che causano la limitazione.

Al momento, un limite dell'analizzatore è il fatto che non includa le richieste per i tipi di risorse disco e snapshot (a supporto dei dischi gestiti). Dato che raccoglie dati dai dati di telemetria del provider di risorse di calcolo, non è inoltre utile per identificare gli errori di limitazione da ARM. Tuttavia, questi errori possono essere identificati facilmente in base alle intestazioni di risposta ARM distintive, come illustrato in precedenza.

I cmdlet di PowerShell usano un'API del servizio REST, che può essere facilmente chiamata direttamente dai client (anche se ancora senza supporto formale). Per visualizzare il formato della richiesta HTTP, eseguire i cmdlet con l'opzione -Debug o esaminare l'esecuzione con Fiddler.


## <a name="best-practices"></a>Procedure consigliate 

- Non eseguire nuovi tentativi per gli errori API del servizio Azure in modo incondizionato e/o immediatamente. Una situazione che si verifica di frequente è quella in cui il codice client entra velocemente in un ciclo di ripetizione dei tentativi quando si verifica un errore che non supporta nuovi tentativi. I nuovi tentativi finiscono per causare il raggiungimento del limite di chiamate consentite per gruppo di operazioni di destinazione e influiscono sugli altri client della sottoscrizione. 
- Nei casi di automazione di API con volumi elevati, è consigliabile implementare preventivamente funzionalità di limitazione automatica delle richieste sul lato client quando il conteggio delle chiamate disponibili per un gruppo di operazioni di destinazione scende sotto una certa soglia. 
- Quando si tiene traccia di operazioni asincrone, rispettare gli hint dell'intestazione Retry-After. 
- Se il codice client richiede informazioni relative a una determinata macchina virtuale, inviare una query direttamente alla macchina virtuale invece di elencare tutte le macchine virtuali nel gruppo di risorse o nell'intera sottoscrizione e quindi selezionare la macchina virtuale necessaria sul lato client. 
- Se il codice client richiede macchine virtuali, dischi e snapshot da una specifica posizione di Azure, usare il formato della query basato sulla posizione invece di inviare una query a tutte le macchine virtuali della sottoscrizione e quindi applicare il filtro in base alla posizione sul lato client: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` invia la query agli endpoint a livello di area del provider di risorse di calcolo. 
-   Soprattutto quando si creano o si aggiornano risorse API, macchine virtuali e set di scalabilità di macchine virtuali, è molto più efficiente tenere traccia dell'operazione asincrona restituita fino al completamento che eseguire il polling sull'URL della risorsa stessa (in base a `provisioningState`).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle linee guida relative alla ripetizione dei tentativi per altri servizi in Azure, vedere [Materiale sussidiario su come eseguire nuovi tentativi per servizi specifici](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
