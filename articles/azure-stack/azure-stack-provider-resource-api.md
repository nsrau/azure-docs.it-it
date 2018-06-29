---
title: Utilizzo del provider di risorse API | Documenti Microsoft
description: Riferimento per l'utilizzo delle risorse API, che recupera le informazioni sull'utilizzo di Azure Stack
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
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 46e46cfea621f99e150446fcc75b71feb468fa49
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052699"
---
# <a name="provider-resource-usage-api"></a>API di utilizzo delle risorse dei provider
Il termine *provider* si applica all'amministratore del servizio e a qualsiasi provider di delegati. Gli operatori di Stack Azure e provider delegati possono utilizzare l'utilizzo del provider di API per visualizzare l'utilizzo dei propri tenant diretto. Ad esempio, come illustrato nel diagramma, P0 può chiamare l'API per ottenere informazioni sul P1 utilizzo del provider e l'utilizzo diretto del P2 e P1 può chiamare per informazioni sull'utilizzo in P3 e P4.

![Modello concettuale della gerarchia di provider](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Riferimento API chiamata
### <a name="request"></a>Richiesta
La richiesta ottiene i dettagli di utilizzo per le sottoscrizioni richieste e per l'intervallo di tempo richiesto. Non vi è alcun corpo della richiesta.

Questo utilizzo API è un provider di API, in modo che il chiamante deve essere assegnato un ruolo di proprietario, collaboratore o Reader nella sottoscrizione del provider.

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & api-version = 2015-06-01-Anteprima & continuationToken = {token-value} |

### <a name="arguments"></a>Argomenti
| **Argomento** | **Descrizione** |
| --- | --- |
| *armendpoint* |Endpoint di gestione risorse Azure dell'ambiente dello Stack di Azure. La convenzione di Stack di Azure è che il nome dell'endpoint di Azure Resource Manager sia nel formato `https://adminmanagement.{domain-name}`. Ad esempio, per il kit di sviluppo, se il nome di dominio *local.azurestack.external*, quindi l'endpoint di gestione risorse è `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID sottoscrizione dell'utente che effettua la chiamata. |
| *reportedStartTime* |Ora di inizio della query. Il valore per *DateTime* deve essere nel formato Coordinated Universal Time (UTC) e all'inizio dell'ora, ad esempio 13.00. Per l'aggregazione giornaliera, impostare questo valore alla mezzanotte ora UTC. Il formato è *escape* ISO 8601. Ad esempio *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, in cui i due punti di escape per *% 3a* e il segno più viene sottoposto a escape a *% 2b* in modo che risulti URI descrittivo. |
| *reportedEndTime* |Ora di fine della query. I vincoli che si applicano a *reportedStartTime* si applicano anche a questo argomento. Il valore per *reportedEndTime* non può essere successiva alla data o alla data corrente. In caso affermativo, il risultato è impostato su "durante l'elaborazione non completato." |
| *aggregationGranularity* |Parametro facoltativo che dispone di due valori potenziali discreti: giornaliera e oraria. Come suggeriscono valori, uno restituisce i dati nella granularità giornaliera e l'altro è una risoluzione oraria. L'opzione giornaliero è il valore predefinito. |
| *subscriberId* |l'ID sottoscrizione. Per ottenere i dati filtrati, è necessario l'ID sottoscrizione di un tenant diretto del provider. Se si specifica alcun parametro di ID sottoscrizione, la chiamata restituisce i dati di utilizzo per i tenant diretti del provider. |
| *api-version* |Versione del protocollo utilizzato per effettuare questa richiesta. Questo valore è impostato su *2015-06-01-preview*. |
| *continuationToken* |Token recuperato all'ultima chiamata al provider di utilizzo API. Questo token è necessaria quando una risposta è maggiore di 1.000 righe e funge da un segnalibro per lo stato di avanzamento. Se il token non è presente, i dati vengono recuperati dall'inizio del giorno o ora, in base alla granularità passati. |

### <a name="response"></a>Risposta
OTTIENI /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = ogni giorno & subscriberId = sub1.1 & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

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
| *instanceData* |Coppie chiave-valore di dettagli di istanze (in un nuovo formato):<br> *resourceUri*: completo ID di risorsa, che include i gruppi di risorse e il nome dell'istanza. <br> *percorso*: area in cui è stato eseguito il servizio. <br> *tag*: i tag delle risorse specificate dall'utente. <br> *additionalInfo*: ulteriori dettagli sulla risorsa che è stata utilizzata, ad esempio, il tipo di versione o l'immagine del sistema operativo. |
| *Quantità* |Quantità di utilizzo delle risorse che si sono verificati in questo periodo di tempo. |
| *meterId* |ID univoco per la risorsa che è stata utilizzata (chiamato anche *ResourceID*). |


## <a name="retrieve-usage-information"></a>Recuperare le informazioni sull'utilizzo

Per generare i dati di utilizzo, è necessario disporre di risorse che sono in esecuzione e che usano attivamente il sistema, ad esempio, una macchina virtuale attiva o un account di archiviazione che contiene alcuni dati e così via. Se non si è certi se si ha delle risorse in esecuzione in Azure Marketplace di Stack, distribuire una macchina virtuale (VM) e verifica la macchina virtuale monitoraggio pannello per assicurarsi che è in esecuzione. Utilizzare i cmdlet di PowerShell seguenti per visualizzare i dati di utilizzo:

1. [Installare PowerShell per Azure dello Stack.](azure-stack-powershell-install.md)
2. [Configurare l'utente di Azure Stack](user/azure-stack-powershell-configure-user.md) o il [dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) ambiente di PowerShell 
3. Per recuperare i dati di utilizzo, usare il [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet di PowerShell:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```

## <a name="next-steps"></a>Passaggi successivi
[Utilizzo delle risorse tenant riferimento API](azure-stack-tenant-resource-usage-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)
