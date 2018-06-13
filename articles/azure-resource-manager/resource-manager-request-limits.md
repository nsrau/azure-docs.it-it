---
title: Limiti di richieste di Azure Resource Manager | Microsoft Docs
description: Viene descritto come usare la limitazione con le richieste di Azure Resource Manager quando sono stati raggiunti i limiti di sottoscrizioni.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2018
ms.author: tomfitz
ms.openlocfilehash: f3dcb0c5036b2cfc38ef2a6a16269a8697bbd9e6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358864"
---
# <a name="throttling-resource-manager-requests"></a>Limitazione delle richieste di Resource Manager
Per ogni sottoscrizione e tenant, Resource Manager definisce un limite di 15.000 richieste di lettura e 1.200 richieste di scrittura al giorno. Questi limiti si applicano a ogni istanza di Azure Resource Manager. Sono presenti più istanze in ogni area di Azure e Azure Resource Manager viene distribuito a tutte le aree di Azure,  quindi nella pratica i limiti sono effettivamente molto superiori a questi, perché le richieste utente vengono in genere gestite da molte istanze diverse.

Se l'applicazione o script raggiunge questi limiti, è necessario restringere le richieste. In questo articolo viene illustrato come determinare il numero di richieste rimanenti prima di raggiungere il limite e come rispondere in caso di raggiungimento.

Quando si raggiunge il limite, viene visualizzato il codice di stato HTTP **429 Too many requests** (429 Troppe richieste).

Il numero di richieste ha come ambito la sottoscrizione o il tenant. Se si dispone di più applicazioni simultanee che effettuano richieste nella sottoscrizione, le richieste da tali applicazioni vengono aggiunte insieme in modo da determinare il numero di richieste rimanenti.

Le richieste nell'ambito della sottoscrizione sono quelle che comportano il passaggio dell'ID sottoscrizione, ad esempio il recupero dei gruppi di risorse nella sottoscrizione. Le richieste nell'ambito del tenant non includono l'ID sottoscrizione, ad esempio il recupero delle posizioni di Azure valide.

## <a name="remaining-requests"></a>Richieste rimanenti
È possibile determinare il numero di richieste rimanenti esaminando le intestazioni di risposta. Ogni richiesta include i valori per il numero di richieste di scrittura e lettura rimanenti. Nella tabella seguente vengono descritte le intestazioni di risposta che è possibile esaminare per tali valori:

| Intestazione risposta | DESCRIZIONE |
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

In alternativa, se desidera visualizzare le richieste rimanenti per il debug, è possibile fornire il parametro **-Debug** nel cmdlet **PowerShell**.

```powershell
Get-AzureRmResourceGroup -Debug
```

Tale parametro restituisce molti valori, incluso il valore di risposta seguente:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
```

Per ottenere i limiti di scrittura, usare un'operazione di scrittura: 

```powershell
New-AzureRmResourceGroup -Name myresourcegroup -Location westus -Debug
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
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '14998'
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

* Per altre informazioni sui limiti e quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
* Per altre informazioni sulla gestione delle richieste REST asincrone, vedere [Track asynchronous Azure operations](resource-manager-async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
