---
title: Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per trovare i problemi delle app Web | Microsoft Docs
description: Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per risolvere i problemi dell'applicazione nei siti Live nel servizio app Azure.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 3efa26a1eaea8f522d9717efb0de0ec8e1682e0e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875124"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usare l'analisi delle modifiche dell'applicazione (anteprima) in monitoraggio di Azure

Quando si verifica un problema o un'interruzione di un sito Live, determinare rapidamente la causa principale è fondamentale. Le soluzioni di monitoraggio standard potrebbero segnalare un problema. Potrebbero anche indicare quale componente ha avuto esito negativo. Tuttavia, questo avviso non descriverà mai immediatamente la cause dell'errore. Si sa che il sito ha funzionato cinque minuti fa e ora è danneggiato. Cosa è cambiato negli ultimi cinque minuti? Questa è la domanda che l'analisi delle modifiche dell'applicazione è progettata per rispondere in monitoraggio di Azure.

Basandosi sulle potenzialità di [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), l'analisi delle modifiche fornisce informazioni approfondite sulle modifiche apportate alle applicazioni Azure per aumentare l'osservabilità e ridurre MTTR (tempo medio per il ripristino).

> [!IMPORTANT]
> L'analisi delle modifiche è attualmente in anteprima. Questa versione di anteprima viene fornita senza un contratto di servizio. Questa versione non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero avere funzionalità vincolate. Per ulteriori informazioni, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Panoramica

L'analisi delle modifiche rileva vari tipi di modifiche, dal livello dell'infrastruttura fino alla distribuzione delle applicazioni. Si tratta di un provider di risorse di Azure a livello di sottoscrizione che controlla le modifiche alle risorse nella sottoscrizione. L'analisi delle modifiche fornisce dati per diversi strumenti di diagnostica che consentono agli utenti di comprendere quali modifiche potrebbero avere causato problemi.

Il diagramma seguente illustra l'architettura dell'analisi delle modifiche:

![Diagramma dell'architettura del modo in cui l'analisi delle modifiche ottiene i dati delle modifiche e li fornisce agli strumenti client](./media/change-analysis/overview.png)

L'analisi delle modifiche è attualmente integrata nell'esperienza di **diagnostica e risoluzione dei problemi** nell'app Web del servizio app. Per abilitare il rilevamento delle modifiche e visualizzare le modifiche nell'app Web, vedere la sezione analisi delle modifiche *per la funzionalità app Web* più avanti in questo articolo.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager modifiche della distribuzione

Usando il [grafo delle risorse di Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), l'analisi delle modifiche fornisce un record cronologico del modo in cui le risorse di Azure che ospitano l'applicazione sono state modificate nel tempo. L'analisi delle modifiche può rilevare, ad esempio, le modifiche alle regole di configurazione IP, alle identità gestite e alle impostazioni SSL. Quindi, se viene aggiunto un tag a un'app Web, l'analisi delle modifiche riflette la modifica. Queste informazioni sono disponibili finché il `Microsoft.ChangeAnalysis` provider di risorse è abilitato nella sottoscrizione di Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Modifiche alla distribuzione e alla configurazione dell'app Web

L'analisi delle modifiche acquisisce lo stato di distribuzione e configurazione di un'applicazione ogni 4 ore. Può rilevare, ad esempio, le modifiche nelle variabili di ambiente dell'applicazione. Lo strumento calcola le differenze e presenta le modifiche apportate. Diversamente dalle modifiche apportate Gestione risorse, le informazioni sulle modifiche alla distribuzione del codice potrebbero non essere immediatamente disponibili nello strumento. Per visualizzare le ultime modifiche apportate all'analisi delle modifiche, selezionare **Scan changes Now**.

![Schermata del pulsante "analizza modifiche ora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Modifiche delle dipendenze

Le modifiche alle dipendenze delle risorse possono causare problemi anche in un'app Web. Se, ad esempio, un'app Web chiama in una cache Redis, lo SKU della cache Redis potrebbe influire sulle prestazioni dell'app Web. Per rilevare le modifiche nelle dipendenze, l'analisi delle modifiche controlla il record DNS dell'app Web. In questo modo, identifica le modifiche in tutti i componenti dell'app che possono causare problemi.

## <a name="change-analysis-for-the-web-apps-feature"></a>Analisi delle modifiche per la funzionalità app Web

In monitoraggio di Azure, l'analisi delle modifiche è attualmente integrata nell'esperienza di **diagnostica e risoluzione dei problemi** in modalità self-service. Accedere a questa esperienza dalla pagina **Panoramica** dell'applicazione del servizio app.

![Screenshot del pulsante "panoramica" e del pulsante "diagnostica e risoluzione dei problemi"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Abilitare l'analisi delle modifiche nello strumento diagnostica e risoluzione dei problemi

1. Selezionare **disponibilità e prestazioni**.

    ![Screenshot delle opzioni di risoluzione dei problemi di disponibilità e prestazioni](./media/change-analysis/availability-and-performance.png)

1. Selezionare **le modifiche**apportate all'applicazione. Non è possibile che la funzionalità sia disponibile anche negli **arresti anomali dell'applicazione**.

   ![Screenshot del pulsante "arresti anomali dell'applicazione"](./media/change-analysis/application-changes.png)

1. Per abilitare l'analisi delle modifiche, selezionare **Abilita ora**.

   ![Screenshot delle opzioni "arresti anomali dell'applicazione"](./media/change-analysis/enable-changeanalysis.png)

1. Attivare l' **analisi delle modifiche** e selezionare **Salva**.

    ![Screenshot dell'interfaccia utente "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)


1. Per accedere all'analisi delle modifiche, selezionare **diagnostica e Risolvi i problemi relativi** > a**disponibilità e prestazioni** > **anomali dell'applicazione**. Verrà visualizzato un grafico che riepiloga il tipo di modifiche nel tempo insieme ai dettagli relativi a tali modifiche:

     ![Screenshot della visualizzazione delle differenze delle modifiche](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Abilitare l'analisi delle modifiche su larga scala

Se la sottoscrizione include numerose app Web, l'abilitazione del servizio al livello dell'app Web risulterebbe inefficiente. In questo caso, seguire queste istruzioni alternative.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registrare il provider di risorse di analisi delle modifiche per la sottoscrizione

1. Registrare il flag della funzionalità di analisi delle modifiche (anteprima). Poiché il flag funzionalità è in anteprima, è necessario registrarlo per renderlo visibile alla sottoscrizione:

   1. Aprire [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Screenshot delle modifiche Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Modificare il tipo di shell in **PowerShell**.

      ![Screenshot delle modifiche Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Eseguire il seguente comando PowerShell:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Registrare il provider di risorse di analisi delle modifiche per la sottoscrizione.

   - Passare a **sottoscrizioni**e selezionare la sottoscrizione che si vuole abilitare nel servizio di modifica. Quindi selezionare provider di risorse:

        ![Screenshot che illustra come registrare il provider di risorse di analisi delle modifiche](./media/change-analysis/register-rp.png)

       - Selezionare **Microsoft. ChangeAnalysis**. Nella parte superiore della pagina selezionare **Register (registra**).

       - Dopo l'abilitazione del provider di risorse, è possibile impostare un tag nascosto nell'app Web per rilevare le modifiche a livello di distribuzione. Per impostare un tag nascosto, seguire le istruzioni riportate in **non è possibile recuperare le informazioni sull'analisi delle modifiche**.

   - In alternativa, è possibile usare uno script di PowerShell per registrare il provider di risorse:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Per usare PowerShell per impostare un tag nascosto in un'app Web, eseguire il comando seguente:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Dopo aver aggiunto il tag nascosto, potrebbe essere comunque necessario attendere fino a 4 ore prima di iniziare a visualizzare le modifiche. I risultati vengono posticipati perché l'analisi delle modifiche analizza l'app Web solo ogni 4 ore. La pianificazione di 4 ore limita l'effetto sulle prestazioni dell'analisi.

## <a name="next-steps"></a>Passaggi successivi

- Abilitare Application Insights per le [app di app Azure Services](azure-web-apps.md).
- Abilitare Application Insights per [le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure con IIS](azure-vm-vmss-apps.md).
- Scopri di più su [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), che consente di migliorare l'analisi del risparmio energia.
