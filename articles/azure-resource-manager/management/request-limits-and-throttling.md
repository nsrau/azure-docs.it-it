---
title: Limiti delle richieste e limitazioni
description: Viene descritto come usare la limitazione con le richieste di Azure Resource Manager quando sono stati raggiunti i limiti di sottoscrizioni.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239369"
---
# <a name="throttling-resource-manager-requests"></a>Limitazione delle richieste di Resource Manager

Questo articolo descrive come Azure Resource Manager limita le richieste. Viene illustrato come tenere traccia del numero di richieste rimanenti prima di raggiungere il limite e come rispondere quando è stato raggiunto il limite.

La limitazione delle richieste avviene a due livelli. Azure Resource Manager limita le richieste per la sottoscrizione e il tenant. Se la richiesta è inferiore ai limiti di limitazione per la sottoscrizione e il tenant, Resource Manager instrada la richiesta al provider di risorse. Il provider di risorse applica limiti di limitazione personalizzati alle operazioni. L'immagine seguente mostra come viene applicata la limitazione delle richieste quando una richiesta passa dall'utente a Azure Resource Manager e al provider di risorse.

![Limitazione delle richiesteRequest throttling](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limiti di sottoscrizione e tenantSubscription and tenant limits

Ogni operazione a livello di sottoscrizione e a livello di tenant è soggetta a limiti di limitazione. Le richieste di sottoscrizione sono quelle che implicano il passaggio dell'ID sottoscrizione, ad esempio il recupero dei gruppi di risorse nella sottoscrizione. Le richieste relative al tenant non includono l'ID sottoscrizione, ad esempio il recupero delle posizioni di Azure valide.

I limiti di limitazione di limitazione predefiniti all'ora sono illustrati nella tabella seguente.

| Scope | Operazioni | Limite |
| ----- | ---------- | ------- |
| Subscription | reads | 12000 |
| Subscription | deletes | 15000 |
| Subscription | writes | 1200 |
| Tenant | reads | 12000 |
| Tenant | writes | 1200 |

L'ambito di questi limiti è l'entità di sicurezza (utente o applicazione) che effettua le richieste e l'ID sottoscrizione o l'ID tenant. Se le richieste provengono da più di un'entità di sicurezza, il limite per la sottoscrizione o il tenant è superiore a 12.000 e 1.200 richieste per ora.

Questi limiti si applicano a ogni istanza di Azure Resource Manager. Sono presenti più istanze in ogni area di Azure e Azure Resource Manager viene distribuito a tutte le aree di Azure,  Quindi, in pratica, i limiti sono superiori a questi limiti. Le richieste di un utente vengono in genere gestite da istanze diverse di Azure Resource Manager.The requests from a user are usually handled by different instances of Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limiti del provider di risorse

I provider di risorse applicano i propri limiti di limitazione. Poiché Resource Manager limita in base all'ID entità e all'istanza di Resource Manager, il provider di risorse potrebbe ricevere più richieste rispetto ai limiti predefiniti nella sezione precedente.

In questa sezione vengono illustrati i limiti di limitazione di alcuni provider di risorse ampiamente utilizzati.

### <a name="storage-throttling"></a>Limitazione del servizio di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Limitazione della larghezza di banda della rete

Il provider di risorse Microsoft.Network applica i limiti di limitazione seguenti:The Microsoft.Network resource provider applies the following throttle limits:

| Operazione | Limite |
| --------- | ----- |
| scrivere/eliminare (PUT) | 1000 per 5 minuti |
| lettura (GET) | 10000 per 5 minuti |

### <a name="compute-throttling"></a>Limitazione del calcolo

Per informazioni sui limiti di limitazione per le operazioni di calcolo, vedere Risoluzione degli errori di [limitazione delle API - Calcolo](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Per controllare le istanze di macchine virtuali all'interno di un set di scalabilità di macchine virtuali, utilizzare le operazioni Set di [scalabilità](/rest/api/compute/virtualmachinescalesetvms)di macchine virtuali . Ad esempio, usare le macchine virtuali del set di [scalabilità](/rest/api/compute/virtualmachinescalesetvms/list) delle macchine virtuali - Elenco con parametri per controllare lo stato di alimentazione delle istanze della macchina virtuale. Questa API riduce il numero di richieste.

### <a name="azure-resource-graph-throttling"></a>Limitazione di Azure Resource GraphAzure Resource Graph throttling

[Azure Resource Graph](../../governance/resource-graph/overview.md) limita il numero di richieste alle relative operazioni. I passaggi descritti in questo articolo per determinare le richieste rimanenti e come rispondere quando viene raggiunto il limite si applicano anche a Resource Graph. Tuttavia, Diagramma risorse imposta il proprio limite e la frequenza di reimpostazione. Per ulteriori informazioni, vedere [Intestazioni di limitazione di Resource Graph](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="error-code"></a>Codice di errore

Quando si raggiunge il limite, viene visualizzato il codice di stato HTTP **429 Too many requests** (429 Troppe richieste). La risposta include un valore **Retry-After,** che specifica il numero di secondi di attesa o sospensione dell'applicazione prima di inviare la richiesta successiva. Se si invia una richiesta prima che sia trascorso il valore per la ripetizione del tentativo, la richiesta non viene elaborata e viene restituito un nuovo valore per la ripetizione del tentativo.

Dopo aver attendito il tempo specificato, è anche possibile chiudere e riaprire la connessione ad Azure.After waiting for specified time, you can also close and reopen your connection to Azure. Reimpostando la connessione, è possibile connettersi a un'istanza diversa di Azure Resource Manager.By resetting the connection, you may connect to a different instance of Azure Resource Manager.

Se si usa Azure SDK, l'SDK potrebbe avere una configurazione di ripetizione dei tentativi automatica. Per altre informazioni, vedere Indicazioni sui tentativi per i servizi di Azure.For more information, see [Retry guidance for Azure services](/azure/architecture/best-practices/retry-service-specific).

Alcuni provider di risorse restituiscono 429 per segnalare un problema temporaneo. Il problema potrebbe essere una condizione di overload che non è direttamente causata dalla richiesta. In alternativa, potrebbe rappresentare un errore temporaneo sullo stato della risorsa di destinazione o della risorsa dipendente. Ad esempio, il provider di risorse di rete restituisce 429 con il codice di errore **RetryableErrorDueToAnotherOperation** quando la risorsa di destinazione è bloccata da un'altra operazione. Per determinare se l'errore proviene dalla limitazione o da una condizione temporanea, visualizzare i dettagli dell'errore nella risposta.

## <a name="remaining-requests"></a>Richieste rimanenti

È possibile determinare il numero di richieste rimanenti esaminando le intestazioni di risposta. Le richieste di lettura restituiscono un valore nell'intestazione per il numero di richieste di lettura rimanenti. Le richieste di scrittura includono un valore per il numero di richieste di scrittura rimanenti. Nella tabella seguente vengono descritte le intestazioni di risposta che è possibile esaminare per tali valori:

| Intestazione risposta | Descrizione |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Richieste di lettura rimanenti nell'ambito della sottoscrizione. Questo valore viene restituito nelle operazioni di lettura. |
| x-ms-ratelimit-remaining-subscription-writes |Richieste di scrittura rimanenti nell'ambito della sottoscrizione. Questo valore viene restituito nelle operazioni di scrittura. |
| x-ms-ratelimit-remaining-tenant-reads |Richieste di lettura rimanenti nell'ambito del tenant |
| x-ms-ratelimit-remaining-tenant-writes |Richieste di scrittura rimanenti nell'ambito del tenant |
| x-ms-ratelimit-remaining-subscription-resource-requests |Richieste di tipo di risorsa rimanenti nell'ambito della sottoscrizione.<br /><br />Questo valore di intestazione viene restituito solo se un servizio ha superato il limite predefinito. Resource Manager aggiunge questo valore invece delle richieste di lettura o scrittura per la sottoscrizione. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Richieste di raccolta di tipo di risorsa rimanenti nell'ambito della sottoscrizione.<br /><br />Questo valore di intestazione viene restituito solo se un servizio ha superato il limite predefinito. Questo valore fornisce il numero di richieste di raccolta rimanenti (elenco di risorse). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Richieste di tipo di risorsa rimanenti nell'ambito del tenant.<br /><br />Questa intestazione viene aggiunta solo per le richieste a livello di tenant e solo se un sevizio ha superato il limite predefinito. Resource Manager aggiunge questo valore invece delle richieste di lettura o scrittura per il tenant. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Richieste di raccolta di tipo di risorsa rimanenti nell'ambito del tenant.<br /><br />Questa intestazione viene aggiunta solo per le richieste a livello di tenant e solo se un sevizio ha superato il limite predefinito. |

Il provider di risorse può anche restituire intestazioni di risposta con informazioni sulle richieste rimanenti. Per informazioni sulle intestazioni di risposta restituite dal provider di risorse Compute, vedere Intestazioni di [risposta informative sulla frequenza delle](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)chiamate .

## <a name="retrieving-the-header-values"></a>Recupero dei valori di intestazione

Il recupero di questi valori di intestazione nel codice o nello script non è diverso rispetto al recupero di qualsiasi valore di intestazione. 

Ad esempio, in **C#** viene recuperato il valore di intestazione da un oggetto **HttpWebResponse** di nome **response** con il codice seguente:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

In **PowerShell** viene recuperato il valore di intestazione da un'operazione Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Per un esempio completo di PowerShell, vedere [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Controllare i limiti di Resource Manager per una sottoscrizione).

Per visualizzare le richieste rimanenti per il debug, è possibile specificare il parametro **-Debug** nel cmdlet **PowerShell**.

```powershell
Get-AzResourceGroup -Debug
```

Tale parametro restituisce molti valori, incluso il valore di risposta seguente:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Per ottenere i limiti di scrittura, usare un'operazione di scrittura: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Tale opzione restituisce numerosi valori, inclusi i seguenti:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

In **Interfaccia della riga di comando di Azure** il valore di intestazione viene recuperato tramite l'opzione più dettagliata.

```azurecli
az group list --verbose --debug
```

Tale opzione restituisce numerosi valori, inclusi i seguenti:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Per ottenere i limiti di scrittura, usare un'operazione di scrittura: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Tale opzione restituisce numerosi valori, inclusi i seguenti:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo di PowerShell, vedere [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Controllare i limiti di Resource Manager per una sottoscrizione).
* Per altre informazioni sui limiti e quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Per altre informazioni sulla gestione delle richieste REST asincrone, vedere [Track asynchronous Azure operations](async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
