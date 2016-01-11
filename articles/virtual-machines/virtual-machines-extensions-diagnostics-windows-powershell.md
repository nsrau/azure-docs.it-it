<properties pageTitle="Abilitare la diagnostica in una macchina virtuale di Azure che esegue Windows mediante PowerShell | Microsoft Azure" description="Informazioni sull'uso di PowerShell per abilitare la diagnostica in una macchina virtuale di Azure che esegue Windows" services="virtual-machines" documentationCenter="" authors="sbtron" manager="" editor="""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="saurabh"/>


# Abilitare la diagnostica in una macchina virtuale di Azure che esegue Windows mediante PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

È possibile raccogliere dati di diagnostica come log delle applicazioni, contatori delle prestazioni e così via da una macchina virtuale di Azure che esegue Windows mediante l'estensione Diagnostica di Azure. Questo articolo descrive come abilitare l'estensione Diagnostica di Azure per una macchina virtuale di Azure mediante PowerShell. Per i prerequisiti necessari per questo articolo, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

## Abilitare l'estensione Diagnostica di Azure in una macchina virtuale mediante un modello di distribuzione di Gestione risorse

È possibile abilitare l'estensione di diagnostica durante la creazione di una macchina virtuale Windows con il modello di distribuzione di Gestione risorse aggiungendo la configurazione dell'estensione al modello di Gestione risorse. Vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure](virtual-machines-extensions-diagnostics-windows-template.md).

Per abilitare l'estensione Diagnostica di Azure in una macchina virtuale esistente creata mediante il modello di distribuzione di Gestione risorse, è possibile usare il cmdlet di PowerShell [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) come illustrato di seguito.


	$vm_resourcegroup = "myvmresourcegroup"
	$vm_name = "myvm"
	$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"
	
	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path 


*$diagnosticsconfig\_path* è il percorso del file contenente la configurazione di diagnostica in formato xml, come descritto nell'[esempio](#sample-diagnostics-configuration) di seguito.

Se il file di configurazione di diagnostica specifica un elemento **StorageAccount** con il nome di un account di archiviazione, lo script *Set AzureRMVMDiagnosticsExtension* imposterà automaticamente l'estensione di diagnostica per l'invio dei dati di diagnostica a quell'account di archiviazione. Affinché tutto funzioni come previsto, l'account di archiviazione deve trovarsi nella stessa sottoscrizione della macchina virtuale.

Se nella configurazione di diagnostica non è stato specificato un elemento **StorageAccount**, è necessario passare il parametro *StorageAccountName* al cmdlet. Se viene specificato il parametro *StorageAccountName*, il cmdlet userà sempre l'account di archiviazione specificato nel parametro e non quello specificato nel file di configurazione di diagnostica.

Se l'account di archiviazione di diagnostica si trova in una sottoscrizione diversa da quella della macchina virtuale, è necessario passare in modo esplicito i parametri *StorageAccountName* e *StorageAccountKey* al cmdlet. Il parametro *StorageAccountKey* non è necessario quando l'account di archiviazione di diagnostica si trova nella stessa sottoscrizione della macchina virtuale, in quanto il cmdlet può automaticamente eseguire una query e impostare il valore della chiave durante l'abilitazione dell'estensione di diagnostica. Se l'account di archiviazione di diagnostica si trova tuttavia in una sottoscrizione diversa, il cmdlet potrebbe non essere in grado di ottenere automaticamente la chiave. In tal caso è necessario specificare la chiave in modo esplicito tramite il parametro *StorageAccountKey*.

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key	

Dopo aver abilitato l'estensione di diagnostica di Azure in una macchina virtuale, è possibile ottenere le impostazioni correnti mediante il cmdlet [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx).

	Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

L'oggetto *PublicSettings* restituito dal cmdlet contiene la configurazione xml in un formato con codifica Base64. Per leggere la configurazione xml, sarà necessario decodificarla.
	
	$publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
	$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
	$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
	Write-Host $xmlconfig
 
Il cmdlet [Remove AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) può essere usato per rimuovere l'estensione di diagnostica dalla macchina virtuale.
  
## Abilitare l'estensione di diagnostica di Azure in una macchina virtuale mediante un modello di distribuzione classica

Il cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) può essere usato per abilitare l'estensione Diagnostica di Azure in una macchina virtuale creata mediante il modello di distribuzione classica. L'esempio seguente illustra come creare una nuova macchina virtuale mediante il modello di distribuzione classica con l'estensione Diagnostica di Azure abilitata.

	$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
	$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
	$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Per abilitare l'estensione Diagnostica di Azure in una macchina virtuale esistente (classica) usare innanzitutto il cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) per ottenere la configurazione della macchina virtuale. Aggiornare quindi la configurazione della macchina virtuale per includere l'estensione Diagnostica di Azure mediante il cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx). Applicare infine la configurazione aggiornata alla macchina virtuale mediante [Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx).

	$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
	$VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## Configurazione di diagnostica di esempio

Per la configurazione pubblica di diagnostica con gli script precedenti, è possibile usare il codice xml seguente. Questa configurazione di esempio trasferirà diversi contatori di prestazioni all'account di archiviazione di diagnostica insieme agli errori dai canali Application, Security e System nei log eventi di Windows e a eventuali errori dai registri dell'infrastruttura di diagnostica.

La configurazione deve essere aggiornata per includere gli elementi seguenti:

- L'attributo *resourceID* dell'elemento **Metrics** deve essere aggiornato con l'ID della risorsa per la macchina virtuale. 
	- L'ID della risorsa può essere creato mediante il modello seguente: "/subscriptions/{*ID della sottoscrizione con la macchina virtuale*}/resourceGroups/{*Nome del gruppo di risorse per la macchina virtuale*}/providers/Microsoft.Compute/virtualMachines/ {*Nome della macchina virtuale*}". 
	- Se ad esempio l'ID della sottoscrizione in cui è in esecuzione la macchina virtuale è **11111111-1111-1111-1111-111111111111**, il nome del gruppo di risorse è **MyResourceGroup** e il nome della macchina virtuale è **MyWindowsVM**, il valore di *resourceID* sarà:
	 
		```
		<Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
		```
	- Per altre informazioni sulla generazione delle metriche in base alla configurazione di metriche e contatori delle prestazioni, vedere la pagina relativa alle [tabelle WADMetrics nell'archiviazione](virtual-machines-extensions-diagnostics-windows-template.md#wadmetrics-tables-in-storage)

- L'elemento **StorageAccount** deve essere aggiornato con il nome dell'account di archiviazione di diagnostica.
 
	```
	<?xml version="1.0" encoding="utf-8"?>
	<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
	        <PerformanceCounters scheduledTransferPeriod="PT1M">
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU utilization" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU privileged time" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU user time" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
	        <annotation displayName="CPU frequency" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Processes" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Threads" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Handles" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Memory usage" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory available" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory committed" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory commit limit" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory paged pool" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory non-paged pool" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active time" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active read time" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active write time" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk operations" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk read operations" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk write operations" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk speed" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk read speed" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk write speed" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average queue length" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average read queue length" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average write queue length" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk free space (percentage)" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk free space (MB)" locale="it-IT"/>
	      </PerformanceCounterConfiguration>
	    </PerformanceCounters>
		<Metrics resourceId="(Update with resource ID for the VM)" >
	        <MetricAggregation scheduledTransferPeriod="PT1H"/>
	        <MetricAggregation scheduledTransferPeriod="PT1M"/>
	    </Metrics>
	    <WindowsEventLog scheduledTransferPeriod="PT1M">
	      <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
	      <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
	      <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
	    </WindowsEventLog>
	      </DiagnosticMonitorConfiguration>
	    </WadCfg>
	    <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
	</PublicConfig>
	```

## Passaggi successivi 
- Per altre istruzioni sull'uso della diagnostica di Azure e di altre tecniche per la risoluzione dei problemi, vedere [Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure](cloud-services-dotnet-diagnostics.md).
- Lo [schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/mt634524.aspx) illustra le varie opzioni di configurazione xml per l'estensione di diagnostica.

<!---HONumber=AcomDC_1223_2015-->