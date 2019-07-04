---
title: Abilitare il Debugger di Snapshot per le app .NET nel servizio App di Azure | Microsoft Docs
description: Abilitare il Debugger di Snapshot per le app .NET nel servizio App di Azure
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: cf06c7c21d30fb107baee0d43b33b221c7da2773
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439871"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Abilitare il Debugger di Snapshot per le app .NET nel servizio App di Azure

Debugger di snapshot di attualmente funziona per le app ASP.NET e ASP.NET Core in esecuzione nel servizio App di Azure nei piani di servizio Windows.

## <a id="installation"></a> Abilitare il Debugger di Snapshot
Per abilitare Snapshot Debugger per un'app, seguire le istruzioni seguenti. Se si esegue un tipo diverso di servizi di Azure, ecco le istruzioni per l'abilitazione di Snapshot Debugger su altre piattaforme supportate:
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi di Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali o fisiche locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se si usa una versione di anteprima di .NET Core, seguire le istruzioni relative [Abilita Snapshot Debugger per altri ambienti](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) prima di tutto per includere il [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet con l'applicazione del pacchetto e quindi completare il resto delle procedure seguenti. 

Debugger di Snapshot di Application Insights è pre-installato come parte del runtime di servizi App, ma è necessario abilitarlo e gli snapshot get per l'App del servizio app. Dopo aver distribuito un'app, anche se è stato incluso Application Insights SDK nel codice sorgente, attenersi alla procedura seguente per abilitare il debugger di snapshot.

1. Passare al riquadro **Servizi app** nel portale di Azure.
2. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Inoltre assicurarsi che entrambe le opzioni del Debugger di Snapshot siano **su**.

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

4. Snapshot Debugger è ora abilitata usando un'impostazione di App di servizi App.

    ![Impostazione dell'App per Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Disabilitare il Debugger di Snapshot

Seguire gli stessi passaggi come per **Abilita Snapshot Debugger**, ma passare entrambe le opzioni per il Debugger di Snapshot da **Off**.
È consigliabile la presenza di Snapshot Debugger abilitata su tutte le App per facilitare la diagnostica di eccezioni dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione che può generare un'eccezione. Attendere quindi 10-15 minuti per gli snapshot da inviare all'istanza di Application Insights.
- Visualizzare [snapshot](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) nel portale di Azure.
- Per informazioni sulla risoluzione dei problemi del Debugger di Snapshot, vedere [risoluzione dei problemi di Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

