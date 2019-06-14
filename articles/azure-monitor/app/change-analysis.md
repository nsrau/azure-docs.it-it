---
title: Usare analisi applicazione di modifiche in Monitoraggio di Azure per individuare i problemi di app web | Microsoft Docs
description: Usare analisi applicazione di modifiche in Monitoraggio di Azure per risolvere i problemi dell'applicazione in siti live nel servizio App di Azure.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 2a31131b662d01f9841a3f1c5b0a6c459a117e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075376"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usare analisi applicazione di modifiche (anteprima) in Monitoraggio di Azure

Quando si verifica un'interruzione o un problema del sito live, è fondamentale stabilire velocemente la causa radice. Soluzioni di monitoraggio standard potrebbero segnala un problema. Potrebbero anche indicare quale componente ha esito negativo. Ma questo avviso non verrà spiegato sempre immediatamente la causa dell'errore. Si conosce il sito ha lavorato cinque minuti fa, e a questo punto è rotto. Le modifiche apportate negli ultimi cinque minuti? Questa è la domanda che analisi di modifica dell'applicazione è progettata per rispondere in Monitoraggio di Azure. 

Compilazione la potenza delle [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview), analisi di modifica fornisce informazioni approfondite le modifiche dell'applicazione di Azure per aumentare l'osservabilità e ridurre il MTTR (tempo medio di ripristino).

> [!IMPORTANT]
> Analisi della modifica è attualmente in anteprima. Questa versione di anteprima viene fornita senza un contratto di servizio. Questa versione non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [condizioni d'uso aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Panoramica

Analisi della modifica rileva diversi tipi di modifiche, dal livello dell'infrastruttura fino alla distribuzione dell'applicazione. È un provider di risorse di Azure a livello di sottoscrizione che controlla le modifiche alle risorse nella sottoscrizione. Analisi della modifica fornisce dati per diversi strumenti di diagnostica per consentire agli utenti comprendere quali modifiche potrebbe essere stato causato problemi.

Il diagramma seguente illustra l'architettura di analisi di modifica:

![Diagramma dell'architettura del modo in cui modificare Analysis Ottiene i dati delle modifiche e la fornisce a strumenti client](./media/change-analysis/overview.png)

Attualmente Analysis modifica è integrato nel **diagnosticare e risolvere i problemi** esperienza nell'app web di servizio App. Per abilitare il rilevamento delle modifiche e visualizzare le modifiche nell'app web, vedere la *analisi modificare per la funzionalità App Web* sezione più avanti in questo articolo.

### <a name="azure-resource-manager-deployment-changes"></a>Modifiche di distribuzione Azure Resource Manager

Usando [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview), analisi della modifica fornisce una registrazione cronologica del modo in cui le risorse di Azure che ospitano l'applicazione sono stati modificati nel corso del tempo. Ad esempio, può rilevare l'analisi della modifica, modifica le regole di configurazione IP, identità gestite e impostazioni SSL. Pertanto, se un tag viene aggiunto a un'app web, modificare Analysis riflette la modifica. Queste informazioni sono disponibili, purché il `Microsoft.ChangeAnalysis` provider di risorse è abilitato nella sottoscrizione di Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Modifiche di configurazione e distribuzione dell'app web

Analisi della modifica acquisisce lo stato di distribuzione e la configurazione di un'applicazione ogni 4 ore. È possibile rilevare, ad esempio, le modifiche nelle variabili di ambiente dell'applicazione. Lo strumento calcola le differenze e presenta le modifiche apportate. A differenza delle modifiche di Resource Manager, informazioni sulle modifiche di codice la distribuzione potrebbe non essere disponibili immediatamente nello strumento. Per visualizzare le modifiche più recenti nell'analisi di modifica, selezionare **analisi Cambia ora**.

![Screenshot del pulsante "Analisi modifiche ora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Modifiche apportate alla dipendenza

Modifiche alle dipendenze delle risorse possono anche causare problemi in un'app web. Ad esempio, se un'app web chiama in una cache Redis, cache Redis SKU compromettere le prestazioni dell'app web. Per rilevare le modifiche nelle dipendenze, l'analisi modifiche controlla i record DNS dell'app web. In questo modo, identifica le modifiche in tutti i componenti dell'app che potrebbe causare problemi.

## <a name="change-analysis-for-the-web-apps-feature"></a>Analisi di modifica per la funzionalità App Web

In Monitoraggio di Azure, analisi di modifica attualmente è incorporata nel Self-Service **diagnosticare e risolvere i problemi** esperienza. Questa esperienza di accesso di **Panoramica** pagina dell'applicazione del servizio App.

![Screenshot del pulsante "Panoramica" e "diagnosi e risoluzione dei problemi" pulsante](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Abilitare l'analisi di modifica nella diagnostica e Risolvi strumento problemi

1. Selezionare **disponibilità e prestazioni**.

    ![Screenshot di "Disponibilità e prestazioni" Opzioni di risoluzione dei problemi](./media/change-analysis/availability-and-performance.png)

1. Selezionare **arresti anomali delle applicazioni**.

   ![Screenshot del pulsante "Blocchi applicazione"](./media/change-analysis/application-crashes-tile.png)

1. Per abilitare l'analisi della modifica, selezionare **Abilita adesso**.

   ![Screenshot delle opzioni "Blocchi applicazione"](./media/change-analysis/application-crashes.png)

1. Per poter usufruire di tutte le funzionalità di analisi della modifica, attivare **modifica Analysis**, **rileva modifiche di codice**, e **Always on**. Selezionare quindi **Salva**.

    ![Screenshot dell'interfaccia utente "Attiva analisi modifica"](./media/change-analysis/change-analysis-on.png)

    - Abilitare **analisi modifica** per rilevare le modifiche a livello di risorsa. 
    - Abilitare **rileva modifiche di codice** per vedere i file di distribuzione e le modifiche alla configurazione del sito. 
    - Abilitare **Always on** per ottimizzare le prestazioni di analisi di modifica. Tuttavia, tenere presente che questa impostazione potrebbe comportare ulteriori addebiti nella fatturazione.

1. Per accedere alle analisi della modifica, selezionare **diagnosi e risoluzione dei problemi** > **disponibilità e prestazioni** > **blocchi applicazione**. Verrà visualizzato un grafico che viene riepilogato il tipo di modifiche nel corso del tempo con i dettagli su queste modifiche:

     ![Screenshot della visualizzazione diff modifica](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Abilitare l'analisi di modifiche su larga scala

Se la sottoscrizione include numerose App web, l'abilitazione del servizio a livello di app web sarebbe inefficiente. In questo caso, seguire queste istruzioni alternative.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registrare il provider di risorse di analisi di modifica per la sottoscrizione

1. Registrare il flag di funzionalità di analisi della modifica (anteprima). Poiché il flag funzionalità è disponibile in anteprima, è necessario registrare in modo che sia visibile nella sottoscrizione:

   1. Aprire [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Screenshot della modifica Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Modificare il tipo di shell **PowerShell**.

      ![Screenshot della modifica Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Eseguire il seguente comando PowerShell:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```
    
1. Registrare il provider di risorse di analisi di modifica per la sottoscrizione.

   - Passare a **sottoscrizioni**, quindi selezionare la sottoscrizione che si desidera abilitare il servizio di modifica. Quindi selezionare i provider di risorse:

        ![Screenshot che illustra come registrare il provider di risorse di analisi della modifica](./media/change-analysis/register-rp.png)

       - Selezionare **Microsoft.ChangeAnalysis**. Quindi nella parte superiore della pagina, selezionare **registrare**.

       - Dopo aver abilitato il provider di risorse, è possibile impostare un tag nascosto nell'app web per rilevare le modifiche a livello di distribuzione. Per impostare un tag nascosto, seguire le istruzioni sotto **non è possibile recuperare le informazioni di analisi della modifica**.

   - In alternativa, è possibile usare uno script di PowerShell per registrare il provider di risorse:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration
    
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Per usare PowerShell per impostare un tag nascosto in un'app web, eseguire il comando seguente:
    
        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Dopo aver aggiunto i tag nascosti, potrebbe essere necessario attendere fino a 4 ore prima di iniziare a visualizzare le modifiche. I risultati sono ritardati perché modifica analisi analizza l'app web solo ogni 4 ore. La pianificazione di 4 ore limita l'impatto sulle prestazioni dell'analisi.

## <a name="next-steps"></a>Passaggi successivi

- Monitorare il servizio App in modo più efficace dal [abilitazione delle funzionalità di Application Insights](azure-web-apps.md) in Monitoraggio di Azure.
- Altre informazioni sulle [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview), che aiuta power analisi della modifica.
