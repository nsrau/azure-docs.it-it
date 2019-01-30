---
title: API di utilizzo delle risorse del tenant | Microsoft Docs
description: Riferimento per l'utilizzo delle risorse API, quale recuperare le informazioni sull'utilizzo di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: db0cdfce5903b8866985b0545494351e2796d8e2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244624"
---
# <a name="tenant-resource-usage-api"></a>API di utilizzo delle risorse del tenant

Un tenant può usare l'API Tenant per visualizzare i dati di utilizzo delle risorse del tenant. Questa API è coerenza con l'API di utilizzo di Azure (attualmente in anteprima privata).

È possibile usare il cmdlet di Windows PowerShell **Get-UsageAggregates** per ottenere i dati di utilizzo, ad esempio in Azure.

## <a name="api-call"></a>Chiamata all'API
### <a name="request"></a>Richiesta
La richiesta ottiene i dettagli di utilizzo per le sottoscrizioni di richieste e per l'intervallo di tempo richiesto. Non vi è alcun corpo della richiesta.

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-preview & continuationToken = {token-value} |

### <a name="arguments"></a>Argomenti
| **Argomento** | **Descrizione** |
| --- | --- |
| *armendpoint* |Endpoint Azure Resource Manager dell'ambiente Azure Stack. La convenzione di Azure Stack è che il nome dell'endpoint di Azure Resource Manager è nel formato `https://management.{domain-name}`. Ad esempio, per il kit di sviluppo, il nome di dominio è local.azurestack.external, quindi l'endpoint di Resource Manager è `https://management.local.azurestack.external`. |
| *subId* |ID sottoscrizione dell'utente che effettua la chiamata. È possibile usare questa API solo per query per l'utilizzo di una singola sottoscrizione. I provider possono utilizzare l'API di utilizzo di Provider di risorse all'utilizzo di query per tutti i tenant. |
| *reportedStartTime* |Ora di inizio della query. Il valore per *DateTime* deve essere in formato UTC e all'inizio dell'ora, ad esempio 13.00. Per l'aggregazione giornaliero, impostare questo valore a mezzanotte UTC. Il formato è *carattere di escape* ISO 8601, ad esempio, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, due punti in cui viene sottoposto a escape a % 3a e oltre a viene sottoposto a escape alla versione 2b % in modo che sia descrittivo URI. |
| *reportedEndTime* |Ora di fine della query. I vincoli che riguardano *reportedStartTime* si applicano anche a questo argomento. Il valore per *reportedEndTime* non può essere una data futura. |
| *aggregationGranularity* |Parametro facoltativo che dispone di due valori potenziali discreti: giornaliera e oraria. Come suggeriscono i valori, uno restituisce i dati nella granularità giornaliera e l'altro è una soluzione oraria. L'opzione giornaliera è l'impostazione predefinita. |
| *api-version* |Versione del protocollo utilizzato per effettuare questa richiesta. È necessario usare 2015-06-01-preview. |
| *continuationToken* |Token recuperato dall'ultima chiamata al provider di API di utilizzo. Questo token è necessaria quando una risposta è maggiore di 1.000 righe e funge da un segnalibro per lo stato di avanzamento. Se non è presente, i dati vengono recuperati dall'inizio del giorno oppure ora, in base alla granularità passato. |

### <a name="response"></a>Risposta
OTTIENI /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = giornaliera & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Dettagli della risposta
| **Argomento** | **Descrizione** |
| --- | --- |
| *id* |ID univoco dell'aggregazione dell'utilizzo |
| *nome* |Nome della funzione di aggregazione dell'utilizzo |
| *type* |Definizione di risorsa |
| *subscriptionId* |Identificatore della sottoscrizione dell'utente di Azure |
| *usageStartTime* |UTC ora di inizio di bucket di utilizzo a cui appartiene questa aggregazione di utilizzo |
| *usageEndTime* |UTC ora di fine del bucket dell'utilizzo a cui appartiene questa aggregazione di utilizzo |
| *instanceData* |Coppie chiave-valore di dettagli dell'istanza (in un nuovo formato):<br>  *resourceUri*: ID risorsa completo, inclusi i gruppi di risorse e il nome di istanza <br>  *percorso*: Area in cui è stato eseguito il servizio <br>  *i tag*: Tag delle risorse specificate dall'utente <br>  *additionalInfo*: Digitare altri dettagli sulla risorsa che è stata utilizzata, ad esempio, versione del sistema operativo o immagine |
| *quantity* |Quantità di utilizzo delle risorse che si sono verificati in questo intervallo di tempo |
| *meterId* |ID univoco per la risorsa che è stata utilizzata (chiamato anche *ResourceID*) |


## <a name="next-steps"></a>Passaggi successivi
[API di utilizzo delle risorse dei provider](azure-stack-provider-resource-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)

