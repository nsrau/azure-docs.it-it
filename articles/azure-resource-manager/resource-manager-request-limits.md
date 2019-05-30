---
title: Limitazione delle richieste di Azure Resource Manager | Microsoft Docs
description: Viene descritto come usare la limitazione con le richieste di Azure Resource Manager quando sono stati raggiunti i limiti di sottoscrizioni.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b382b9ae35d492b4c779b8f7ee360fb378d54e08
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399709"
---
# <a name="throttling-resource-manager-requests"></a>Limitazione delle richieste di Resource Manager

Per ogni sottoscrizione e tenant di Azure, Resource Manager consente fino a 12.000 richieste di lettura per ora e 1.200 richieste di scrittura per ora. L'ambito di questi limiti è l'ID dell'entità di sicurezza che effettua le richieste e l'ID sottoscrizione o l'ID tenant. Se le richieste provengono da più di un ID dell'entità di sicurezza, il limite per la sottoscrizione o il tenant è superiore a 12.000 e 1.200 richieste per ora.

Le richieste vengono applicate alla sottoscrizione o al tenant. Le richieste di sottoscrizione sono quelle che coinvolgono passando l'ID sottoscrizione, ad esempio il recupero di gruppi di risorse nella sottoscrizione. Le richieste relative al tenant non includono l'ID sottoscrizione, ad esempio il recupero delle posizioni di Azure valide.

Questi limiti si applicano a ogni istanza di Azure Resource Manager. Sono presenti più istanze in ogni area di Azure e Azure Resource Manager viene distribuito a tutte le aree di Azure,  quindi nella pratica i limiti sono effettivamente molto superiori a questi, perché le richieste utente vengono in genere gestite da molte istanze diverse.

Se l'applicazione o script raggiunge questi limiti, è necessario restringere le richieste. Questo articolo illustra come determinare il numero di richieste rimanenti prima di raggiungere il limite e come procedere quando è stato raggiunto il limite.

Quando si raggiunge il limite, viene visualizzato il codice di stato HTTP **429 Too many requests** (429 Troppe richieste).

Graph di risorse di Azure limita il numero di richieste per le operazioni. I passaggi descritti in questo articolo per determinare il numero di richieste rimanenti e la modalità di risposta quando viene raggiunto il limite si applicano anche al grafico di risorse. Tuttavia, risorsa Graph imposta la propria frequenza di limite e reimpostare. Per altre informazioni, vedere [limitare le richieste in Graph di Azure Resource](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Richieste rimanenti
È possibile determinare il numero di richieste rimanenti esaminando le intestazioni di risposta. Le richieste di lettura restituiscono il valore dell'intestazione per il numero di richieste di lettura rimanenti. Scrivere le richieste includono un valore per il numero di richieste di scrittura rimanenti. Nella tabella seguente vengono descritte le intestazioni di risposta che è possibile esaminare per tali valori:

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

```powershell
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

```powershell
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

```azurecli
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

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Attesa prima dell'invio della richiesta successiva
Quando si raggiunge il limite di richieste, Resource Manager restituisce il codice di stato HTTP **429** e un valore **Retry-After** nell'intestazione. Il valore **Retry-After** specifica il numero di secondi durante i quali l'applicazione deve attendere (o restare in sospensione) prima di inviare la richiesta successiva. Se si invia una richiesta prima che sia trascorso il valore per la ripetizione del tentativo, la richiesta non viene elaborata e viene restituito un nuovo valore per la ripetizione del tentativo.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo di PowerShell, vedere [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Controllare i limiti di Resource Manager per una sottoscrizione).
* Per altre informazioni sui limiti e quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
* Per altre informazioni sulla gestione delle richieste REST asincrone, vedere [Track asynchronous Azure operations](resource-manager-async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
