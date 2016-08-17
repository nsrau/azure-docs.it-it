<properties
	pageTitle="Azure Insights: esempi di avvio rapido di PowerShell Azure Insights. | Microsoft Azure"
	description="I comandi PowerShell di esempio per l’avvio rapido di Azure Insights aiutano ad accedere rapidamente alle funzionalità di monitoraggio di Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Esempi di avvio rapido con PowerShell per Azure Insights

Questo articolo illustra comandi PowerShell di esempio per accedere rapidamente alle funzionalità di monitoraggio di Azure Insights. Azure Insights consente di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web oltre che inviare notifiche di avviso o chiamare URL Web in base ai valori di dati di telemetria configurati.

## Configurare PowerShell
Se non è ancora stato fatto, configurare PowerShell per l'esecuzione sul computer. Per altre informazioni, vedere [Come installare e configurare PowerShell](../powershell-install-configure.md).

## Esempi in questo articolo

Gli esempi in questo articolo illustrano come usare i cmdlet di Azure Insights. È anche possibile esaminare l'elenco completo di cmdlet di Azure Insights (monitoraggio) in [Cmdlet di Azure Insights](https://msdn.microsoft.com/library/mt282452.aspx).


## Eseguire l'acccesso e usare le sottoscrizioni

Per prima cosa, accedere alla propria sottoscrizione di Azure.

```
Login-AzureRmAccount
```

Questo richiede di effettuare l’accesso. Una volta effettuato l’accesso, vengono visualizzati Account, ID tenantId e ID sottoscrizione predefinito. Tutti i cmdlet di Azure operano nel contesto della sottoscrizione predefinita. Per visualizzare l'elenco delle sottoscrizioni accessibili, usare il comando seguente.

```
Get-AzureRmSubscription
```

Per modificare il contesto operativo in una sottoscrizione diversa, utilizzare il comando seguente.

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## Recuperare i log di controllo per una sottoscrizione
Utilizzare il cmdlet `Get-AzureRmLog`. Di seguito sono riportati alcuni esempi comuni.

Ottenere le voci di log da questa data e ora fino a oggi:

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Ottenere le voci di log in un intervallo di date e ore:

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log da un gruppo di risorse specifico:

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Ottenere le voci di log da un provider di risorse specifico in un intervallo di date e ore:

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere tutte le voci di log con un chiamante specifico:

```
Get-AzureRmLog -Caller 'myname@company.com'
```

Il comando seguente recupera gli ultimi 1000 eventi dal log di controllo:

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` supporta diversi altri parametri. Per altre informazioni, vedere il riferimento `Get-AzureRmLog`.

>[AZURE.NOTE] `Get-AzureRmLog` fornisce solo 15 giorni di cronologia. L’uso del parametro **-MaxEvents** consente di eseguire una query sugli ultimi N eventi, oltre i 15 giorni. Per accedere agli eventi precedenti ai 15 giorni, usare l'API REST o l'SDK (esempio di C# tramite il SDK). Se non si include **StartTime**, il valore predefinito è **EndTime** meno un'ora. Se non si include **EndTime**, il valore predefinito è l’ora corrente. Tutte le ore sono in formato UTC.

## Recupero della cronologia di avvisi
Per visualizzare tutti gli eventi di avviso, è possibile ricercare nei registri di Azure Resource Manager (ARM) utilizzando gli esempi seguenti.

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Per visualizzare la cronologia per una regola avviso specifica, è possibile utilizzare il cmdlet `Get-AzureRmAlertHistory` passando l'ID risorsa della regola avvisi.

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Il cmdlet `Get-AzureRmAlertHistory` supporta diversi parametri. Per altre informazioni, vedere [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## Recupero delle informazioni sulle regole di avviso
Tutti i comandi seguenti agiscono su un gruppo di risorse chiamato "montest".

Visualizzare tutte le proprietà della regola di avviso:

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperare tutti gli avvisi in un gruppo di risorse:

```
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperare tutte le regole di avviso impostate per una risorsa di destinazione. Ad esempio, tutte le regole di avviso impostate su una VM.

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` supporta altri parametri. Per altre informazioni, vedere [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx).

## Creazione di regole di avviso
È possibile utilizzare il cmdlet `Add-AlertRule` per creare, aggiornare o disabilitare una regola di avviso.

È possibile creare le proprietà di posta elettronica e webhook utilizzando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, rispettivamente. Nel cmdlet per la regola avvisi assegnare queste azioni alla proprietà **Actions** della regola avvisi.

La sezione successiva contiene un esempio che illustra come creare una regola di avviso con diversi parametri.

### Regola di avviso su una metrica
La tabella seguente descrive i parametri e valori usati per creare un avviso tramite una metrica.


|parametro|value|
|---|---|
|Nome|	simpletestdiskwrite|
|Posizione di questa regola di avviso|	Stati Uniti orientali|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName dell'avviso creato|	\\PhysicalDisk(\_Total)\\Disk Writes/sec. Vedere il cmdlet `Get-MetricDefinitions` di seguito per il recupero dei nomi esatti delle metriche|
|operator|	GreaterThan|
|Valore soglia (conteggio al secondo per questa metrica)|	1|
|WindowSize (formato hh:mm:ss)|	00:05:00|
|aggregatore (statistica della metrica che usa il numero medio, in questo caso)|	Media|
|indirizzi di posta elettronica personalizzati (matrice di stringhe)|'foo@example.com','bar@example.com'|
|invio di messaggi di posta elettronica a proprietari, collaboratori e lettori|	-SendToServiceOwners|

Creare un'azione Email

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Creazione di un’azione Webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creazione di una regola di avviso sulla metrica CPU% per una VM classica

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recupero di una regola di avviso

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Il cmdlet Aggiungi avviso aggiorna anche la regola se esiste già una regola di avviso per le proprietà specificate. Per disabilitare una regola di avviso, includere il parametro **-DisableRule**.

### Avviso per un evento del registro eventi

>[AZURE.NOTE] Questa funzionalità è ancora in anteprima.

In questo scenario viene inviato un messaggio di posta elettronica quando un sito Web viene avviato correttamente nella sottoscrizione personale del gruppo di risorse *abhingrgtest123*.

Configurazione di una regola di posta elettronica

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurazione di una regola di webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creazione di una regola per l’evento

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recupero di una regola di avviso

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

Il cmdlet `Add-AlertRule` ammette diversi parametri supplementari. Per altre informazioni, vedere [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## Acquisizione di un elenco delle metriche disponibili per gli avvisi
Usare il cmdlet `Get-AzureRmMetricDefinition` per visualizzare l'elenco di tutte le metriche per una specifica risorsa.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

L'esempio seguente genera una tabella con la metrica Name e il relativo valore Unit.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Un elenco completo delle opzioni disponibili per `Get-AzureRmMetricDefinition` si trova in [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## Creazione e gestione delle impostazioni di scalabilità automatica
Una risorsa, ad esempio un'app Web, una macchina virtuale, un servizio cloud o un set di scalabilità di macchine virtuali, può avere una sola impostazione di scalabilità automatica configurata. Tuttavia, ogni impostazione di scalabilità automatica può includere diversi profili. Ad esempio, un profilo di scalabilità in base alle prestazioni e un altro profilo basato sulla pianificazione. Ogni profilo può avere più regole associate configurate. Per altre informazioni sulla scalabilità automatica, vedere [Come configurare la scalabilità automatica di un servizio cloud](../cloud-services/cloud-services-how-to-scale.md).

Ecco i passaggi da utilizzare:

1. Creare le regole.
2. Creare i profili eseguendo il mapping delle regole create in precedenza.
3. Facoltativo: creare notifiche per la scalabilità automatica configurando le proprietà di webhook e posta elettronica.
4. Creare un'impostazione di scalabilità automatica con un nome per la risorsa di destinazione associando profili e notifiche creati nei passaggi precedenti.

Gli esempi seguenti illustrano come creare un'impostazione di scalabilità automatica per un set di scalabilità di macchine virtuali per un sistema operativo Windows in base alla metrica di utilizzo della CPU.

Per prima cosa, creare una regola per aumentare il numero di istanze, con un incremento del numero di istanze.

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

Creare poi una regola per ridurre il numero di istanze, con una diminuzione di una istanza.

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

A questo punto, creare un profilo per le regole.

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Creare una proprietà webhook.

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Creare la proprietà di notifica per l'impostazione di scalabilità automatica, tra cui posta elettronica e webhook create in precedenza.

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Infine, creare l'impostazione di scalabilità automatica da aggiungere al profilo appena creato.

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Per altre informazioni sulla gestione delle impostazioni di scalabilità automatica, vedere [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## Cronologia di scalabilità automatica
Il seguente esempio illustra come visualizzare gli eventi di scalabilità automatica e avviso recenti. Utilizzare la ricerca dei log di controllo per consultare la cronologia di scalabilità automatica.

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

È possibile usare il cmdlet `Get-AzureRmAutoScaleHistory` per recuperare la cronologia di scalabilità automatica.

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Per altre informazioni, vedere [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### Visualizzazione dei dettagli per un'impostazione di scalabilità automatica
È possibile usare il cmdlet `Get-Autoscalesetting` per recuperare altre informazioni sull'impostazione di scalabilità automatica.

L'esempio seguente mostra dettagli su tutte le impostazioni di scalabilità automatica nel gruppo di risorse 'myrg1'.

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

L'esempio seguente mostra i dettagli su tutte le impostazioni di scalabilità automatica nel gruppo di risorse 'myrg1' e in particolare l'impostazione di scalabilità automatica denominata 'MyScaleVMSSSetting'.

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### Rimozione di un'impostazione di scalabilità automatica
È possibile usare il cmdlet `Remove-Autoscalesetting` per eliminare un'impostazione di scalabilità automatica.

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## Gestione dei profili di log per i log di controllo

È possibile creare un *profilo di log* ed esportare i dati dai log di controllo in un account di archiviazione ed è possibile configurare la relativa conservazione dei dati. Facoltativamente, è inoltre possibile trasmettere i dati all'hub eventi. Questa funzionalità attualmente è in anteprima ed è possibile creare solo un profilo di log per ogni sottoscrizione. Per creare e gestire i profili di log, è possibile utilizzare i cmdlet seguenti con la sottoscrizione corrente. È anche possibile scegliere una sottoscrizione specifica. Anche se PowerShell usa la sottoscrizione corrente per impostazione predefinita, è sempre possibile modificarla usando `Set-AzureRmContext`. È possibile configurare i log di controllo per indirizzare dati a qualsiasi account di archiviazione o all'hub eventi all'interno di tale sottoscrizione. I dati sono scritti come file di BLOB in formato JSON.

### Acquisizione di un profilo di log
Per recuperare i profili di log esistenti, usare il cmdlet `Get-AzureRmLogProfile`.

### Aggiunta di un profilo di log senza conservazione dei dati

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Rimozione di un profilo di log

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### Aggiunta di un profilo di log con conservazione dei dati

È possibile specificare la proprietà **-RetentionInDays** con il numero di giorni, sotto forma di numero intero positivo, per i quali i dati vengono conservati.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### Aggiunta di un profilo di log con conservazione e hub di eventi
Oltre a instradare i dati a un account di archiviazione, è anche possibile trasmetterli all'hub eventi. In questa versione di anteprima, la configurazione dell'account di archiviazione è obbligatoria, mentre quella dell’hub di eventi è facoltativo.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## Configurazione dei log di diagnostica
Molti servizi di Azure forniscono log e telemetria aggiuntivi, inclusi gruppi di sicurezza di rete di Azure, servizi di bilanciamento del carico software, insieme di credenziali chiave, servizi di Ricerca di Azure e app per la logica e possono essere configurati per salvare i dati nell'account di archiviazione di Azure. Tale operazione può essere eseguita solo a livello di risorse e l'account di archiviazione deve essere presente nella stessa area come risorsa di destinazione in cui viene configurata l'impostazione di diagnostica.

### Acquisizione dell’impostazione di diagnostica

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Disabilitazione dell’impostazione di diagnostica

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Abilitazione dell'impostazione di diagnostica senza conservazione

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Abilitazione dell'impostazione di diagnostica con conservazione

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Abilitazione dell’impostazione di diagnostica con conservazione per una categoria di log specifica

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0803_2016-->