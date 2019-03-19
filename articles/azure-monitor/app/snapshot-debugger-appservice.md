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
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57876244"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Abilitare il Debugger di Snapshot per le app .NET nel servizio App di Azure

Debugger di snapshot di attualmente funziona per le app ASP.NET e ASP.NET Core in esecuzione nel servizio App di Azure nei piani di servizio Windows.

## <a id="installation"></a> Abilitare il Debugger di Snapshot
Per abilitare Snapshot Debugger per un'app, seguire le istruzioni seguenti. Se si esegue un tipo diverso di servizi di Azure, ecco le istruzioni per l'abilitazione di Snapshot Debugger su altre piattaforme supportate:
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi di Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali o fisiche locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

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

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

