---
title: Monitorare le risorse delegate su larga scalaMonitor delegated resources at scale
description: Informazioni su come usare in modo efficace i log di Monitoraggio di Azure in modo scalabile tra i tenant dei clienti che stai gestendo.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985202"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorare le risorse delegate su larga scalaMonitor delegated resources at scale

I provider di servizi potrebbero aver integrato più tenant di clienti per la gestione risorse delegate di Azure. [Azure Lighthouse](../overview.md) consente ai provider di servizi di eseguire operazioni su larga scala tra più tenant contemporaneamente, rendendo più efficienti le attività di gestione.

Questo argomento illustra come usare [i log](../../azure-monitor/platform/data-platform-logs.md) di Monitoraggio di Azure in modo scalabile tra i tenant dei clienti che stai gestendo.

## <a name="create-log-analytics-workspaces"></a>Creare aree di lavoro di Log AnalyticsCreate Log Analytics workspaces

Per raccogliere dati, è necessario creare aree di lavoro di Log Analytics.In order to collect data, you'll need to create Log Analytics workspaces. Queste aree di lavoro di Log Analytics sono ambienti univoci per i dati raccolti da Monitoraggio di Azure.These Log Analytics workspaces are environments for data collected by Azure Monitor. Ogni area di lavoro ha un proprio repository di dati e una propria configurazione, mentre le origini dati e le soluzioni sono configurate per l'archiviazione dei dati in un'area di lavoro specifica.

È consigliabile creare queste aree di lavoro direttamente nei tenant dei clienti. In questo modo i loro dati rimangono nei loro inquilini piuttosto che essere esportati nel tuo. Ciò consente inoltre il monitoraggio centralizzato di tutte le risorse o servizi supportati da Log Analytics, offrendo una maggiore flessibilità sui tipi di dati monitorati.

È possibile creare un'area di lavoro di Log Analytics usando il portale di [Azure,](../../azure-monitor/learn/quick-create-workspace.md)l'interfaccia [della riga di comando](../../azure-monitor/learn/quick-create-workspace-cli.md)di Azure o Azure [PowerShell.](../../azure-monitor/learn/quick-create-workspace-posh.md)

## <a name="deploy-policies-that-log-data"></a>Distribuire criteri che registrano i datiDeploy policies that log data

Dopo aver creato le aree di lavoro di Log Analytics, è possibile distribuire [Criteri di Azure](../../governance/policy/index.yml) tra le gerarchie dei clienti in modo che i dati di diagnostica vengano inviati all'area di lavoro appropriata in ogni tenant. I criteri esatti da distribuire possono variare a seconda dei tipi di risorse che si desidera monitorare.

Per ulteriori informazioni sulla creazione di criteri, vedere [Esercitazione: Creare e gestire criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md). Questo [strumento di community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) fornisce uno script che consente di creare criteri per monitorare i tipi di risorse specifici scelti.

Dopo aver determinato i criteri da distribuire, è possibile [distribuirli alle sottoscrizioni delegate su larga scala.](policy-at-scale.md)

## <a name="analyze-the-gathered-data"></a>Analizzare i dati raccolti

Dopo aver distribuito i criteri, i dati verranno registrati nelle aree di lavoro di Log Analytics create in ogni tenant del cliente. Per ottenere informazioni dettagliate su tutti i clienti [gestiti,](../../azure-monitor/platform/workbooks-overview.md) è possibile usare strumenti come Monitoraggio cartelle di lavoro di Azure per raccogliere e analizzare informazioni da più origini dati.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Monitoraggio di Azure](../../azure-monitor/index.yml).
- Informazioni sui log di Monitoraggio di [Azure](../../azure-monitor/platform/data-platform-logs.md).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
