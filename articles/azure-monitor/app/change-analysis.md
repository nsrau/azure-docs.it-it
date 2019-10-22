---
title: Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per trovare i problemi delle app Web | Microsoft Docs
description: Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per risolvere i problemi dell'applicazione nei siti Live nel servizio app Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 3805d7b39c25bcb213a1d4f110161dcd00eb3630
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678244"
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

Usando il [grafo delle risorse di Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), l'analisi delle modifiche fornisce un record cronologico del modo in cui le risorse di Azure che ospitano l'applicazione sono state modificate nel tempo. L'analisi delle modifiche può rilevare, ad esempio, le modifiche alle regole di configurazione IP, alle identità gestite e alle impostazioni SSL. Quindi, se viene aggiunto un tag a un'app Web, l'analisi delle modifiche riflette la modifica. Queste informazioni sono disponibili purché il provider di risorse `Microsoft.ChangeAnalysis` sia abilitato nella sottoscrizione di Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Modifiche alla distribuzione e alla configurazione dell'app Web

L'analisi delle modifiche acquisisce lo stato di distribuzione e configurazione di un'applicazione ogni 4 ore. Può rilevare, ad esempio, le modifiche nelle variabili di ambiente dell'applicazione. Lo strumento calcola le differenze e presenta le modifiche apportate. Diversamente dalle modifiche apportate Gestione risorse, le informazioni sulle modifiche alla distribuzione del codice potrebbero non essere immediatamente disponibili nello strumento. Per visualizzare le ultime modifiche apportate all'analisi delle modifiche, selezionare **Scan changes Now**.

![Schermata del pulsante "analizza modifiche ora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Modifiche delle dipendenze

Le modifiche alle dipendenze delle risorse possono causare problemi anche in un'app Web. Se, ad esempio, un'app Web chiama in una cache Redis, lo SKU della cache Redis potrebbe influire sulle prestazioni dell'app Web. Per rilevare le modifiche nelle dipendenze, l'analisi delle modifiche controlla il record DNS dell'app Web. In questo modo, identifica le modifiche in tutti i componenti dell'app che possono causare problemi.
Attualmente sono supportate le dipendenze seguenti:
- App Web
- Archiviazione di Azure
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>Analisi delle modifiche per la funzionalità app Web

In monitoraggio di Azure, l'analisi delle modifiche è attualmente integrata nell'esperienza di **diagnostica e risoluzione dei problemi** in modalità self-service. Accedere a questa esperienza dalla pagina **Panoramica** dell'applicazione del servizio app.

![Screenshot del pulsante "panoramica" e del pulsante "diagnostica e risoluzione dei problemi"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Abilitare l'analisi delle modifiche nello strumento diagnostica e risoluzione dei problemi

1. Selezionare **disponibilità e prestazioni**.

    ![Screenshot delle opzioni di risoluzione dei problemi di disponibilità e prestazioni](./media/change-analysis/availability-and-performance.png)

1. Selezionare **le modifiche apportate all'applicazione**. Non è possibile che la funzionalità sia disponibile anche negli **arresti anomali dell'applicazione**.

   ![Screenshot del pulsante "arresti anomali dell'applicazione"](./media/change-analysis/application-changes.png)

1. Per abilitare l'analisi delle modifiche, selezionare **Abilita ora**.

   ![Screenshot delle opzioni "arresti anomali dell'applicazione"](./media/change-analysis/enable-changeanalysis.png)

1. Attivare l' **analisi delle modifiche** e selezionare **Salva**.

    ![Screenshot dell'interfaccia utente "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)


1. Per accedere all'analisi delle modifiche, selezionare **diagnostica e Risolvi i problemi**  > **disponibilità e prestazioni**  > **arresti anomali dell'applicazione**. Verrà visualizzato un grafico che riepiloga il tipo di modifiche nel tempo insieme ai dettagli relativi a tali modifiche:

     ![Screenshot della visualizzazione delle differenze delle modifiche](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Abilitare l'analisi delle modifiche su larga scala

Se la sottoscrizione include numerose app Web, l'abilitazione del servizio al livello dell'app Web risulterebbe inefficiente. Eseguire lo script seguente per abilitare tutte le app Web nella sottoscrizione.

Prerequisiti:
* PowerShell AZ Module. Seguire le istruzioni in [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Eseguire lo script seguente:

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

- Abilitare Application Insights per le [app di app Azure Services](azure-web-apps.md).
- Abilitare Application Insights per [le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure con IIS](azure-vm-vmss-apps.md).
- Scopri di più su [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), che consente di migliorare l'analisi del risparmio energia.
