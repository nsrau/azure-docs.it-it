---
title: Applicazione Azure Insights Snapshot Debugger aggiornamento per le app .NET | Microsoft Docs
description: Come aggiornare Snapshot Debugger alla versione più recente nei servizi app Azure o tramite pacchetti NuGet
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899845"
---
# <a name="upgrading-the-snapshot-debugger"></a>Aggiornamento del Snapshot Debugger

Per fornire la migliore sicurezza possibile per i dati, Microsoft si allontana da TLS 1,0 e TLS 1,1, che risultano vulnerabili a utenti malintenzionati determinati. Se si usa una versione precedente dell'estensione del sito, sarà necessario un aggiornamento per continuare a funzionare. Questo documento illustra i passaggi necessari per aggiornare snapshot debugger alla versione più recente. Esistono due percorsi di aggiornamento principali a seconda che sia stato abilitato il Snapshot Debugger usando un'estensione del sito o se è stato usato un SDK/NuGet aggiunto all'applicazione. Entrambi i percorsi di aggiornamento sono descritti di seguito. 

## <a name="upgrading-the-site-extension"></a>Aggiornamento dell'estensione del sito

Se è stato abilitato snapshot debugger usando l'estensione del sito, è possibile eseguire facilmente l'aggiornamento usando la procedura seguente:

1. Accedere al portale di Azure.
2. Passare alla risorsa in cui è abilitato Application Insights e snapshot debugger. Per un'app Web, ad esempio, passare alla risorsa del servizio app:

   ![Screenshot delle singole risorse del servizio App denominate DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Dopo aver eseguito la navigazione alla risorsa, fare clic su Application Insights nel pannello Panoramica:

   ![Screenshot di tre pulsanti. Il pulsante centrale con nome Application Insights è selezionato](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Viene aperto un nuovo pannello con le impostazioni correnti. A meno che non si desideri modificare le impostazioni, è possibile lasciarle invariate. Il pulsante **applica** nella parte inferiore del pannello non è abilitato per impostazione predefinita e sarà necessario attivare o disattivare una delle impostazioni per attivare il pulsante. Non è necessario modificare le impostazioni effettive, ma è possibile modificare l'impostazione e quindi modificarla immediatamente. Si consiglia di abilitare o disabilitare l'impostazione del profiler e quindi di selezionare **applica**.

   ![Screenshot della pagina di configurazione del servizio app Application Insights con il pulsante Applica evidenziato in rosso](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Quando si fa clic su **applica**, verrà chiesto di confermare le modifiche.

    > [!NOTE]
    > Il sito verrà riavviato come parte del processo di aggiornamento.

   ![Screenshot del prompt dei comandi applica monitoraggio del servizio app. Nella casella di testo viene visualizzato il messaggio: "verranno apportate modifiche alle impostazioni dell'app e verranno installati gli strumenti per collegare la risorsa Application Insights all'app Web. Il sito verrà riavviato. Continuare?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Fare clic su **Sì** per applicare le modifiche. Durante il processo verrà visualizzata una notifica che indica che sono state applicate le modifiche:

   ![Screenshot del messaggio Apply Changes-aggiornamento delle estensioni visualizzato nell'angolo superiore destro](./media/snapshot-debugger-upgrade/updating-extensions.png)

Al termine, verrà visualizzata la notifica **"modifiche applicate"** .

   ![Screenshot del messaggio che indica che le modifiche sono state applicate](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Il sito è stato aggiornato ed è pronto per l'uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Aggiornamento Snapshot Debugger mediante SDK/NuGet

Se l'applicazione usa una versione di `Microsoft.ApplicationInsights.SnapshotCollector` sotto la versione 1.3.1, sarà necessario aggiornarla a una versione più [recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) per continuare a funzionare.
