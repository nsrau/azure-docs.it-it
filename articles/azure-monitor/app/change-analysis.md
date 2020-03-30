---
title: Usare l'analisi delle modifiche delle applicazioni in Monitoraggio di Azure per individuare i problemi relativi alle app Web. Documenti Microsoft
description: Usare l'analisi delle modifiche delle applicazioni in Monitoraggio di Azure per risolvere i problemi delle applicazioni nei siti attivi nel servizio app di Azure.Use Application Change Analysis in Azure Monitor to troubleshoot application issues on live sites on Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348740"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usare l'analisi delle modifiche dell'applicazione (anteprima)Use Application Change Analysis (preview) in Azure Monitor

Quando si verifica un problema o un'interruzione del sito attivo, determinare rapidamente la causa principale è fondamentale. Le soluzioni di monitoraggio standard potrebbero avvisare l'utente di un problema. Potrebbero anche indicare quale componente sta non riuscendo. Ma questo avviso non sempre spiega immediatamente la causa del fallimento. Sai che il tuo sito ha funzionato cinque minuti fa, e ora è rotto. Cosa è cambiato negli ultimi cinque minuti? Questa è la domanda a cui l'analisi delle modifiche dell'applicazione è progettata per rispondere in Monitoraggio di Azure.This is the question that Application Change Analysis is designed to answer in Azure Monitor.

Basandosi sulla potenza di [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), L'analisi delle modifiche fornisce informazioni dettagliate sulle modifiche apportate all'applicazione Azure per aumentare l'osservabilità e ridurre MTTR (tempo medio di riparazione).

> [!IMPORTANT]
> L'analisi delle modifiche è attualmente in anteprima. Questa versione di anteprima viene fornita senza un contratto di servizio. Questa versione non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o avere funzionalità vincolate. Per altre informazioni, vedere [Condizioni per l'uso supplementari per le anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)di Microsoft Azure.For more information, see Supplemental terms of use for Microsoft Azure previews .

## <a name="overview"></a>Panoramica

L'analisi delle modifiche rileva vari tipi di modifiche, dal livello dell'infrastruttura fino alla distribuzione dell'applicazione. Si tratta di un provider di risorse di Azure a livello di sottoscrizione che controlla le modifiche delle risorse nella sottoscrizione. L'analisi delle modifiche fornisce i dati per vari strumenti di diagnostica che consentono agli utenti di comprendere quali modifiche potrebbero aver causato problemi.

The following diagram illustrates the architecture of Change Analysis:

![Diagramma dell'architettura di come l'analisi delle modifiche ottiene i dati delle modifiche e li fornisce agli strumenti client](./media/change-analysis/overview.png)

Attualmente l'analisi delle modifiche è integrata nell'esperienza **Di diagnosi e risoluzione dei problemi** nell'app Web del servizio app, nonché disponibile come scheda autonoma nel portale di Azure.Currently Change Analysis is integrated into the Diagnose and solve problems experience in the App Service web app, as well as well as a standalone tab in Azure portal.
Vedere la sezione *Visualizzazione delle modifiche per tutte le risorse in Azure* per accedere al pannello Analisi delle modifiche e la sezione Analisi delle modifiche per la funzionalità Delle app *Web* per usarla nel portale dell'app Web più avanti in questo articolo.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Modifiche alle proprietà rilevate di Azure Resource ManagerAzure Resource Manager tracked properties changes

Uso di [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), l'analisi delle modifiche fornisce un record cronologico di come le risorse di Azure che ospitano l'applicazione sono cambiate nel tempo. È possibile rilevare le impostazioni rilevate, ad esempio le identità gestite, l'aggiornamento del sistema operativo della piattaforma e i nomi host.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Modifiche alle impostazioni proxy di Azure Resource ManagerAzure Resource Manager proxied setting changes
Impostazioni come la regola di configurazione IP, le impostazioni TLS e le versioni delle estensioni non sono ancora disponibili in ARG, pertanto Analisi modifiche esegue query e calcola tali modifiche in modo sicuro per fornire ulteriori dettagli sulle modifiche apportate nell'app. Queste informazioni non sono ancora disponibili in Azure Resource Graph, ma saranno presto disponibili.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Modifiche nella distribuzione e nella configurazione delle app Web (modifiche nell'ospite)

Change Analysis acquisisce lo stato di distribuzione e configurazione di un'applicazione ogni 4 ore. È in grado di rilevare, ad esempio, le modifiche apportate alle variabili di ambiente dell'applicazione. Lo strumento calcola le differenze e presenta ciò che è cambiato. A differenza delle modifiche di Resource Manager, le informazioni sulle modifiche alla distribuzione del codice potrebbero non essere disponibili immediatamente nello strumento. Per visualizzare le modifiche più recenti in Analisi modifiche, selezionare **Analizza modifiche ora**.

![Screenshot del pulsante "Scansione modifiche ora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Modifiche alle dipendenze

Le modifiche alle dipendenze delle risorse possono anche causare problemi in un'app Web.Changes to resource dependencies can also cause issues in a web app. Ad esempio, se un'app Web chiama una cache Redis, lo SKU della cache Redis potrebbe influire sulle prestazioni dell'app Web.For example, if a web app calls into a Redis cache, the Redis cache SKU could affect the web app performance. Per rilevare le modifiche nelle dipendenze, L'analisi delle modifiche controlla il record DNS dell'app Web. In questo modo, identifica le modifiche in tutti i componenti dell'app che potrebbero causare problemi.
Attualmente sono supportate le seguenti dipendenze:
- App Web
- Archiviazione di Azure
- SQL di Azure

### <a name="enablement"></a>Abilitazione
Il provider di risorse "Microsoft.ChangeAnalysis" deve essere registrato con una sottoscrizione per le proprietà di rilevamento di Azure Resource Manager e le impostazioni di proxy modificano i dati in modo che siano disponibili. Quando si immette lo strumento per diagnosticare e risolvere i problemi dell'app Web o visualizzare la scheda autonoma Analisi delle modifiche, questo provider di risorse viene registrato automaticamente. Non dispone di implementazioni di prestazioni e costi per la sottoscrizione. Quando si abilita l'analisi delle modifiche per le app Web (o si abilita lo strumento Diagnostica e risolvi problemi), l'utente avrà un impatto sulle prestazioni dell'app Web e nessun costo di fatturazione.
Per le modifiche nell'ospite dell'app Web, è necessaria un'abilitazione separata per l'analisi dei file di codice all'interno di un'app Web. Per altre informazioni, vedere [Abilitare l'analisi](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) delle modifiche nella sezione Diagnosticare e risolvere i problemi più avanti in questo articolo per ulteriori dettagli.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Visualizzazione delle modifiche per tutte le risorse in AzureViewing changes for all resources in Azure
In Monitoraggio di Azure è disponibile un pannello autonomo per l'analisi delle modifiche per visualizzare tutte le modifiche con informazioni dettagliate e risorse sulle dipendenze dell'applicazione.

Cercare l'analisi delle modifiche nella barra di ricerca nel portale di Azure per avviare il pannello.

![Screenshot della ricerca dell'analisi delle modifiche nel portale di Azure](./media/change-analysis/search-change-analysis.png)

Selezionare Gruppo di risorse e risorse per avviare la visualizzazione delle modifiche.

![Screenshot del pannello Modifica analisi nel portale di Azure](./media/change-analysis/change-analysis-standalone-blade.png)

È possibile visualizzare le risorse Insights e le dipendenze correlate che ospitano l'applicazione. Questa visualizzazione è progettata per essere incentrata sulle applicazioni per gli sviluppatori per la risoluzione dei problemi.

Le risorse attualmente supportate includono:Currently supported resources include:
- Macchine virtuali
- Set di scalabilità delle macchine virtualiVirtual Machine Scale Set
- Risorse di Rete di AzureAzure Networking resources
- Modifiche alle variabili di ambiente e di app Web con rilevamento file in-guest

Per commenti e suggerimenti, utilizzare il pulsante Invia feedback nel pannello o nell'e-mail. changeanalysisteam@microsoft.com

![Screenshot del pulsante di feedback nel pannello Analisi modifiche](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Modificare l'analisi per la funzionalità App Web

In Monitoraggio di Azure, l'analisi delle modifiche è anche integrata nell'esperienza di diagnosi self-service **e risoluzione dei problemi.** Accedere a questa esperienza dalla pagina **Panoramica** dell'applicazione del servizio app.

![Screenshot del pulsante "Panoramica" e del pulsante "Diagnostica e risolvi problemi"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Abilitare l'analisi delle modifiche nello strumento Diagnostica e risolvi problemi

1. Selezionare **Disponibilità e prestazioni**.

    ![Screenshot delle opzioni di risoluzione dei problemi "Disponibilità e prestazioni"](./media/change-analysis/availability-and-performance.png)

1. Selezionare **Modifiche applicazione**. Non che la funzionalità sia disponibile anche in **Arresti anomali delle**applicazioni .

   ![Screenshot del pulsante "Arresti anomali dell'applicazione"](./media/change-analysis/application-changes.png)

1. Per abilitare l'analisi delle modifiche, selezionare **Abilita ora**.

   ![Screenshot delle opzioni "Arresti anomali delle applicazioni"](./media/change-analysis/enable-changeanalysis.png)

1. Attivare **Modifica analisi** e selezionare **Salva**. Lo strumento visualizza tutte le app Web in un piano di servizio app. È possibile utilizzare l'opzione a livello di piano per attivare l'analisi delle modifiche per tutte le app Web in un piano.

    ![Screenshot dell'interfaccia utente "Abilita analisi modifiche"](./media/change-analysis/change-analysis-on.png)


1. Per accedere all'analisi delle modifiche, selezionare **Diagnostica e risolvere i problemi** > Di disponibilità e arresto**anomalo dell'applicazione****delle prestazioni** > . Verrà visualizzato un grafico che riepiloga il tipo di modifiche nel tempo insieme ai dettagli su tali modifiche. Per impostazione predefinita, vengono visualizzate modifiche nelle ultime 24 ore per facilitare i problemi immediati.

     ![Screenshot della visualizzazione diff di modifica](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Abilita analisi delle modifiche su larga scala

Se l'abbonamento include numerose app Web, l'abilitazione del servizio a livello dell'app Web sarebbe inefficiente. Eseguire lo script seguente per abilitare tutte le app Web nella sottoscrizione.

Prerequisiti:
* PowerShell Az Module. Seguire le istruzioni in [Installare il modulo di Azure PowerShellFollow](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) instructions at Install the Azure PowerShell module

Eseguire lo script riportato di seguito:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Passaggi successivi

- Abilitare le app di Application Insights per i [servizi app di Azure.](azure-web-apps.md)
- Abilitare Application Insights per la macchina virtuale di Azure e la scalabilità delle macchine virtuali di [Azure set di app ospitate da IIS](azure-vm-vmss-apps.md).
- Altre informazioni su [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), che consente di potenziare l'analisi delle modifiche.
