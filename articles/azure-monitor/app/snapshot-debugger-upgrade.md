---
title: Applicazione Azure Insights Snapshot Debugger aggiornamento per le app .NET | Microsoft Docs
description: Come aggiornare Snapshot Debugger alla versione più recente nei servizi app Azure o tramite pacchetti NuGet
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51642dde3de16f2bed3ca247e573237effb30917
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935938"
---
# <a name="upgrading-the-snapshot-debugger"></a>Aggiornamento del Snapshot Debugger

Per fornire la migliore sicurezza possibile per i dati, Microsoft si allontana da TLS 1,0 e TLS 1,1, che risultano vulnerabili a utenti malintenzionati determinati. Se si usa una versione precedente dell'estensione del sito, sarà necessario un aggiornamento per continuare a funzionare. Questo documento illustra i passaggi necessari per aggiornare snapshot debugger alla versione più recente. Esistono due percorsi di aggiornamento principali a seconda che sia stato abilitato il Snapshot Debugger usando un'estensione del sito o se è stato usato un SDK/NuGet aggiunto all'applicazione. Entrambi i percorsi di aggiornamento sono descritti di seguito. 

## <a name="upgrading-the-site-extension"></a>Aggiornamento dell'estensione del sito

> [!IMPORTANT]
> Le versioni precedenti di Application Insights usavano un'estensione del sito privata denominata _Application Insights Extension per app Azure Service_. L'esperienza di Application Insights corrente è abilitata impostando le impostazioni dell'app in modo da illuminare un'estensione del sito preinstallata.
> Per evitare conflitti, che potrebbero causare l'interruzione del funzionamento del sito, è importante eliminare prima l'estensione del sito privato. Vedere il passaggio 4 seguente.

Se è stato abilitato snapshot debugger usando l'estensione del sito, è possibile eseguire l'aggiornamento usando la procedura seguente:

1. Accedere al portale di Azure.
2. Passare alla risorsa in cui è abilitato Application Insights e snapshot debugger. Per un'app Web, ad esempio, passare alla risorsa del servizio app:

   ![Screenshot delle singole risorse del servizio App denominate DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Dopo aver eseguito l'esplorazione della risorsa, fare clic sul pannello estensioni e attendere il popolamento dell'elenco di estensioni:

   ![Screenshot delle estensioni del servizio app che visualizzano Application Insights estensione per app Azure servizio installato](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Se è installata una versione di _Application Insights estensione per il servizio app Azure_ , selezionarla e fare clic su Elimina. Confermare **Sì** per eliminare l'estensione e attendere il completamento dell'eliminazione prima di passare al passaggio successivo.

   ![Screenshot delle estensioni del servizio app che visualizzano Application Insights estensione per app Azure servizio con il pulsante Elimina evidenziato](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Passare al pannello panoramica della risorsa e fare clic su Application Insights:

   ![Screenshot di tre pulsanti. Il pulsante centrale con nome Application Insights è selezionato](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Se è la prima volta che si Visualizza il pannello Application Insights per questo servizio app, verrà richiesto di attivare Application Insights. Selezionare **attiva Application Insights**.
 
   ![Screenshot della prima esperienza per il pannello Application Insights con il pulsante attiva Application Insights evidenziato](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Verranno visualizzate le impostazioni Application Insights correnti. A meno che non si desideri modificare le impostazioni, è possibile lasciarle invariate. Il pulsante **applica** nella parte inferiore del pannello non è abilitato per impostazione predefinita ed è necessario attivare o disattivare una delle impostazioni per attivare il pulsante. Non è necessario modificare le impostazioni effettive, ma è possibile modificare l'impostazione e quindi modificarla immediatamente. Si consiglia di abilitare o disabilitare l'impostazione del profiler e quindi di selezionare **applica**.

   ![Screenshot della pagina di configurazione del servizio app Application Insights con il pulsante Applica evidenziato in rosso](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Quando si fa clic su **applica**, verrà chiesto di confermare le modifiche.

    > [!NOTE]
    > Il sito verrà riavviato come parte del processo di aggiornamento.

   ![Screenshot del prompt dei comandi applica monitoraggio del servizio app. Nella casella di testo viene visualizzato il messaggio: "verranno apportate modifiche alle impostazioni dell'app e verranno installati gli strumenti per collegare la risorsa Application Insights all'app Web. Il sito verrà riavviato. Continuare?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Fare clic su **Sì** per applicare le modifiche e attendere il completamento del processo.

Il sito è stato aggiornato ed è pronto per l'uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Aggiornamento Snapshot Debugger mediante SDK/NuGet

Se l'applicazione usa una versione di `Microsoft.ApplicationInsights.SnapshotCollector` sotto la versione 1.3.1, sarà necessario aggiornarla a una versione più [recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) per continuare a funzionare.
