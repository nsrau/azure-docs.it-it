---
title: Utilizzo del provider di risorse API | Documenti Microsoft
description: Riferimento per l'utilizzo delle risorse API, che recupera informazioni sull'utilizzo di Azure Stack
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b6055923-b6a6-45f0-8979-225b713150ae
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: alfredop
ms.openlocfilehash: 0c45ce3bc93945ed8700464beebabcda07e8d77c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="provider-resource-usage-api"></a>API di utilizzo delle risorse dei provider
Il termine *provider* si applica all'amministratore del servizio e a qualsiasi provider di delegati. Provider delegati e gli operatori di Azure Stack consente l'utilizzo del provider di API per visualizzare l'utilizzo dei propri tenant diretto. Utilizzo diretto del P2 e P1 possono chiamare per informazioni sull'utilizzo P3 e P4, ad esempio, come illustrato nel diagramma, P0 può chiamare l'API per ottenere informazioni sul P1 utilizzo del provider.

![Modello concettuale della gerarchia di provider](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Chiamata di riferimento sulle API
### <a name="request"></a>Richiesta
La richiesta ottiene i dettagli di utilizzo per le sottoscrizioni di richieste e per l'intervallo di tempo richiesto. Non vi è alcun corpo della richiesta.

Questo utilizzo API è un provider di API, pertanto il chiamante deve essere assegnato un ruolo di proprietario, collaboratore o lettore nella sottoscrizione del provider.

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argomenti
| **Argomento** | **Descrizione** |
| --- | --- |
| *armendpoint* |Endpoint di gestione delle risorse di Azure dell'ambiente dello Stack di Azure. La convenzione di Stack di Azure è che il nome dell'endpoint di gestione risorse di Azure è nel formato `https://adminmanagement.{domain-name}`. Ad esempio, per il kit di sviluppo, se il nome di dominio è *local.azurestack.external*, quindi l'endpoint di gestione risorse è `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID sottoscrizione dell'utente che effettua la chiamata. |
| *reportedStartTime* |Ora di inizio della query. Il valore per *DateTime* deve essere in formato Coordinated Universal Time (UTC) e all'inizio dell'ora, ad esempio, 13:00. Per l'aggregazione giornaliera, impostare questo valore alla mezzanotte ora UTC. Il formato è *escape* ISO 8601. Ad esempio, *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, in cui viene sottoposta a escape per i due punti *% 3a* e il segno più viene sottoposta a escape a *% 2b* in modo che sia descrittivo URI. |
| *reportedEndTime* |Ora di fine della query. I vincoli che si applicano a *reportedStartTime* si applicano anche a questo argomento. Il valore per *reportedEndTime* non può essere nel futuro o alla data corrente. Questo caso, il risultato è impostato su "l'elaborazione non completato." |
| *aggregationGranularity* |Parametro facoltativo che dispone di due valori potenziali discreti: giornaliera e oraria. Come suggeriscono i valori, uno restituisce i dati nella granularità giornaliera e l'altro è una risoluzione oraria. L'opzione giornaliera è il valore predefinito. |
| *subscriberId* |l'ID sottoscrizione. Per ottenere i dati filtrati, è necessario l'ID sottoscrizione di un tenant diretto del provider. Se viene specificato alcun parametro di ID di sottoscrizione, la chiamata restituisce dati di utilizzo per i tenant diretto del provider. |
| *api-version* |Versione del protocollo utilizzato per effettuare questa richiesta. Questo valore è impostato su *2015-06-01-preview*. |
| *continuationToken* |Token recuperato dall'ultima chiamata al provider di utilizzo API. Questo token è necessaria quando una risposta è maggiore di 1.000 righe e funge da un segnalibro per lo stato di avanzamento. Se il token non è presente, i dati vengono recuperati dall'inizio del giorno o ora, in base al livello di dettaglio passati. |

### <a name="response"></a>Risposta
OTTIENI /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = ogni giorno & subscriberId = sub1.1 & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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
| *id* |ID univoco della funzione di aggregazione di utilizzo. |
| *nome* |Nome della funzione di aggregazione di utilizzo. |
| *type* |Definizione di risorsa. |
| *subscriptionId* |Identificatore della sottoscrizione dell'utente dello Stack di Azure. |
| *usageStartTime* |UTC ora di inizio il bucket di utilizzo a cui appartiene questa aggregazione di utilizzo.|
| *usageEndTime* |UTC ora di fine dell'intervallo di utilizzo a cui appartiene questa aggregazione di utilizzo. |
| *instanceData* |Coppie chiave-valore di dettagli di istanze (in un nuovo formato):<br> *resourceUri*: completo di ID di risorsa, che include i gruppi di risorse e il nome dell'istanza. <br> *percorso*: area in cui è stato eseguito il servizio. <br> *tag*: i tag delle risorse specificate dall'utente. <br> *additionalInfo*: più dettagli sulla risorsa che è stata utilizzata, ad esempio, il tipo di versione o l'immagine del sistema operativo. |
| *quantity* |Quantità di utilizzo delle risorse che si sono verificati in questo periodo di tempo. |
| *meterId* |ID univoco per la risorsa che è stata utilizzata (detto anche *ResourceID*). |

## <a name="next-steps"></a>Passaggi successivi
[Utilizzo delle risorse di riferimento all'API tenant](azure-stack-tenant-resource-usage-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)
