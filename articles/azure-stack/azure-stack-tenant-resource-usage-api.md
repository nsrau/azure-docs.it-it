---
title: Utilizzo delle risorse API del tenant | Documenti Microsoft
description: Riferimento per l'utilizzo delle risorse API, ovvero recuperare informazioni sull'utilizzo dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2016
ms.author: alfredop
ms.openlocfilehash: f2eaf1c766d6c86741cf0fd561c131eacb34d782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tenant-resource-usage-api"></a>Utilizzo delle risorse API tenant
Un tenant può utilizzare l'API Tenant per visualizzare i dati di utilizzo delle risorse del tenant. Questa API è coerenza con l'API di utilizzo di Azure (attualmente in anteprima privata).

È possibile utilizzare il cmdlet di Windows PowerShell **Get UsageAggregates** per ottenere dati di utilizzo, ad esempio in Azure.

## <a name="api-call"></a>Chiamata API
### <a name="request"></a>Richiesta
La richiesta ottiene i dettagli di utilizzo per le sottoscrizioni di richieste e per l'intervallo di tempo richiesto. Non vi è alcun corpo della richiesta.

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-preview & continuationToken = {valore del token} |

### <a name="arguments"></a>Argomenti
| **Argomento** | **Descrizione** |
| --- | --- |
| *Armendpoint* |Endpoint di gestione delle risorse di Azure dell'ambiente dello Stack di Azure. La convenzione di Stack di Azure è che il nome dell'endpoint di gestione risorse di Azure è nel formato `https://management.{domain-name}`. Ad esempio, per il kit di sviluppo, il nome di dominio è local.azurestack.external, quindi l'endpoint di gestione risorse è `https://management.local.azurestack.external`. |
| *subId* |ID sottoscrizione dell'utente che effettua la chiamata. È possibile utilizzare questa API solo per query per l'utilizzo di una singola sottoscrizione. Provider possono utilizzare l'API di utilizzo risorse di Provider per l'utilizzo di query per tutti i tenant. |
| *reportedStartTime* |Ora di inizio della query. Il valore per *DateTime* deve essere in formato UTC e all'inizio dell'ora, ad esempio, 13:00. Per l'aggregazione giornaliera, impostare questo valore alla mezzanotte ora UTC. Il formato è *escape* ISO 8601, ad esempio 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, due punti in cui viene sottoposta a escape a % 3a e più in modo che sia descrittivo URI alla versione 2b % di escape. |
| *reportedEndTime* |Ora di fine della query. I vincoli che si applicano a *reportedStartTime* si applicano anche a questo argomento. Il valore per *reportedEndTime* non può essere in futuro. |
| *aggregationGranularity* |Parametro facoltativo che dispone di due valori potenziali discreti: giornaliera e oraria. Come suggeriscono i valori, uno restituisce i dati nella granularità giornaliera e l'altro è una risoluzione oraria. L'opzione giornaliera è il valore predefinito. |
| *versione dell'API* |Versione del protocollo utilizzato per effettuare questa richiesta. È necessario utilizzare 2015-06-01-preview. |
| *continuationToken* |Token recuperato dall'ultima chiamata al provider di API di utilizzo. Questo token è necessaria quando una risposta è maggiore di 1.000 righe e funge da un segnalibro per lo stato di avanzamento. Se non è presente, i dati vengono recuperati dall'inizio del giorno o ora, in base al livello di dettaglio passati. |

### <a name="response"></a>Response
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
| *subscriptionId* |Identificatore della sottoscrizione dell'utente di Azure |
| *usageStartTime* |UTC ora di inizio del bucket di utilizzo a cui appartiene questa aggregazione di utilizzo |
| *usageEndTime* |UTC ora di fine dell'intervallo di utilizzo a cui appartiene questa aggregazione di utilizzo |
| *instanceData* |Coppie chiave-valore di dettagli di istanze (in un nuovo formato):<br>  *resourceUri*: ID di risorsa, inclusi gruppi di risorse e il nome di istanza completo <br>  *percorso*: area in cui è stato eseguito il servizio <br>  *tag*: i tag delle risorse specificate dall'utente <br>  *additionalInfo*: ulteriori informazioni sulle risorse che è stata utilizzata, ad esempio, il tipo di versione o l'immagine del sistema operativo |
| *quantità* |Quantità di utilizzo delle risorse che si sono verificati in questo periodo di tempo |
| *ID misuratore* |ID univoco per la risorsa che è stata utilizzata (detto anche *ResourceID*) |

## <a name="next-steps"></a>Passaggi successivi
[API di utilizzo delle risorse dei provider](azure-stack-provider-resource-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)

