---
title: Esperienza dei log semplici in monitoraggio di Azure (anteprima) | Microsoft Docs
description: La semplice esperienza dei log consente di creare query di base in monitoraggio di Azure senza interagire direttamente con KQL.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: bff80b7083f2fb2e35af7db652849d527a5b8ba4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963447"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Esperienza dei log semplici in monitoraggio di Azure (anteprima)
Monitoraggio di Azure offre un' [esperienza avanzata](get-started-portal.md) per la creazione di [query di log](log-query-overview.md) con il linguaggio KQL. È possibile che non sia necessaria tutta la potenza di KQL, ma è preferibile un'esperienza semplificata per i requisiti di query di base. La semplice esperienza dei log consente di creare query di base senza interagire direttamente con KQL. È anche possibile usare i log semplici come strumento di apprendimento per KQL, in quanto sono necessarie query più sofisticate.

> [!NOTE]
> I log semplici vengono attualmente implementati come test solo per Cosmos DB e Key Vault. Condividi la tua esperienza con Microsoft tramite [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) per aiutarci a determinare se la funzionalità verrà espansa e rilasciata.


## <a name="scope"></a>Ambito
L'esperienza dei log semplici recupera i dati dalla tabella *AzureDiagnostics*, *AzureMetrics*e *AzureActivity* per la risorsa selezionata. 

## <a name="using-simple-logs"></a>Uso di log semplici
Passare a qualsiasi Cosmos DB o Key Vault nella sottoscrizione di Azure con [le impostazioni di diagnostica configurate per raccogliere i log in un'area di lavoro log Analytics](../platform/diagnostic-logs-stream-log-store.md). Fare clic su **log** nel menu **monitoraggio** per aprire la semplice esperienza dei log.

![Menu](media/simple-logs/menu.png)

Selezionare un **campo** e un **operatore** e specificare un **valore** per il confronto. Fare **+** clic e specificare **e/o** per aggiungere altri criteri.

![Criteri](media/simple-logs/criteria.png)

Fare clic su **Esegui** per visualizzare i risultati della query.

## <a name="view-and-edit-kql"></a>Visualizzare e modificare KQL
Selezionare **editor di query** per aprire il KQL generato dalla query log semplici nell'esperienza log Analytics completa. 

![Editor di query](media/simple-logs/query-editor.png)

È possibile modificare direttamente il KQL e usare altre funzionalità in Log Analytics, ad esempio i filtri, per perfezionare ulteriormente i risultati.

![Modifica KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Passaggi successivi

- Completare un'esercitazione sull' [uso di log Analytics nel portale di Azure](get-started-portal.md).
- Completare un'esercitazione sulla [scrittura di query di log](get-started-portal.md).
