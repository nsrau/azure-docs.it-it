---
title: Utilizzo delle risorse API del tenant | Documenti Microsoft
description: Riferimento API, utilizzo di risorse cui recuperare le informazioni sull'utilizzo dello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 8472d8ce733c07641a7fa6d53aeb6909cd709990
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048391"
---
# <a name="tenant-resource-usage-api"></a>Utilizzo delle risorse API del tenant

Un tenant può utilizzare l'API Tenant per visualizzare i dati di utilizzo delle risorse del tenant. Questa API è coerenza con l'API di utilizzo di Azure (attualmente in anteprima privata).

È possibile usare il cmdlet di Windows PowerShell **Get-UsageAggregates** per ottenere i dati di utilizzo, ad esempio in Azure.

## <a name="api-call"></a>Chiamata API
### <a name="request"></a>Richiesta
La richiesta ottiene i dettagli di utilizzo per le sottoscrizioni richieste e per l'intervallo di tempo richiesto. Non vi è alcun corpo della richiesta.

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-Anteprima & continuationToken = {token-value} |

### <a name="arguments"></a>Argomenti
| **Argomento** | **Descrizione** |
| --- | --- |
| *Armendpoint* |Endpoint di gestione risorse Azure dell'ambiente dello Stack di Azure. La convenzione di Stack di Azure è che il nome dell'endpoint di Azure Resource Manager sia nel formato `https://management.{domain-name}`. Ad esempio, per il kit di sviluppo, il nome di dominio è local.azurestack.external e quindi l'endpoint di gestione risorse è `https://management.local.azurestack.external`. |
| *subId* |ID sottoscrizione dell'utente che effettua la chiamata. È possibile utilizzare questa API solo per query per l'utilizzo di una singola sottoscrizione. I provider possono utilizzare l'API di utilizzo di risorse di Provider sull'utilizzo di query per tutti i tenant. |
| *reportedStartTime* |Ora di inizio della query. Il valore per *DateTime* deve essere in formato UTC e all'inizio dell'ora, ad esempio 13.00. Per l'aggregazione giornaliera, impostare questo valore alla mezzanotte ora UTC. Il formato è *escape* ISO 8601, ad esempio, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, due punti in cui viene sottoposta a escape a % 3a e più in modo che risulti URI descrittivo alla versione 2b % di escape. |
| *reportedEndTime* |Ora di fine della query. I vincoli che si applicano a *reportedStartTime* si applicano anche a questo argomento. Il valore per *reportedEndTime* non può essere in futuro. |
| *aggregationGranularity* |Parametro facoltativo che dispone di due valori potenziali discreti: giornaliera e oraria. Come suggeriscono valori, uno restituisce i dati nella granularità giornaliera e l'altro è una risoluzione oraria. L'opzione giornaliero è il valore predefinito. |
| *api-version* |Versione del protocollo utilizzato per effettuare questa richiesta. È necessario utilizzare 2015-06-01-preview. |
| *continuationToken* |Token recuperato all'ultima chiamata al provider di API di utilizzo. Questo token è necessaria quando una risposta è maggiore di 1.000 righe e funge da un segnalibro per lo stato di avanzamento. Se non è presente, i dati vengono recuperati dall'inizio del giorno o ora, in base alla granularità passati. |

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
| *id* |ID univoco della funzione di aggregazione di utilizzo |
| *nome* |Nome della funzione di aggregazione di utilizzo |
| *type* |Definizione di risorsa |
| *subscriptionId* |Identificatore della sottoscrizione dell'utente Azure |
| *usageStartTime* |UTC ora di inizio del bucket di utilizzo a cui appartiene questa aggregazione di utilizzo |
| *usageEndTime* |UTC ora di fine dell'intervallo di utilizzo a cui appartiene questa aggregazione di utilizzo |
| *instanceData* |Coppie chiave-valore di dettagli di istanze (in un nuovo formato):<br>  *resourceUri*: ID di risorsa, inclusi i gruppi di risorse e il nome di istanza completo <br>  *percorso*: area in cui è stato eseguito il servizio <br>  *tag*: i tag delle risorse specificate dall'utente <br>  *additionalInfo*: ulteriori informazioni sulle risorse che è stata utilizzata, ad esempio, il tipo di versione o l'immagine del sistema operativo |
| *Quantità* |Quantità di utilizzo delle risorse che si sono verificati in questo periodo di tempo |
| *meterId* |ID univoco per la risorsa che è stata utilizzata (chiamato anche *ResourceID*) |


## <a name="next-steps"></a>Passaggi successivi
[API di utilizzo delle risorse dei provider](azure-stack-provider-resource-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)

