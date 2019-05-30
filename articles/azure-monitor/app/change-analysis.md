---
title: Applicazione di monitoraggio di Azure modificare analysis - individuare le modifiche che potrebbero influire sulla problemi/interruzioni di siti live con applicazione di monitoraggio di Azure modificare analysis | Microsoft Docs
description: Risolvere i problemi di siti attivi dell'applicazione in servizi App di Azure con analisi di modifica dell'applicazione di monitoraggio di Azure
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226291"
---
# <a name="application-change-analysis-public-preview"></a>Analisi delle modifiche alle applicazioni (anteprima pubblica)

Quando si verifica una problema o interruzione del sito live, è essenziale determinare rapidamente la causa radice. Soluzioni di monitoraggio standard può consentire di identificare rapidamente che si è verificato un problema e spesso anche quale componente ha esito negativo. Ma ciò non sempre causare una spiegazione immediata per il motivo per cui si sta verificando l'errore. Il sito ha lavorato cinque minuti fa, a questo punto è rotto. Le modifiche apportate negli ultimi cinque minuti? Questa è la domanda che la nuova funzionalità di analisi applicazione di modifiche sono progettata per rispondere a monitoraggio di Azure. Creando la potenza delle [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview) analisi applicazione di modifiche offre informazioni approfondite le modifiche dell'applicazione di Azure per aumentare l'osservabilità e ridurre il MTTR (tempo medio di ripristino).

> [!IMPORTANT]
> Modifica analisi applicazione di Azure Monitor sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Panoramica del servizio di analisi della modifica
Il servizio di analisi di modifica rileva diversi tipi di modifiche del livello infrastruttura fino alla distribuzione dell'applicazione. È un provider di risorse di Azure a livello di sottoscrizione che esamina le modifiche alle risorse nella sottoscrizione e fornisce i dati per diversi strumenti di diagnostica per consentire agli utenti comprendere quali modifiche potrebbe essere stato causato problemi.

Il diagramma seguente illustra l'architettura del servizio di analisi di modifica: ![Diagramma dell'architettura per la modalità modifica analysis service Ottiene modificare i dati e fornire i dati per gli strumenti client](./media/change-analysis/overview.png)

Attualmente lo strumento è integrato in servizi App di app web di diagnosticare e risolvere i problemi esperienza. Visualizzare *servizio di analisi di modifica per l'App Web di servizi App* sezione su come abilitare e visualizzare le modifiche apportate a un'app web.

### <a name="azure-resource-manager-deployment-changes"></a>Modifiche di distribuzione Azure Resource Manager
Sfruttando [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview) lo strumento di analisi delle modifiche fornisce un record cronologico del modo in cui le risorse di Azure che ospitano l'applicazione sono stati modificati nel corso del tempo. Ad esempio, se un'app web ha un tag aggiunto ad esso, le modifiche si rifletteranno nello strumento di analisi di modifica.
Queste informazioni sono sempre disponibili, purché il `Microsoft.ChangeAnalysis` provider di risorse è caricato nella sottoscrizione di Azure.

### <a name="web-application-deployment-and-configuration-changes"></a>Modifiche di configurazione e distribuzione dell'applicazione Web
Lo strumento di analisi di modifica acquisisce lo stato di distribuzione e la configurazione di un'applicazione ogni 4 ore per calcolare le differenze e presentare le modifiche apportate. Le modifiche alle variabili di ambiente dell'applicazione, modifiche alle regole di configurazione IP, le modifiche di identità del servizio gestito, le modifiche alle impostazioni SSL e così via sono esempi di tali modifiche.
A differenza delle modifiche di Resource Manager, questo tipo di informazioni sulle modifiche potrà non essere disponibile immediatamente nello strumento. Per visualizzare le modifiche più recenti, usare il pulsante 'Analisi modifiche ora' nello strumento.

![Screenshot di analisi per le modifiche a questo punto sul pulsante Esegui diagnosi e risoluzione dello strumento di problemi con l'integrazione di analisi di modifica per l'app web di servizio app](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Modifiche apportate alla dipendenza
Risorse di dipendenze potrebbero anche essere la causa dei problemi. Ad esempio, se un'app web chiama in una cache Redis, le prestazioni dell'app web possono essere influenzata dalla cache Redis di SKU. Modificare analysis service presentare anche le dipendenze informazioni sulle modifiche per identificare le modifiche in tutti i componenti di un'app che hanno causato problemi da prendere in considerazione il record DNS di app web.


## <a name="change-analysis-service-for-app-services-web-app"></a>Modificare il servizio di analisi per App Web di servizi App

Modifica analisi applicazione di Azure Monitor sono attualmente incorporato in Self-Service **diagnosi e risoluzione dei problemi** esperienza, che è possibile accedere dalle **Panoramica** sezione del servizio App di Azure applicazione:

![Cenni preliminari sulla schermata di Azure App Service con caselle rosse intorno pulsante Cenni preliminari sulla pagina e diagnosticare e risolvere i pulsante problemi](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Abilitare l'analisi di modifica nella diagnosi e risoluzione dello strumento di problemi

1. Selezionare **disponibilità e prestazioni**

    ![schermata di disponibilità e le opzioni di risoluzione dei problemi di prestazioni](./media/change-analysis/availability-and-performance.png)

2. Scegliere il **blocchi applicazione** riquadro.

   ![Screenshot con riquadro di arresti anomali dell'applicazione](./media/change-analysis/application-crashes-tile.png)

3. Per abilitare **modifica Analysis** selezionate **abilitare ora**.

   ![schermata di disponibilità e le opzioni di risoluzione dei problemi di prestazioni](./media/change-analysis/application-crashes.png)

4. Per sfruttare i vantaggi della versione completa modificare set di funzionalità di analisi **modificare Analysis**, **rileva modifiche di codice**, e **Always on** al **su** e selezionare **salvare**.

    ![Screenshot dell'interfaccia utente del servizio App di Azure Abilita modifica analysis](./media/change-analysis/change-analysis-on.png)

    Se **analisi modifica** è abilitato, sarà in grado di rilevare le modifiche a livello di risorsa. Se **rileva modifiche di codice** è abilitata, verrà anche vedere i file di distribuzione e le modifiche di configurazione del sito. Abilitazione **Always on** ottimizzerà la modifica l'analisi delle prestazioni, ma può comportare costi aggiuntivi da una prospettiva di fatturazione.

5.  Dopo che tutto ciò che è abilitato, selezionando **diagnosi e risoluzione dei problemi** > **disponibilità e prestazioni** > **blocchi applicazione** sarà possibile accedere l'esperienza di analisi di modifica. Il grafico riepiloga i il tipo di modifiche che si sono verificati nel corso del tempo con i dettagli su queste modifiche:

     ![Screenshot della visualizzazione di modifica delle differenze](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Abilitare il servizio di analisi di modifiche su larga scala
Se si dispone di molte delle App web nella sottoscrizione, abilitare il servizio in base al livello di app web sarà inefficiente. Ecco alcune istruzioni per l'onboarding alternativo.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Registra provider di risorse di analisi di modifica per la sottoscrizione

1. Registrare i flag funzionalità Anteprima di analisi della modifica

    Poiché questa funzionalità è disponibile in anteprima, è necessario per prima cosa registrare il flag funzionalità per renderlo visibile per la sottoscrizione.
    - Aprire [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Screenshot della modifica Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

    - Modificare il tipo di shell per PowerShell:

    ![Screenshot della modifica Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

    - Eseguire il seguente comando PowerShell:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Registrare i Provider di risorse di analisi di modifica per la sottoscrizione

    - Passare alle sottoscrizioni, selezionare la sottoscrizione desiderata per l'onboarding del servizio di modifica, quindi fare clic su provider di risorse:

        ![Schermata per la registrazione della relying Party di modifica analisi dal pannello delle sottoscrizioni](./media/change-analysis/register-rp.png)

    - Selezionare *Microsoft.ChangeAnalysis* e fare clic su *registrare* nella parte superiore della pagina.

    - Una volta eseguito l'onboarding del Provider di risorse, seguire le istruzioni disponibili nel *non è possibile recuperare le informazioni di analisi modificare* seguito per impostare i tag nascosti nell'app web per abilitare la distribuzione a livello di rilevamento modifiche nell'app web.

3. In alternativa al passaggio 2 precedente, è possibile registrarsi per il Provider di risorse tramite script di PowerShell:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Per impostare un tag nascosto in un'app web con PowerShell, eseguire il comando seguente:

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> Dopo aver aggiunto il tag nascosto, è comunque necessario inizialmente attendere fino a 4 ore per essere in grado di visualizzare prima di tutto le modifiche. Ciò è dovuto il freqeuncy entro 4 ore che usa il servizio di analisi di modifica per analizzare l'app web pur limitando l'impatto sulle prestazioni dell'analisi.

## <a name="next-steps"></a>Passaggi successivi

- Migliorare il monitoraggio dei servizi App di Azure [abilitando le funzionalità di Application Insights](azure-web-apps.md) di monitoraggio di Azure.
- Migliorare la comprensione del [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview) in modo da modificare analisi applicazione di monitoraggio di Azure power.
