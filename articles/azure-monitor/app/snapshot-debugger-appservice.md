---
title: Abilitare Snapshot Debugger per le app .NET nel servizio app Azure | Microsoft Docs
description: Abilitare Snapshot Debugger per le app .NET nel servizio app Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f6eb6376075337edd7656e4bc83b5b7fddde479
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899901"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Abilitare Snapshot Debugger per le app .NET nel servizio app Azure

Snapshot Debugger attualmente funziona per le app ASP.NET e ASP.NET Core in esecuzione nel servizio app Azure nei piani di servizio Windows.

## <a id="installation"></a>Abilita Snapshot Debugger
Per abilitare Snapshot Debugger per un'app, seguire le istruzioni riportate di seguito. Se si esegue un tipo diverso di servizio di Azure, di seguito sono riportate le istruzioni per abilitare Snapshot Debugger su altre piattaforme supportate:
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi Service Fabric di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Computer fisici o virtuali locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se si usa una versione di anteprima di .NET Core, seguire le istruzioni per [abilitare snapshot debugger per gli altri ambienti](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) per includere prima di tutto il pacchetto NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) con l'applicazione, e quindi completare le altre istruzioni riportate di seguito. 

Application Insights Snapshot Debugger è preinstallato come parte del runtime dei servizi app, ma è necessario attivarlo per ottenere gli snapshot per l'app del servizio app. Dopo aver distribuito un'app, anche se è stata inclusa la Application Insights SDK nel codice sorgente, attenersi alla procedura seguente per abilitare snapshot debugger.

1. Passare al riquadro **Servizi app** nel portale di Azure.
2. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che entrambe le opzioni per Snapshot Debugger siano **attivate**.

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

4. Snapshot Debugger ora è abilitata usando un'impostazione dell'app servizi app.

    ![Impostazione app per Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Disabilitare Snapshot Debugger

Seguire gli stessi passaggi di **Abilitazione di snapshot debugger**, ma impostare entrambe le opzioni per snapshot debugger su **disattivato**.
Si consiglia di Snapshot Debugger abilitata in tutte le app per semplificare la diagnostica delle eccezioni delle applicazioni.

## <a name="next-steps"></a>Passaggi successivi

- Generare il traffico all'applicazione che può attivare un'eccezione. Attendere da 10 a 15 minuti per l'invio degli snapshot all'istanza di Application Insights.
- Vedere [snapshot](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) nel portale di Azure.
- Per informazioni sulla risoluzione dei problemi di Snapshot Debugger, vedere [snapshot debugger risoluzione dei](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)problemi.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

