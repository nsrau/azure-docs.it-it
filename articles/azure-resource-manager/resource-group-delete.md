---
title: Eliminare un gruppo di risorse e le risorse - Azure Resource Manager
description: Descrive l'ordine di eliminazione delle risorse in Azure Resource Manager quando viene eliminato un gruppo di risorse. Descrive i codici di risposta e il modo in cui Resource Manager li gestisce in modo da determinare se l'eliminazione è stata completata.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b8c4fdc942af291e912a4c1e74d1292279cf9f8c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132330"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Eliminazione di un gruppo di risorse in Azure Resource Manager

Questo articolo descrive l'ordine di eliminazione delle risorse in Azure Resource Manager quando viene eliminato un gruppo di risorse.

## <a name="determine-order-of-deletion"></a>Determinare l'ordine di eliminazione

Quando si elimina un gruppo di risorse, Resource Manager determina l'ordine di eliminazione delle risorse. L'ordine è il seguente:

1. Vengono eliminate tutte le risorse figlio (annidate).

2. Successivamente vengono eliminate le risorse che gestiscono altre risorse. Per una risorsa può essere impostata la proprietà `managedBy` per indicare che è gestita da una risorsa diversa. Quando questa proprietà è impostata, la risorsa che gestisce l'altra risorsa viene eliminata prima delle altre risorse.

3. Le risorse rimanenti vengono eliminate dopo le due categorie precedenti.

## <a name="resource-deletion"></a>Eliminazione delle risorse

Dopo aver determinato l'ordine, Resource Manager genera un'operazione DELETE per ogni risorsa. Attende il completamento di eventuali dipendenze prima di procedere.

Per le operazioni sincrone, i codici di risposta con esito positivo previsti sono:

* 200
* 204
* 404

Per le operazioni asincrone, il codice di risposta con esito positivo previsto è 202. Resource Manager monitora l'intestazione del percorso o l'intestazione dell'operazione azure-async per determinare lo stato dell'operazione di eliminazione asincrona.
  
### <a name="errors"></a>Errors

Quando un'operazione di eliminazione restituisce un errore, Resource Manager ritenta la chiamata DELETE. La ripetizione dei tentativi avviene per i codici di stato 5xx, 429 e 408. Per impostazione predefinita, l'intervallo di tempo per la ripetizione dei tentativi è di 15 minuti.

## <a name="after-deletion"></a>Dopo l'eliminazione

Resource Manager invia una chiamata GET per ogni risorsa che ha tentato di eliminare. La risposta della chiamata GET è previsto sia 404. Quando Resource Manager riceve un codice 404, considera l'eliminazione completata correttamente. Resource Manager rimuove la risorsa dalla propria cache.

Tuttavia, se la chiamata GET sulla risorsa restituisce 200 o 201, Resource Manager ricrea la risorsa.

### <a name="errors"></a>Errors

Se l'operazione GET restituisce un errore, Resource Manager esegue un nuovo tentativo GET per i codici di errore seguenti:

* Minore di 100
* 408
* 429
* Maggiore di 500

Per altri codici di errore, Resource Manager considera l'eliminazione della risorsa non riuscita.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui concetti correlati a Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).
* Per i comandi di eliminazione, vedere [PowerShell](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup), [Interfaccia della riga di comando di Azure](/cli/azure/group?view=azure-cli-latest#az-group-delete) e [API REST](/rest/api/resources/resourcegroups/delete).
