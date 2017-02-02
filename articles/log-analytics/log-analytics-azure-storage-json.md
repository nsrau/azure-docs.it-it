---
title: Analizzare i log di diagnostica di Azure con Log Analytics | Documentazione Microsoft
description: "Log Analytics può leggere i log dei servizi di Azure che scrivono i log di diagnostica di Azure in un archivio BLOB in formato JSON."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: adf2f366-ea98-4250-ae66-6d2cfce5b4f9
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: d0b98fe9c3ec685e95b5a8cf96afe1fb72c659f2
ms.openlocfilehash: 76dfc064d4c50f291e48ce35435229da1323a520


---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analizzare i log di diagnostica di Azure con Log Analytics
Log Analytics può raccogliere i log dei servizi di Azure riportati di seguito, che scrivono i [log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) in un archivio BLOB in formato JSON:

* Insieme di credenziali delle chiavi (anteprima)
* Gateway applicazione (anteprima)
* Gruppo di sicurezza di rete (anteprima)

> [!NOTE]
> Questo metodo di raccolta dei log è deprecato. Per raccogliere i log per i servizi riportati sopra usare la [scrittura diretta dei dati di diagnostica di Azure in Log Analytics](log-analytics-azure-storage.md). Quando le soluzioni di gestione Key Vault Analytics e Azure Networking Analytics saranno aggiornate e supporteranno i log raccolti direttamente dalla diagnostica di Azure, questa documentazione verrà eliminata.
>
>

Le sezioni seguenti illustrano l'uso di PowerShell per:

* Configurare Log Analytics per la raccolta dei log dall'archivio per ciascuna risorsa  
* Abilitare la soluzione di Log Analytics per il servizio di Azure

Affinché Log Analytics possa raccogliere i dati per tali risorse, è necessario abilitare la diagnostica di Azure. Usare il cmdlet `Set-AzureRmDiagnosticSetting` per abilitare la registrazione.

Per altre informazioni su come abilitare la registrazione diagnostica, vedere gli articoli seguenti:

* [Insieme di credenziali di chiave](../key-vault/key-vault-logging.md)
* [Gateway applicazione](../application-gateway/application-gateway-diagnostics.md)
* [Gruppo di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md)

Questa documentazione include anche informazioni dettagliate su:

* Risoluzione dei problemi di raccolta dati
* Arresto della raccolta dati

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurare Log Analytics per la raccolta dei log di diagnostica di Azure
Per raccogliere i log di questi servizi e abilitarne la visualizzazione nella soluzione vengono usati script di PowerShell.

L'esempio seguente abilita la registrazione in tutte le risorse supportate.

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Per alcune risorse è possibile eseguire la configurazione precedente dal portale di Azure seguendo la procedura descritta in [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurare Log Analytics per la raccolta dei log di diagnostica di Azure
Microsoft offre un modulo di script di PowerShell che consente di esportare due cmdlet per facilitare la configurazione di Log Analytics:

1. `Add-AzureDiagnosticsToLogAnalyticsUI` richiede l'input dell'utente e può impostare semplici configurazioni
2. `Add-AzureDiagnosticsToLogAnalytics` usa le risorse da monitorare come input e quindi configura Log Analytics  

### <a name="pre-requisites"></a>Prerequisiti
1. Azure PowerShell con versione 1.0.8 o successiva dei cmdlet di Operational Insights.
   * [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs)
   * Verificare la versione dei cmdlet: `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. La registrazione diagnostica è configurata per la risorsa di Azure da monitorare. Usare `Set-AzureRmDiagnosticSetting` oppure vedere l'articolo su come [usare Log Analytics per raccogliere dati da account di archiviazione di Azure](log-analytics-azure-storage.md) per informazioni su come abilitare la diagnostica.
3. Un'area di lavoro di [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. Modulo AzureDiagnosticsAndLogAnalytics di PowerShell
   * Scaricare il modulo [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) da PowerShell Gallery

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Opzione 1: Eseguire gli script di configurazione interattivi
Aprire PowerShell ed eseguire:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Verrà visualizzato un elenco di opzioni disponibili e verrà richiesto di effettuare una selezione.
Verrà chiesto di effettuare una selezione per ognuno degli elementi seguenti:

* Tipi di risorse (Servizi di Azure) da cui raccogliere i log
* Istanze delle risorse da cui raccogliere i log
* Area di lavoro di Log Analytics per raccogliere i dati

Dopo aver eseguito questo script, i record verranno visualizzati in Log Analytics circa 30 minuti dopo la scrittura dei nuovi dati di diagnostica nell'archivio. Se i record non sono disponibili dopo questo periodo di tempo, fare riferimento alla sezione sulla risoluzione dei problemi riportata di seguito.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opzione 2: Compilare un elenco di risorse e passarle al cmdlet di configurazione
È possibile compilare un elenco di risorse in cui è abilitata la diagnostica di Azure e quindi passare le risorse al cmdlet di configurazione.

Per visualizzare informazioni aggiuntive sul cmdlet, eseguire `Get-Help Add-AzureDiagnosticsToLogAnalytics`.

Per altri dettagli su OMS, vedere l'articolo sui [cmdlet di PowerShell per Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx).

> [!NOTE]
> Se la risorsa e l'area di lavoro si trovano in sottoscrizioni di Azure diverse, passare da una sottoscrizione all'altra in base alle esigenze con `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`
>
>

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Dopo aver eseguito questo script, i record verranno visualizzati in Log Analytics circa 30 minuti dopo la scrittura dei nuovi dati di diagnostica nell'archivio. Se i record non sono disponibili dopo questo periodo di tempo, fare riferimento alla sezione sulla risoluzione dei problemi riportata di seguito.  

> [!NOTE]
> La configurazione non è visibile nel portale di Azure. È possibile verificare la configurazione tramite il cmdlet `Get-AzureRmOperationalInsightsStorageInsight` .  
>
>

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Arrestare la raccolta dei log di diagnostica di Azure in Log Analytics
Per eliminare la configurazione di Log Analytics per una risorsa, usare il cmdlet `Remove-AzureRmOperationalInsightsStorageInsight`.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Risoluzione dei problemi di configurazione per i log di diagnostica di Azure
*Problema*

Quando si configura una risorsa che esegue la registrazione nell'archiviazione classica, viene visualizzato l'errore seguente:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Risoluzione*

Accedere all'API per il modello di distribuzione classica con `Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Risoluzione dei problemi di raccolta dati per i log di diagnostica di Azure
Se per una risorsa di Azure non vengono visualizzati dati in Log Analytics, seguire questa procedura di risoluzione dei problemi:

* Verificare il flusso dei dati all'account di archiviazione
* Verificare che la soluzione di Log Analytics per il servizio sia abilitata
* Verificare che Log Analytics sia configurato per la lettura dall'archivio
* Aggiornare la chiave dell'account di archiviazione

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Verificare il flusso dei dati all'account di archiviazione
È possibile controllare il flusso dei dati all'account di archiviazione con uno strumento che consenta di esplorare le risorse di archiviazione di Azure (ad esempio, Visual Studio) o con PowerShell.

Per trovare l'account di archiviazione a cui accede la risorsa, usare il comando di PowerShell seguente:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Il contenitore di archiviazione usato da Diagnostica di Azure ha un nome con il formato seguente:  

`insights-logs-<Category>`

Per altre informazioni sulla visualizzazione del contenuto dell'account di archiviazione, vedere l'articolo sull'[uso dei cmdlet di Archiviazione per verificare la presenza di dati recenti in un contenitore](../storage/storage-powershell-guide-full.md).

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Verificare che la soluzione di Log Analytics per il servizio sia abilitata
Se si usa `Add-AzureDiagnosticsToLogAnalyticsUI`, la soluzione corretta di Log Analytics viene abilitata automaticamente.

Per verificare se è abilitata una soluzione, eseguire il comando di PowerShell seguente:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Se la soluzione non è abilitata, è possibile abilitarla tramite il comando di PowerShell seguente:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Per trovare il nome della soluzione da abilitare per ogni tipo di risorsa, usare il comando di PowerShell seguente (questa variabile è disponibile dopo l'importazione del modulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Verificare che Log Analytics sia configurato per la lettura dall'archivio
Se si aggiungono altre risorse di Azure, è necessario abilitare la registrazione diagnostica e configurare Log Analytics per tali risorse.
Per verificare le risorse e gli account di archiviazione per cui in Log Analytics è configurata la raccolta dei log, usare il comando di PowerShell seguente:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Aggiornare la chiave di archiviazione
Se si modifica la chiave per l'account di archiviazione, è necessario aggiornare anche la configurazione di Log Analytics con la nuova chiave.
Per aggiornare la configurazione di Log Analytics con una nuova chiave, usare il comando di PowerShell seguente:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Per trovare il nome dell'archiviazione di Operational Insights, usare il cmdlet `Get-AzureRmOperationalInsightsStorageInsight` , come illustrato negli esempi precedenti.

## <a name="next-steps"></a>Passaggi successivi
* [Usare l'archiviazione BLOB per IIS e l'archiviazione tabelle per gli eventi](log-analytics-azure-storage-iis-table.md) per leggere i log dei servizi di Azure che scrivono dati di diagnostica in un archivio tabelle o i log IIS scritti in un archivio BLOB.
* [Abilitare soluzioni](log-analytics-add-solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](log-analytics-log-searches.md) per analizzare i dati.



<!--HONumber=Dec16_HO3-->


