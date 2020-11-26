---
title: Soluzioni di monitoraggio in Monitoraggio di Azure | Microsoft Docs
description: Le soluzioni di monitoraggio in Monitoraggio di Azure sono una raccolta di regole logiche, di visualizzazione e di acquisizione dei dati che forniscono metriche relative a un'area problematica specifica.  Questo articolo offre informazioni sull'installazione e l'uso delle soluzioni di monitoraggio.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9ced3dfeccdbac5f0eb220cf0e104679f263aac
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186865"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluzioni di monitoraggio in Monitoraggio di Azure

Le soluzioni di monitoraggio in monitoraggio di Azure consentono di analizzare il funzionamento di un'applicazione o un servizio di Azure specifico. Questo articolo offre una breve panoramica delle soluzioni di monitoraggio in Azure e informazioni dettagliate sull'uso e l'installazione di tali soluzioni. È possibile aggiungere soluzioni di monitoraggio a Monitoraggio di Azure per qualsiasi applicazione e servizio usati. Mentre le soluzioni sono in genere disponibili gratuitamente, l'uso della raccolta dei dati potrebbe determinare un addebito.

## <a name="use-monitoring-solutions"></a>Usare le soluzioni di monitoraggio

Nella pagina **Panoramica** soluzioni di monitoraggio di Azure viene visualizzato un riquadro per ogni soluzione installata in un'area di lavoro log Analytics. Per aprire questa pagina, passare a **monitoraggio di Azure** nella [portale di Azure](https://ms.portal.azure.com). Nel menu **Insights** selezionare **altro** per aprire l' **Hub Insights** e quindi fare clic su **log Analytics aree di lavoro**.

[![Hub Insights](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Usare le caselle a discesa nella parte superiore della schermata per modificare l'area di lavoro o l'intervallo di tempo usato per i riquadri. Fare clic sul riquadro di una soluzione per aprire la visualizzazione corrispondente, in cui sarà possibile esaminare analisi più dettagliate sui dati raccolti.

[![Screenshot mostra il menu portale di Azure con le soluzioni selezionate e le soluzioni visualizzate nel riquadro soluzioni.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Le soluzioni di monitoraggio possono contenere più tipi di risorse di Azure ed è possibile visualizzare tutte le risorse incluse in una soluzione proprio come qualsiasi altra risorsa. Ad esempio, tutte le query di log incluse nella soluzione sono elencate in query di **soluzione** in [Esplora query](../log-query/log-analytics-tutorial.md). È possibile usare queste query durante l'esecuzione di analisi ad hoc con [query di log](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Elencare le soluzioni di monitoraggio installate

### <a name="portal"></a>[Portale](#tab/portal)

Usare la procedura seguente per elencare le soluzioni di monitoraggio installate nella sottoscrizione.

1. Accedere al [portale di Azure](https://ms.portal.azure.com). Cercare e selezionare **Soluzioni**.
1. Vengono elencate le soluzioni installate in tutte le aree di lavoro. Il nome della soluzione è seguito dal nome dell'area di lavoro in cui è installata.
1. Usare le caselle a discesa nella parte superiore della schermata per filtrare in base alla sottoscrizione o al gruppo di risorse.

![Elencare tutte le soluzioni](media/solutions/list-solutions-all.png)

Fare clic sul nome di una soluzione per aprire la relativa pagina di riepilogo. Questa pagina mostra le visualizzazioni incluse nella soluzione e fornisce diverse opzioni per la soluzione stessa e la relativa area di lavoro. Visualizzare la pagina di riepilogo per una soluzione usando una delle procedure sopra indicate per elencare le soluzioni e quindi fare clic sul nome della soluzione.

![Proprietà della soluzione](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [AZ monitor log-Analytics Solution list](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) per elencare le soluzioni di monitoraggio installate nella sottoscrizione.   Prima di eseguire il `list` comando, attenersi ai prerequisiti disponibili in [installare una soluzione di monitoraggio](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Usare il cmdlet [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) per elencare le soluzioni di monitoraggio installate nella sottoscrizione. Prima di eseguire questi comandi, attenersi ai prerequisiti disponibili in [installare una soluzione di monitoraggio](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Installare una soluzione di monitoraggio

### <a name="portal"></a>[Portale](#tab/portal)

Le soluzioni di monitoraggio di Microsoft e dei partner sono disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com). È possibile cercare e installare le soluzioni disponibili usando la procedura seguente. Quando si installa una soluzione, è necessario selezionare un'[area di lavoro Log Analytics](../platform/manage-access.md) in cui la soluzione verrà installata e in cui verranno raccolti i dati.

1. Nell'[elenco delle soluzioni per la sottoscrizione](#list-installed-monitoring-solutions) fare clic su **Aggiungi**.
1. Individuare o cercare una soluzione. È anche possibile individuare le soluzioni utilizzando questo [collegamento di ricerca](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Individuare la soluzione di monitoraggio desiderata e leggere la relativa descrizione.
1. Fare clic su **Crea** per avviare il processo di installazione.
1. All'avvio del processo di installazione, verrà chiesto di specificare l'area di lavoro Log Analytics e di presentare tutte le configurazioni necessarie per la soluzione.

![Installare una soluzione](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installare una soluzione dalla community

I membri della community possono inviare le soluzioni di gestione come modelli di avvio rapido di Azure. È possibile installare direttamente queste soluzioni o scaricarle per installarle in un secondo momento.

1. Seguire la procedura descritta in [area di lavoro Log Analytics e account di Automazione](#log-analytics-workspace-and-automation-account) per collegare un'area di lavoro e un account.
2. Passare a [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
3. Cercare una soluzione adatta alle proprie esigenze.
4. Selezionare la soluzione nei risultati per visualizzarne i dettagli.
5. Fare clic sul pulsante **Distribuisci in Azure**.
6. Viene richiesto di fornire informazioni come il gruppo di risorse e la posizione, oltre ai valori per i parametri nella soluzione.
7. Fare clic su **Acquista** per installare la soluzione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Installare l'interfaccia della riga di comando di Azure

   È necessario [installare l'interfaccia della](/cli/azure/install-azure-cli) riga di comando di Azure prima di eseguire i comandi di riferimento cli.  Se si preferisce, è anche possibile usare Azure Cloud Shell per completare la procedura descritta in questo articolo.  Azure Cloud Shell è un ambiente di shell interattivo utilizzabile tramite il browser.  Per avviare Cloud Shell, usare uno di questi metodi:

   - Aprire Cloud Shell passando a [https://shell.azure.com](https://shell.azure.com)

   - Selezionare il pulsante **Cloud Shell** sulla barra dei menu nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com)

1. Accedere.

   Se si usa un'installazione locale dell'interfaccia della riga di comando, eseguire l'accesso con il comando [AZ login](/cli/azure/reference-index#az-login) .  Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

    ```azurecli
    az login
    ```

1. Installare l'estensione `log-analytics-solution`

   `log-analytics-solution`Si tratta di un'estensione sperimentale dell'interfaccia della riga di comando di Azure core. Per altre informazioni sui riferimenti all'estensione, vedere [usare l'estensione con l'interfaccia](/cli/azure/azure-cli-extensions-overview?)della riga di comando

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   È previsto il seguente avviso.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Installare una soluzione con l'interfaccia della riga di comando di Azure

Quando si installa una soluzione, è necessario selezionare un'[area di lavoro Log Analytics](../platform/manage-access.md) in cui la soluzione verrà installata e in cui verranno raccolti i dati.  Con l'interfaccia della riga di comando di Azure, è possibile gestire le aree di lavoro usando i comandi [AZ monitor log-Analytics](/cli/azure/monitor/log-analytics/workspace) Reference.  Seguire la procedura descritta in [area di lavoro Log Analytics e account di Automazione](#log-analytics-workspace-and-automation-account) per collegare un'area di lavoro e un account.

Usare la [soluzione AZ monitor log-Analytics create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) per installare una soluzione di monitoraggio.  I parametri tra parentesi quadre sono facoltativi.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Ecco un esempio di codice che crea una soluzione di log Analytics per il prodotto del piano di OMSGallery/contenitori.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Installare Azure PowerShell

   È necessario [installare Azure PowerShell](/powershell/azure/install-az-ps) prima di eseguire Azure PowerShell comandi di riferimento. Se si preferisce, è anche possibile usare Azure Cloud Shell per completare la procedura descritta in questo articolo. Azure Cloud Shell è un ambiente di shell interattivo utilizzabile tramite il browser. Per avviare Cloud Shell, usare uno di questi metodi:

   - Aprire Cloud Shell passando a [https://shell.azure.com](https://shell.azure.com)

   - Selezionare il pulsante **Cloud Shell** sulla barra dei menu nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com)

   > [!IMPORTANT]
   > Mentre il modulo di PowerShell **AZ. MonitoringSolutions** è in anteprima, è necessario installarlo separatamente usando il `Install-Module` cmdlet. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Accedere.

   Se si usa un'installazione locale di PowerShell, accedere con il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Per completare il processo di autenticazione, seguire la procedura visualizzata in PowerShell.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Installare una soluzione con Azure PowerShell

Quando si installa una soluzione, è necessario selezionare un'[area di lavoro Log Analytics](../platform/manage-access.md) in cui la soluzione verrà installata e in cui verranno raccolti i dati. Con Azure PowerShell, è possibile gestire le aree di lavoro usando i cmdlet nel modulo [AZ. MonitoringSolutions](/powershell/module/az.monitoringsolutions) di PowerShell. Seguire la procedura descritta in [area di lavoro Log Analytics e account di Automazione](#log-analytics-workspace-and-automation-account) per collegare un'area di lavoro e un account.

Usare il cmdlet [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) per installare una soluzione di monitoraggio. I parametri tra parentesi quadre sono facoltativi.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

L'esempio seguente crea una soluzione di monitoraggio di log Analytics per l'area di lavoro di log Analytics.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>area di lavoro Log Analytics e account di Automazione

Tutte le soluzioni di monitoraggio richiedono un'[area di lavoro Log Analytics](../platform/manage-access.md) per archiviare i dati raccolti dalla soluzione e per ospitare le ricerche nei log e le visualizzazioni. Alcune soluzioni richiedono anche un [account di Automazione](../../automation/automation-security-overview.md) per contenere i runbook e le relative risorse. L'area di lavoro e l'account devono soddisfare i requisiti indicati di seguito.

* Ogni installazione di una soluzione può usare solo un'area di lavoro Log Analytics e un account di Automazione. È possibile installare la soluzione separatamente in più aree di lavoro.
* Se una soluzione richiede un account di Automazione, l'area di lavoro Log Analytics e l'account di Automazione devono essere collegati tra loro. Un'area di lavoro Log Analytics può essere collegata a un solo account di Automazione e un account di Automazione può essere collegato a una sola area di lavoro Log Analytics.

Quando si installa una soluzione tramite Azure Marketplace, vengono richiesti un'area di lavoro e un account di automazione. Se non sono già collegati, viene creato il collegamento tra di essi.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verificare il collegamento tra un'area di lavoro Log Analytics e un account di Automazione

È possibile verificare il collegamento tra un'area di lavoro Log Analytics e un account Automazione mediante la procedura seguente.

1. Selezionare l'account di Automazione nel portale di Azure.
1. Scorrere fino alla voce **Risorse correlate** del menu e scegliere **Linked workplace** (Area di lavoro collegata).
1. Se l'**area di lavoro** è collegata a un account di Automazione, in questa pagina verrà riportata l'area di lavoro a cui è collegato l'account. Se si seleziona il nome dell'area di lavoro inclusa nell'elenco, si verrà reindirizzati alla pagina di panoramica relativa all'area di lavoro.

## <a name="remove-a-monitoring-solution"></a>Rimuovere una soluzione di monitoraggio

### <a name="portal"></a>[Portale](#tab/portal)

Per rimuovere una soluzione installata tramite il portale, individuarla nell' [elenco delle soluzioni installate](#list-installed-monitoring-solutions). Fare clic sul nome della soluzione per aprire la pagina di riepilogo e quindi fare clic su **Elimina**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per rimuovere una soluzione installata usando l'interfaccia della riga di comando di Azure, usare il comando [AZ monitor log-Analytics Solution Delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete) .

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Per rimuovere una soluzione installata usando Azure PowerShell, usare il cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Passaggi successivi

* Ottenere un [elenco di soluzioni di monitoraggio da Microsoft](../monitor-reference.md).
* Informazioni su come [creare query](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle soluzioni di monitoraggio.
* Vedere tutti i [comandi dell'interfaccia della riga di comando di Azure per Azure monitor](/cli/azure/azure-cli-reference-for-monitor).