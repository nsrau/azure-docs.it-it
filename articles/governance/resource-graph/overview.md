---
title: Panoramica di Azure Resource Graph
description: Informazioni sul servizio Azure Resource Graph, che consente di eseguire query complesse sulle risorse su vasta scala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 28efdabc024fd32c83ba966b15284ec6ff368d4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269289"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Panoramica del servizio Azure Resource Graph

Azure Resource Graph è un servizio di Azure che consente di estendere la gestione delle risorse di Azure fornendo una funzione di esplorazione delle risorse efficiente e ad alte prestazioni con la possibilità di eseguire query su larga scala in tutte le sottoscrizioni e i gruppi di gestione, in modo da poter controllare l'ambiente efficacemente. Queste query forniscono le funzionalità seguenti:

- Eseguire query sulle risorse con filtri, raggruppamenti e ordinamenti complessi basati sulle proprietà delle risorse.
- Esplorare le risorse in modo iterativo in base ai requisiti di governance e convertire l'espressione risultante in una definizione di criteri.
- Valutare l'impatto dell'applicazione di criteri in un ambiente cloud vasto.

In questa documentazione verrà esaminata ogni funzionalità in modo dettagliato.

> [!NOTE]
> Azure Resource Graph è usato dalla nuova esperienza di navigazione "Tutte le risorse" del portale di Azure. Il suo scopo è aiutare i clienti che hanno la necessità di gestire ambienti su larga scala.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>In che modo Resource Graph integra Azure Resource Manager

Attualmente, Azure Resource Manager invia i dati a una cache risorse limitata che rende disponibili diversi campi di risorse, in particolare il nome della risorsa, l'ID, il tipo, il gruppo di risorse, le sottoscrizioni e la posizione. In precedenza per usare varie proprietà delle risorse era necessario effettuare chiamate a ogni singolo provider di risorse e richiedere i dettagli delle proprietà per ogni risorsa.

Con Azure Resource Graph, è possibile accedere a queste proprietà restituite dai provider di risorse senza la necessità di effettuare chiamate singole per ogni provider di risorse. Per un elenco dei tipi di risorsa supportati, cercare un **Sì** nella tabella [Risorse per le distribuzioni in modalità completa](../../azure-resource-manager/complete-mode-deletion.md).

## <a name="the-query-language"></a>Il linguaggio di query

Ora che si conosce meglio Azure Resource Graph, si passerà alla creazione di query.

È importante sapere che il linguaggio di query di Azure Resource Graph è basato sul [linguaggio di query Kusto](../../data-explorer/data-explorer-overview.md) usato da Esplora dati di Azure.

In primo luogo, per informazioni dettagliate sulle operazioni e le funzioni che possono essere usate con Azure Resource Graph, vedere l'articolo sul [linguaggio di query di Azure Resource Graph](./concepts/query-language.md). Per esaminare le risorse, vedere l'articolo sull'[esplorazione delle risorse](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Autorizzazioni in Azure Resource Graph

Per usare Resource Graph, è necessario avere i diritti appropriati nel [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) con almeno l'accesso in lettura alle risorse su cui eseguire le query. Senza almeno le autorizzazioni `read` per l'oggetto o il gruppo di oggetti di Azure, non verranno restituiti risultati.

> [!NOTE]
> Resource Graph usa le sottoscrizioni disponibili a un'entità di sicurezza durante l'accesso. Per visualizzare le risorse di una nuova sottoscrizione aggiunta durante una sessione attiva, l'entità deve aggiornare il contesto. Questa azione viene eseguita automaticamente quando ci si disconnette e si accede nuovamente.

## <a name="throttling"></a>Limitazione

Le query a Resource Graph sono limitate per offrire l'esperienza e i tempi di risposta migliori per tutti i clienti. Se l'organizzazione vuole usare l'API Resource Graph per le query su larga scala e frequenti, usare i commenti al portale dalla pagina Resource Graph. Assicurarsi di specificare il caso aziendale e selezionare la casella di controllo "Microsoft potrà inviare un messaggio di posta elettronica in merito ai commenti inviati" per essere contattati dal team.

## <a name="running-your-first-query"></a>Esecuzione della prima query

Resource Graph supporta l'interfaccia della riga di comando di Azure, Azure PowerShell e Azure SDK per .NET. La query è strutturata nello stesso modo per ogni linguaggio. Informazioni su come abilitare Resource Graph nell'[interfaccia della riga di comando di Azure](first-query-azurecli.md#add-the-resource-graph-extension) e in [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Passaggi successivi

- Eseguire la prima query con l'[interfaccia della riga di comando di Azure](first-query-azurecli.md)
- Eseguire la prima query con [Azure PowerShell](first-query-powershell.md)
- Iniziare con le [query di base](./samples/starter.md)
- Migliorare le conoscenze con le [query avanzate](./samples/advanced.md)