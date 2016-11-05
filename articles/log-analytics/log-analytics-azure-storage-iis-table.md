---
title: Uso dell'archivio BLOB per IIS e dell'archivio tabelle per gli eventi | Microsoft Docs
description: Log Analytics è in grado di leggere i log per i servizi di Azure che scrivono dati di diagnostica nell'archivio tabelle o log di IIS nell'archivio BLOB.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Uso dell'archivio BLOB per IIS e dell'archivio tabelle per gli eventi
Log Analytics è in grado di leggere i log per i servizi seguenti che scrivono dati di diagnostica nell'archivio tabelle o log di IIS nell'archivio BLOB:

* Cluster di Service Fabric (Anteprima)
* Macchine virtuali
* Ruoli di lavoro/Web

Affinché Log Analytics possa raccogliere i dati per tali risorse, è necessario abilitare la diagnostica di Azure.

Dopo l'abilitazione della diagnostica, è possibile usare il portale di Azure o PowerShell per configurare Log Analytics per la raccolta dei log.

Diagnostica di Azure è un'estensione di Azure che consente di raccogliere i dati di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati vengono memorizzati in un account di archiviazione di Azure e possono quindi essere raccolti da Log Analytics.

Per consentire a Log Analytics di raccogliere questi log di Diagnostica di Azure, è necessario che i log si trovino nelle posizioni seguenti:

| Tipo di log | Tipo di risorsa | Località |
| --- | --- | --- |
| Log di IIS |Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |wad-iis-logfiles (archivio BLOB) |
| syslog |Macchine virtuali |LinuxsyslogVer2v0 (archivio tabelle) |
| Eventi operativi di Service Fabric |Nodi di Service Fabric |WADServiceFabricSystemEventTable |
| Eventi di Reliable Actor di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventi di Reliable Service di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableServiceEventTable |
| Registri eventi di Windows |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADWindowsEventLogsTable (archivio tabelle) |
| Log di Windows ETW |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADETWEventTable (archivio tabelle) |

> [!NOTE]
> I log IIS provenienti dai siti Web di Azure non sono attualmente supportati.
> 
> 

Per le macchine virtuali è anche possibile installare [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) nella macchina virtuale per abilitare la raccolta di informazioni aggiuntive. In questo modo sarà possibile analizzare i log IIS e i registri eventi, oltre che eseguire ulteriori analisi, tra cui il rilevamento delle modifiche alla configurazione, la valutazione degli aggiornamenti e la valutazione di SQL.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log eventi e IIS
Usare la procedura seguente per abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log di eventi e IIS tramite il portale di gestione di Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-management-portal"></a>Per abilitare la diagnostica di Azure in una macchina virtuale con il portale di gestione di Azure
1. Installare l'agente di macchine virtuali quando si crea una macchina virtuale. Se la macchina virtuale esiste già, verificare che l'agente di macchine virtuali sia già installato.
   
   * Nel portale di gestione di Azure passare alla macchina virtuale, selezionare **Configurazione facoltativa**, quindi **Diagnostica** e impostare lo **Stato** su **Sì**.
     
     Al termine, nella macchina virtuale risulterà automaticamente installata e in esecuzione l'estensione Diagnostica di Azure che sarà responsabile della raccolta dei dati di diagnostica.
2. Abilitare il monitoraggio e configurare la registrazione degli eventi su una macchina virtuale esistente. La diagnostica può essere abilitata a livello di macchina virtuale. Per abilitare la diagnostica e quindi configurare la registrazione degli eventi, eseguire la procedura seguente:
   
   1. Selezionare la macchina virtuale.
   2. Fare clic su **Monitoraggio**.
   3. Fare clic su **Diagnostica**.
   4. Impostare lo **Stato** su **SÌ**.
   5. Selezionare ogni log di diagnostica da raccogliere.
   6. Fare clic su **OK**.

Con Azure PowerShell è possibile specificare in modo più preciso gli eventi che vengono scritti nell'Archiviazione di Azure. Vedere [Collect data using Azure diagnostics written to table storage or IIS Logs written to blob](log-analytics-azure-storage-json.md) (Raccogliere dati usando i log di diagnostica di Azure scritti nell'archivio tabelle o i log di IIS scritti nell'archivio BLOB).

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Abilitare Diagnostica di Azure in un ruolo Web per la raccolta di eventi e log IIS
Vedere [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Le informazioni di base contenute in tale articolo consentiranno di personalizzare i passaggi da eseguire qui per usare Log Analytics.

Con Diagnostica di Azure abilitata:

* Per impostazione predefinita, i log di IIS vengono archiviati e i dati dei log vengono trasferiti in base all'intervallo di trasferimento scheduledTransferPeriod.
* Per impostazione predefinita, i registri eventi di Windows non vengono trasferiti.

### <a name="to-enable-diagnostics"></a>Per abilitare la diagnostica
Per abilitare i registri eventi di Windows o per modificare scheduledTransferPeriod, configurare Diagnostica di Azure con il file di configurazione XML (diagnostics.wadcfg), come mostrato in [Passaggio 4: Creare il file di configurazione della diagnostica e installare l'estensione](../cloud-services/cloud-services-dotnet-diagnostics.md).

Il file di configurazione di esempio seguente raccoglie i log IIS e tutti gli eventi dai log di applicazione e sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Assicurarsi che ConfigurationSettings specifichi un account di archiviazione, come illustrato nell'esempio seguente:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

I valori **AccountName** e **AccountKey** sono disponibili nel dashboard dell'account di archiviazione del portale di gestione di Azure in Gestisci chiavi di accesso. Il protocollo per la stringa di connessione deve essere **https**.

Dopo che la configurazione della diagnostica aggiornata è stata applicata al servizio cloud e ha iniziato a scrivere la diagnostica in Archiviazione di Azure, è possibile configurare Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usare il portale di Azure per raccogliere log da Archiviazione di Azure
È possibile usare il portale di Azure per configurare Log Analytics per raccogliere i log per i servizi di Azure seguenti:

* Cluster di Service Fabric
* Macchine virtuali
* Ruoli di lavoro/Web

Nel portale di Azure passare all'area di lavoro di Log Analytics ed eseguire queste attività:

1. Fare clic su *Log account di archiviazione*
2. Fare clic sull'attività *Aggiungi*
3. Selezionare l'account di archiviazione che contiene i log di diagnostica
   * È possibile usare un account di archiviazione classico o un account di archiviazione di Azure Resource Manager
4. Selezionare il tipo di dati per cui si vogliono raccogliere i log
   * È possibile scegliere i log di IIS, i log eventi, i log di Syslog (Linux), i log ETW e i log di eventi di Service Fabric
5. Il valore per l'origine verrà popolato automaticamente in base al tipo di dati e non può essere modificato
6. Fare clic su OK per salvare la configurazione

Ripetere i passaggi da 2 a 6 per account di archiviazione aggiuntivi e tipi di dati che devono essere raccolti da Log Analytics.

Entro 30 minuti circa sarà possibile visualizzare i dati dall'account di archiviazione in Log Analytics. Verranno visualizzati solo i dati scritti nell'archivio dopo l'applicazione della configurazione. Log Analytics non legge i dati preesistenti dall'account di archiviazione.

> [!NOTE]
> Il portale non conferma l'esistenza dell'origine nell'account di archiviazione e non verifica se vengono scritti nuovi dati.
> 
> 

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log eventi e log IIS tramite PowerShell
Con Azure PowerShell è possibile specificare in modo più preciso gli eventi che vengono scritti nell'Archiviazione di Azure.
Per altri dettagli, vedere [Abilitare la diagnostica nelle macchine virtuali di Azure](../virtual-machines-dotnet-diagnostics.md) .

È possibile abilitare e aggiornare l'agente con il seguente script di PowerShell.
È possibile usare questo script anche con una configurazione della registrazione personalizzata.
Sarà necessario modificare lo script per impostare l'account di archiviazione, il nome del servizio e il nome della macchina virtuale.
Lo script usa i cmdlet per le macchine virtuali classiche.

Esaminare il seguente script di esempio, copiarlo, modificarlo se necessario, salvare l'esempio come file script di PowerShell e quindi eseguire lo script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```

## <a name="next-steps"></a>Passaggi successivi
* [Usare i file JSON nell'archivio BLOB](log-analytics-azure-storage-json.md) per leggere i log dai servizi di Azure che scrivono dati di diagnostica nell'archivio BLOB in formato JSON.
* [Abilitare soluzioni](log-analytics-add-solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](log-analytics-log-searches.md) per analizzare i dati.

<!--HONumber=Oct16_HO2-->


