---
title: Analisi delle modifiche in Monitoraggio di Azure per le macchine virtuali
description: Monitoraggio di Azure per le macchine virtuali integrazione con l'integrazione dell'analisi delle modifiche dell'applicazione consente di visualizzare tutte le modifiche apportate a una macchina virtuale che potrebbe avere influenzato le prestazioni it.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711555"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Analisi delle modifiche in Monitoraggio di Azure per le macchine virtuali
Monitoraggio di Azure per le macchine virtuali integrazione con l'integrazione [dell'analisi delle modifiche dell'applicazione](../app/change-analysis.md) consente di visualizzare tutte le modifiche apportate a una macchina virtuale che potrebbe avere influenzato le prestazioni it.

## <a name="overview"></a>Panoramica
Si supponga di disporre di una macchina virtuale in esecuzione lenta e di voler verificare se le modifiche recenti alla configurazione potrebbero influire sulle prestazioni. È possibile visualizzare le prestazioni della macchina virtuale usando Monitoraggio di Azure per le macchine virtuali e verificare che sia presente un aumento dell'utilizzo della memoria nell'ultima ora. L'analisi delle modifiche può consentire di determinare se le modifiche apportate alla configurazione in questo periodo di tempo sono state provocate da questo aumento.

Il servizio di analisi delle modifiche dell'applicazione aggrega le modifiche da [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) , nonché le modifiche alle proprietà annidate, ad esempio le regole di sicurezza di rete da Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Abilitazione dell'analisi delle modifiche
Per eseguire l'onboarding dell'analisi delle modifiche in Monitoraggio di Azure per le macchine virtuali, è necessario registrare il provider di risorse *Microsoft. ChangeAnalysis* . La prima volta che si avvia Monitoraggio di Azure per le macchine virtuali o l'analisi delle modifiche dell'applicazione nel portale di Azure, questo provider di risorse verrà automaticamente registrato. L'analisi delle modifiche dell'applicazione è un servizio gratuito che non comporta un sovraccarico delle prestazioni sulle risorse.

## <a name="view-change-analysis"></a>Visualizza analisi modifiche
L'analisi delle modifiche è disponibile nella scheda **prestazioni** o **mappa** del monitoraggio di Azure per le macchine virtuali selezionando l'opzione **Cambia** . 

[![Esaminare le modifiche](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Fare clic sul pulsante **Esamina modifiche** per avviare la pagina di analisi delle modifiche dell'applicazione filtrata per la macchina virtuale. È possibile esaminare le modifiche elencate per verificare se sono presenti eventuali problemi che potrebbero aver causato il problema. Se non si è certi di una modifica particolare, è possibile fare riferimento alla colonna **modifica** per determinare la persona che ha apportato la modifica.

[![Dettagli modifiche](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [analisi delle modifiche dell'applicazione](../app/change-analysis.md).
- Scopri di più sul [grafico delle risorse di Azure](../../governance/resource-graph/how-to/get-resource-changes.md). 

