---
title: Esempi di avvio rapido con PowerShell per Monitoraggio di Azure. | Microsoft Docs
description: "Usare PowerShell per accedere alle funzionalità di Monitoraggio di Azure, ad esempio scalabilità automatica, avvisi, webhook e ricerca nei log attività."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 66848eee215732803a1070e13d56f35b17e54975
ms.lasthandoff: 03/07/2017


---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Esempi di avvio rapido con PowerShell per Monitoraggio di Azure
Questo articolo illustra comandi di PowerShell di esempio per accedere rapidamente alle funzionalità di Monitoraggio di Azure. Monitoraggio di Azure consente di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web e di inviare notifiche di avviso o chiamare URL Web in base ai valori dei dati di telemetria configurati.

> [!NOTE]
> Dal 25 settembre 2016 Monitoraggio di Azure è il nuovo nome di "Azure Insights". Tuttavia, gli spazi dei nomi e quindi i comandi seguenti contengono ancora il termine "insights".
> 
> 

## <a name="set-up-powershell"></a>Configurare PowerShell
Se non è ancora stato fatto, configurare PowerShell per l'esecuzione sul computer. Per altre informazioni, vedere [Come installare e configurare PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="examples-in-this-article"></a>Esempi in questo articolo
Gli esempi in questo articolo illustrano come usare i cmdlet di Monitoraggio di Azure. È anche possibile esaminare l'elenco completo di cmdlet di PowerShell di Monitoraggio di Azure nell'argomento relativo ai [cmdlet di Monitoraggio di Azure(Azure Insights)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>Eseguire l'acccesso e usare le sottoscrizioni
Per prima cosa, accedere alla propria sottoscrizione di Azure.

```PowerShell
Login-AzureRmAccount
```

Questo richiede di effettuare l’accesso. Dopo aver effettuato l'accesso, vengono visualizzati l'Account, l'ID tenant e l'ID sottoscrizione predefinito. Tutti i cmdlet di Azure funzionano nel contesto della sottoscrizione predefinita. Per visualizzare l'elenco delle sottoscrizioni accessibili, usare il comando seguente.

```PowerShell
Get-AzureRmSubscription
```

Per modificare il contesto di lavoro in una sottoscrizione diversa, usare il comando seguente.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Recuperare il registro attività per una sottoscrizione
Utilizzare il cmdlet `Get-AzureRmLog` .  Di seguito sono riportati alcuni esempi comuni.

Ottenere le voci di log da questa data e ora fino a oggi:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Ottenere le voci di log in un intervallo di date e ore:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log da un gruppo di risorse specifico:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Ottenere le voci di log da un provider di risorse specifico in un intervallo di date e ore:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere tutte le voci di log con un chiamante specifico:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Il comando seguente recupera gli ultimi 1000 eventi dal registro attività:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` supporta diversi altri parametri. Per altre informazioni, vedere il riferimento `Get-AzureRmLog` .

> [!NOTE]
> `Get-AzureRmLog` fornisce solo 15 giorni di cronologia. L’uso del parametro **-MaxEvents** consente di eseguire una query sugli ultimi N eventi, oltre i 15 giorni. Per accedere agli eventi precedenti ai 15 giorni, usare l'API REST o l'SDK (esempio di C# tramite il SDK). Se non si include **StartTime**, il valore predefinito è **EndTime** meno un'ora. Se non si include **EndTime**, il valore predefinito è l’ora corrente. Tutte le ore sono in formato UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recupero della cronologia di avvisi
Per visualizzare tutti gli eventi di avviso, è possibile eseguire query in Azure Resource Manager usando gli esempi seguenti.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Per visualizzare la cronologia per una regola avviso specifica, è possibile utilizzare il cmdlet `Get-AzureRmAlertHistory` passando l'ID risorsa della regola avvisi.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Il cmdlet `Get-AzureRmAlertHistory` supporta diversi parametri. Per altre informazioni, vedere [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Recupero delle informazioni sulle regole di avviso
Tutti i comandi seguenti agiscono su un gruppo di risorse chiamato "montest".

Visualizzare tutte le proprietà della regola di avviso:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperare tutti gli avvisi in un gruppo di risorse:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperare tutte le regole di avviso impostate per una risorsa di destinazione. Ad esempio, tutte le regole di avviso impostate su una VM.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` supporta altri parametri. Per altre informazioni, vedere [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-alert-rules"></a>Creazione di regole di avviso
È possibile utilizzare il cmdlet `Add-AlertRule` per creare, aggiornare o disabilitare una regola di avviso.

È possibile creare proprietà di posta elettronica e webhook usando rispettivamente `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`. Nel cmdlet per la regola avvisi assegnare queste azioni alla proprietà **Actions** della regola avvisi.

La sezione successiva contiene un esempio che illustra come creare una regola di avviso con diversi parametri.

### <a name="alert-rule-on-a-metric"></a>Regola di avviso su una metrica
La tabella seguente descrive i parametri e valori usati per creare un avviso tramite una metrica.

| parametro | value |
| --- | --- |
| Nome |simpletestdiskwrite |
| Posizione di questa regola di avviso |Stati Uniti orientali |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName dell'avviso creato |\PhysicalDisk(_Total)\Disk Writes/sec. Vedere il cmdlet `Get-MetricDefinitions` di seguito per il recupero dei nomi esatti delle metriche |
| operator |GreaterThan |
| Valore soglia (conteggio al secondo per questa metrica) |1 |
| WindowSize (formato hh:mm:ss) |00:05:00 |
| aggregatore (statistica della metrica che usa il numero medio, in questo caso) |Media |
| indirizzi di posta elettronica personalizzati (matrice di stringhe) |'foo@example.com','bar@example.com' |
| invio di messaggi di posta elettronica a proprietari, collaboratori e lettori |-SendToServiceOwners |

Creare un'azione Email

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Creazione di un’azione Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creazione di una regola di avviso sulla metrica CPU% per una VM classica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recupero di una regola di avviso

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Il cmdlet Aggiungi avviso aggiorna anche la regola se esiste già una regola di avviso per le proprietà specificate. Per disabilitare una regola di avviso, includere il parametro **-DisableRule**.

### <a name="alert-on-activity-log-event"></a>Creazione di avvisi su eventi del registro attività
> [!NOTE]
> Questa funzionalità è disponibile in anteprima e verrà rimossa in futuro, perché verrà sostituita.
> 
> 

In questo scenario viene inviato un messaggio di posta elettronica quando un sito Web viene avviato correttamente nella sottoscrizione personale del gruppo di risorse *abhingrgtest123*.

Configurazione di una regola di posta elettronica

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurazione di una regola di webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creazione di una regola per l’evento

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recupero di una regola di avviso

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

Il cmdlet `Add-AlertRule` ammette diversi parametri supplementari. Per altre informazioni, vedere [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Acquisizione di un elenco delle metriche disponibili per gli avvisi
Usare il cmdlet `Get-AzureRmMetricDefinition` per visualizzare l'elenco di tutte le metriche per una specifica risorsa.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

L'esempio seguente genera una tabella con la metrica Name e il  relativo valore Unit.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Un elenco completo delle opzioni disponibili per `Get-AzureRmMetricDefinition` si trova in [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-autoscale-settings"></a>Creazione e gestione delle impostazioni di scalabilità automatica
Una risorsa, ad esempio un'app Web, una macchina virtuale, un servizio cloud o un set di scalabilità di macchine virtuali, può avere una sola impostazione di scalabilità automatica configurata.
Tuttavia, ogni impostazione di scalabilità automatica può includere diversi profili. Ad esempio, un profilo di scalabilità in base alle prestazioni e un altro profilo basato sulla pianificazione. Ogni profilo può avere più regole associate configurate. Per altre informazioni sulla scalabilità automatica, vedere [Come configurare la scalabilità automatica di un servizio cloud](../cloud-services/cloud-services-how-to-scale.md).

Ecco i passaggi da utilizzare:

1. Creare le regole.
2. Creare i profili eseguendo il mapping delle regole create in precedenza.
3. Facoltativo: creare notifiche per la scalabilità automatica configurando le proprietà di webhook e posta elettronica.
4. Creare un'impostazione di scalabilità automatica con un nome per la risorsa di destinazione associando profili e notifiche creati nei passaggi precedenti.

Gli esempi seguenti illustrano come creare un'impostazione di scalabilità automatica per un set di scalabilità di macchine virtuali per un sistema operativo Windows in base alla metrica di utilizzo della CPU.

Per prima cosa, creare una regola per aumentare il numero di istanze, con un incremento del numero di istanze.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Creare poi una regola per ridurre il numero di istanze, con una diminuzione di una istanza.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

A questo punto, creare un profilo per le regole.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Creare una proprietà webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Creare la proprietà di notifica per l'impostazione di scalabilità automatica, tra cui posta elettronica e webhook create in precedenza.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Infine, creare l'impostazione di scalabilità automatica da aggiungere al profilo appena creato.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Per altre informazioni sulla gestione delle impostazioni di scalabilità automatica, vedere [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Cronologia di scalabilità automatica
Il seguente esempio illustra come visualizzare gli eventi di scalabilità automatica e avviso recenti. Usare la ricerca dei registri attività per consultare la cronologia di scalabilità automatica.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

È possibile usare il cmdlet `Get-AzureRmAutoScaleHistory` per recuperare la cronologia di scalabilità automatica.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Per altre informazioni, vedere [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Visualizzazione dei dettagli per un'impostazione di scalabilità automatica
È possibile usare il cmdlet `Get-Autoscalesetting` per recuperare altre informazioni sull'impostazione di scalabilità automatica.

L'esempio seguente mostra dettagli su tutte le impostazioni di scalabilità automatica nel gruppo di risorse 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

L'esempio seguente mostra i dettagli su tutte le impostazioni di scalabilità automatica nel gruppo di risorse 'myrg1' e in particolare l'impostazione di scalabilità automatica denominata 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Rimozione di un'impostazione di scalabilità automatica
È possibile usare il cmdlet `Remove-Autoscalesetting` per eliminare un'impostazione di scalabilità automatica.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gestione dei profili di log per i registri attività
È possibile creare un *profilo di log* ed esportare i dati dai registri attività in un account di archiviazione ed è possibile configurare la relativa conservazione dei dati. Facoltativamente, è inoltre possibile trasmettere i dati all'hub eventi. Questa funzionalità attualmente è in anteprima ed è possibile creare solo un profilo di log per ogni sottoscrizione. Per creare e gestire i profili di log, è possibile usare i cmdlet seguenti con la sottoscrizione corrente. È anche possibile scegliere una sottoscrizione specifica. Anche se PowerShell usa la sottoscrizione corrente per impostazione predefinita, è sempre possibile modificarla usando `Set-AzureRmContext`. È possibile configurare i registri attività per indirizzare i dati a qualsiasi account di archiviazione o all'hub eventi all'interno di tale sottoscrizione. I dati sono scritti come file di BLOB in formato JSON.

### <a name="get-a-log-profile"></a>Acquisizione di un profilo di log
Per recuperare i profili di log esistenti, usare il cmdlet `Get-AzureRmLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Aggiunta di un profilo di log senza conservazione dei dati
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Aggiunta di un profilo di log con conservazione dei dati
È possibile specificare la proprietà **-RetentionInDays** con il numero di giorni, sotto forma di numero intero positivo, per i quali i dati vengono conservati.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Aggiunta di un profilo di log con conservazione e hub di eventi
Oltre a instradare i dati a un account di archiviazione, è anche possibile trasmetterli all'hub eventi. In questa versione di anteprima, la configurazione dell'account di archiviazione è obbligatoria, mentre quella dell’hub di eventi è facoltativo.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurazione dei log di diagnostica
Molti servizi di Azure fornisce log e dati di telemetria aggiuntivi che possono essere configurati per salvare i dati nell'account di Archiviazione di Azure, inviare dati all'hub eventi e/o inviare dati a un'area di lavoro di Log Analytics di OMS. Tale operazione può essere eseguita solo a livello di risorse e l'account di archiviazione o l'hub eventi deve essere presente nella stessa area come risorsa di destinazione in cui viene configurata l'impostazione di diagnostica.

### <a name="get-diagnostic-setting"></a>Acquisizione dell’impostazione di diagnostica
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Disabilitazione dell’impostazione di diagnostica

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Abilitazione dell'impostazione di diagnostica senza conservazione

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Abilitazione dell'impostazione di diagnostica con conservazione

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Abilitazione dell’impostazione di diagnostica con conservazione per una categoria di log specifica

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Abilitazione dell'impostazione di diagnostica per hub eventi

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Abilitazione dell'impostazione di diagnostica per OMS

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId 76d785fd-d1ce-4f50-8ca3-858fc819ca0f -Enabled $true

```

