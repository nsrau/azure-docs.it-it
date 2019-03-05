---
title: Utilizzo del provider di risorse API | Microsoft Docs
description: Informazioni di riferimento per l'utilizzo delle risorse API, che recupera le informazioni sull'utilizzo di Azure Stack
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
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: 48002ecb3186218d2cf86e38b43b8e35ae39d240
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338383"
---
# <a name="provider-resource-usage-api"></a>API di utilizzo delle risorse dei provider

Il termine *provider* si applica all'amministratore del servizio e a qualsiasi provider delegati. Operatori di Azure Stack e i provider delegati è possono usare l'API di utilizzo del provider per visualizzare l'utilizzo dei propri tenant diretto. Ad esempio, come illustrato nel diagramma, P0 può chiamare l'API per ottenere informazioni sull'utilizzo in P1 provider e l'utilizzo diretto di P2 e P1 può chiamare per informazioni sull'utilizzo in P3 e P4.

![Modello concettuale della gerarchia di provider](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Riferimenti alle chiamate API
### <a name="request"></a>Richiesta
La richiesta ottiene i dettagli di utilizzo per le sottoscrizioni di richieste e per l'intervallo di tempo richiesto. Non vi è alcun corpo della richiesta.

Questa API di utilizzo è un provider di API, in modo che il chiamante deve essere assegnato un ruolo di proprietario, collaboratore o lettore nella sottoscrizione del provider.

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & api-version = 2015-06-01-preview & continuationToken = {token-value} |

### <a name="arguments"></a>Argomenti
| **Argomento** | **Descrizione** |
| --- | --- |
| *armendpoint* |Endpoint Azure Resource Manager dell'ambiente Azure Stack. La convenzione di Azure Stack è che il nome dell'endpoint Azure Resource Manager è nel formato `https://adminmanagement.{domain-name}`. Ad esempio, per il kit di sviluppo, se il nome di dominio *local.azurestack.external*, quindi l'endpoint di Resource Manager è `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID sottoscrizione dell'utente che effettua la chiamata. |
| *reportedStartTime* |Ora di inizio della query. Il valore per *DateTime* deve essere nel formato Coordinated Universal Time (UTC) e all'inizio dell'ora, ad esempio 13.00. Per l'aggregazione giornaliero, impostare questo valore a mezzanotte UTC. Il formato è *carattere di escape* ISO 8601. Ad esempio, *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, in cui viene sottoposto a escape per i due punti *% 3a* e il segno più viene sottoposto a escape a *% 2b* in modo che sia descrittivo URI. |
| *reportedEndTime* |Ora di fine della query. I vincoli che riguardano *reportedStartTime* si applicano anche a questo argomento. Il valore per *reportedEndTime* non può essere nel futuro o la data corrente. Se si tratta, il risultato viene impostato su "elaborazione non completata." |
| *aggregationGranularity* |Parametro facoltativo che dispone di due valori potenziali discreti: giornaliera e oraria. Come suggeriscono i valori, uno restituisce i dati nella granularità giornaliera e l'altro è una soluzione oraria. L'opzione giornaliera è l'impostazione predefinita. |
| *subscriberId* |l'ID sottoscrizione. Per ottenere i dati filtrati, è necessario l'ID sottoscrizione di un tenant diretto del provider. Se si specifica alcun parametro di ID sottoscrizione, la chiamata restituisce i dati di utilizzo per i tenant diretto del provider. |
| *api-version* |Versione del protocollo utilizzato per effettuare questa richiesta. Questo valore è impostato su *2015-06-01-preview*. |
| *continuationToken* |Token recuperato dall'ultima chiamata al provider di API di utilizzo. Questo token è necessaria quando una risposta è maggiore di 1.000 righe e funge da un segnalibro per lo stato di avanzamento. Se il token non è presente, i dati vengono recuperati dall'inizio della giornata o ora, in base alla granularità passato. |

### <a name="response"></a>Risposta
OTTIENI /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = giornaliera & subscriberId = sub1.1 & api-version = 1.0

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
| *id* |ID univoco dell'aggregazione dell'utilizzo. |
| *nome* |Nome della funzione di aggregazione di utilizzo. |
| *type* |Definizione di risorsa. |
| *subscriptionId* |Identificatore della sottoscrizione dell'utente di Azure Stack. |
| *usageStartTime* |UTC ora di inizio il bucket di utilizzo a cui appartiene questa aggregazione di utilizzo.|
| *usageEndTime* |UTC ora di fine del bucket dell'utilizzo a cui appartiene questa aggregazione di utilizzo. |
| *instanceData* |Coppie chiave-valore di dettagli dell'istanza (in un nuovo formato):<br> *resourceUri*: ID risorsa completo, che include i gruppi di risorse e il nome dell'istanza. <br> *percorso*: Area in cui è stato eseguito il servizio. <br> *i tag*: Tag delle risorse specificati dall'utente. <br> *additionalInfo*: Altri dettagli sulla risorsa che è stata utilizzata, ad esempio, la versione del sistema operativo o immagine del tipo. |
| *quantity* |Quantità di utilizzo delle risorse che si sono verificati in questo intervallo di tempo. |
| *meterId* |ID univoco per la risorsa che è stata utilizzata (chiamato anche *ResourceID*). |


## <a name="retrieve-usage-information"></a>Recuperare le informazioni sull'utilizzo

### <a name="powershell"></a>PowerShell

Per generare i dati di utilizzo, è necessario disporre di risorse che sono in esecuzione e in uso il sistema, ad esempio, una macchina virtuale attiva o un account di archiviazione che contiene alcuni dati e così via. Se non si è certi se sono presenti risorse in esecuzione in Azure Stack Marketplace, distribuire una macchina virtuale (VM) e verificare la macchina virtuale monitoraggio pannello per assicurarsi che è in esecuzione. Usare i cmdlet di PowerShell seguenti per visualizzare i dati di utilizzo:

1. [Installare PowerShell per Azure Stack.](azure-stack-powershell-install.md)
2. [Configurare l'utente di Azure Stack](user/azure-stack-powershell-configure-user.md) o il [dell'operatore di Azure Stack](azure-stack-powershell-configure-admin.md) ambiente di PowerShell 
3. Per recuperare i dati di utilizzo, usare il [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet di PowerShell:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>API REST

È possibile raccogliere informazioni sull'utilizzo per le sottoscrizioni eliminate chiamando il servizio Microsoft.Commerce.Admin. 

**Per restituire tutti gli utilizzi di tenant per eliminati per gli utenti attivi:**

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-preview |

**Per restituire l'utilizzo per il tenant di active o deleted:**

| **Metodo** | **URI della richiesta** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ id-sottoscrizione} & api-version = 2015-06-01-preview |


## <a name="next-steps"></a>Passaggi successivi
[Utilizzo delle risorse tenant riferimento all'API](azure-stack-tenant-resource-usage-api.md)

[Domande frequenti relative all'uso](azure-stack-usage-related-faq.md)
