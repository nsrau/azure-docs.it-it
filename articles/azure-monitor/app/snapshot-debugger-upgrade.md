---
title: Aggiornamento del debugger snapshot di Azure Application InsightsUpgrading Azure Application Insights Snapshot Debugger
description: Come aggiornare il debugger snapshot per le app .NET alla versione più recente nei servizi app di Azure o tramite i pacchetti NugetHow to upgrade Snapshot Debugger for .NET apps to the latest version on Azure App Services, or via Nuget packages
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671393"
---
# <a name="upgrading-the-snapshot-debugger"></a>Aggiornamento del debugger snapshotUpgrading the Snapshot Debugger

Per garantire la migliore sicurezza possibile per i dati, Microsoft si sta allontanando da TLS 1.0 e TLS 1.1, che hanno dimostrato di essere vulnerabili a determinati aggressori. Se si utilizza una versione precedente dell'estensione del sito, sarà necessario un aggiornamento per continuare a funzionare. In questo documento vengono descritti i passaggi necessari per aggiornare il debugger snapshot alla versione più recente. Esistono due percorsi di aggiornamento principali a seconda che il debugger snapshot sia stato abilitato utilizzando un'estensione del sito o se è stato usato un SDK/Nuget aggiunto all'applicazione. Entrambi i percorsi di aggiornamento sono descritti di seguito. 

## <a name="upgrading-the-site-extension"></a>Aggiornamento dell'estensione del sito

> [!IMPORTANT]
> Le versioni precedenti di Application Insights usavano un'estensione del sito privato _denominata Estensione di Application Insights per_il servizio app di Azure. L'esperienza di Application Insights corrente viene abilitata impostando Impostazioni app per illuminare un'estensione del sito preinstallata.
> Per evitare conflitti che possono causare l'interruzione del funzionamento del sito, è importante eliminare prima l'estensione del sito privato. Vedere il passaggio 4 riportato di seguito.

Se il debugger snapshot è stato abilitato utilizzando l'estensione del sito, è possibile eseguire l'aggiornamento utilizzando la procedura seguente:

1. Accedere al portale di Azure.
2. Passare alla risorsa in cui è abilitato Application Insights e il debugger snapshot. Ad esempio, per un'app Web, passare alla risorsa Servizio app:

   ![Screenshot della singola risorsa del servizio app denominata DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Dopo aver eseguito il mouse sulla risorsa, fare clic sul pannello Estensioni e attendere che l'elenco delle estensioni venga popolato:Once you've navigated to your resource, click on the Extensions blade and wait for the list of extensions to populate:

   ![Screenshot delle estensioni del servizio app che mostra l'estensione di Application Insights per il servizio app di Azure installata](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Se è installata una versione _dell'estensione Application Insights per_ il servizio app di Azure, selezionarla e fare clic su Elimina.If any version of Application Insights extension for Azure App Service is installed, then select it and click Delete. Confermare **Sì** per eliminare l'estensione e attendere il completamento dell'eliminazione prima di passare al passaggio successivo.

   ![Screenshot delle estensioni del servizio app che mostra l'estensione di Application Insights per il servizio app di Azure con il pulsante Elimina evidenziato](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Passare al pannello Panoramica della risorsa e fare clic su Application Insights:

   ![Screenshot di tre pulsanti. Pulsante centrale con nome Application Insights selezionato](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Se è la prima volta che viene visualizzato il pannello Application Insights per questo servizio app, verrà richiesto di attivare Application Insights.If this is the first time you've viewed the Application Insights blade for this App Service, you'll be prompted to turn on Application Insights. Selezionare **Attiva Application Insights**.
 
   ![Screenshot dell'esperienza per la prima volta per il pannello Application Insights con il pulsante Attiva Application Insights evidenziato](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Vengono visualizzate le impostazioni correnti di Application Insights.The current Application Insights settings are displayed. A meno che tu non voglia cogliere l'occasione per modificare le impostazioni, è possibile lasciarle così com'è. Il pulsante **Applica** nella parte inferiore del pannello non è abilitato per impostazione predefinita e sarà necessario attivare una delle impostazioni per attivare il pulsante. Non è necessario modificare le impostazioni effettive, ma è possibile modificare l'impostazione e quindi modificarla immediatamente. Si consiglia di alternare l'impostazione Profiler e quindi selezionare **Applica**.

   ![Screenshot della pagina Configurazione del servizio app di Application Insights con il pulsante Applica evidenziato in rosso](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Dopo aver fatto clic su **Applica**, ti verrà chiesto di confermare le modifiche.

    > [!NOTE]
    > Il sito verrà riavviato come parte del processo di aggiornamento.

   ![Screenshot della richiesta di monitoraggio dell'applicazione del servizio app. Casella di testo visualizza il messaggio: "Ora applicheremo le modifiche alle impostazioni dell'app e installeremo i nostri strumenti per collegare la risorsa di Application Insights all'app Web. Il sito verrà riavviato. Continuare?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Fare clic su **Sì** per applicare le modifiche e attendere il completamento del processo.

Il sito è stato aggiornato ed è pronto per l'uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Aggiornamento del debugger snapshot tramite SDK/NugetUpgrading Snapshot Debugger using SDK/Nuget

Se l'applicazione utilizza `Microsoft.ApplicationInsights.SnapshotCollector` una versione di versione 1.3.1 successiva, dovrà essere aggiornata a una [versione più recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) per continuare a funzionare.
