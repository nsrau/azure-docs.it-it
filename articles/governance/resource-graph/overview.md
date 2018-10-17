---
title: Panoramica di Azure Resource Graph
description: Azure Resource Graph è un servizio disponibile in Azure che consente di eseguire query complesse sulle risorse su vasta scala.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162101"
---
# <a name="what-is-azure-resource-graph"></a>Informazioni su Azure Resource Graph

Azure Resource Graph è un servizio di Azure che consente di estendere la gestione delle risorse di Azure fornendo una funzione di esplorazione delle risorse efficiente e ad alte prestazioni con la possibilità di eseguire query su larga scala in tutte le sottoscrizioni e i gruppi di gestione, in modo da poter controllare l'ambiente efficacemente. Queste query forniscono le capacità seguenti:

- Eseguire query sulle risorse con filtri, raggruppamenti e ordinamenti complessi basati sulle proprietà delle risorse.
- Esplorare le risorse in modo iterativo in base ai requisiti di governance e convertire l'espressione risultante in una definizione di criteri.
- Valutare l'impatto dell'applicazione di criteri in un ambiente cloud vasto.

In questa documentazione verrà esaminata ogni capacità in modo dettagliato.

> [!NOTE]
> Azure Resource Graph è usato dalla nuova esperienza di navigazione "Tutte le risorse" del portale di Azure. Il suo scopo è aiutare i clienti che hanno la necessità di gestire ambienti su larga scala.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>In che modo Resource Graph integra Azure Resource Manager

Attualmente, Azure Resource Manager invia i dati a una cache risorse limitata che espone diversi campi di risorse, in particolare il nome della risorsa, l'ID, il tipo, il gruppo di risorse, le sottoscrizioni e la posizione. Oggi, se si volesse lavorare con altre proprietà delle risorse, sarebbe necessario effettuare chiamate a ogni singolo provider di risorse e richiedere i dettagli delle proprietà per ogni risorsa.

Con Azure Resource Graph, è possibile accedere a queste proprietà restituite dai provider di risorse senza la necessità di effettuare chiamate singole per ogni provider di risorse.

## <a name="the-query-language"></a>Il linguaggio di query

Ora che si conosce meglio Azure Resource Graph, si passerà alla creazione di query.

È importante sapere che il linguaggio di query di Azure Resource Graph è basato sul [linguaggio di query di Esplora dati di Azure](../../data-explorer/data-explorer-overview.md).

In primo luogo, per informazioni dettagliate sulle operazioni e le funzioni che possono essere usate con Azure Resource Graph, vedere l'articolo sul [linguaggio di query di Azure Resource Graph](./concepts/query-language.md). Per esaminare le risorse, vedere l'articolo sull'[esplorazione delle risorse](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Autorizzazioni in Azure Resource Graph

Per usare Resource Graph, è necessario essere autorizzati attraverso il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) e avere almeno l'accesso in lettura alle risorse su cui eseguire le query. Se non si possiedono le autorizzazioni `read` per il gruppo di gestione, la sottoscrizione, il gruppo di risorse o le singole risorse, l'elemento non sarà restituito nei risultati delle query di Resource Graph.

## <a name="running-your-first-query"></a>Esecuzione della prima query

Resource Graph supporta sia l'interfaccia della riga di comando di Azure che Azure PowerShell. Il componente per le query è strutturato nello stesso modo indipendentemente dal linguaggio utilizzato. Il supporto per Azure Resource Graph non è ancora disponibile per impostazione predefinita in nessun SDK, perciò è necessario caricare un modulo o un'estensione per ottenere i comandi necessari.
Informazioni su come abilitare Resource Graph nell'[interfaccia della riga di comando di Azure](first-query-azurecli.md#add-the-resource-graph-extension) e in [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Passaggi successivi

- Eseguire la prima query con l'[interfaccia della riga di comando di Azure](first-query-azurecli.md)
- Eseguire la prima query con [Azure PowerShell](first-query-powershell.md)
- Iniziare con le [query di base](./samples/starter.md)
- Migliorare le conoscenze con le [query avanzate](./samples/advanced.md)