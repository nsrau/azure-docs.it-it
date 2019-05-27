---
title: Usare l'archiviazione blob per IIS e tabella di archiviazione per gli eventi in Monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure può leggere i log per i servizi di Azure che scrivono dati di diagnostica nell'archivio tabelle o log IIS scritti nell'archivio blob.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: a4e0432260cfb9ee11ed318305fb967d160de835
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129680"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-azure-monitor"></a>Usare l'archiviazione blob di Azure per archiviazione tabelle di Azure e IIS per gli eventi con monitoraggio di Azure

Monitoraggio di Azure può leggere i log per i servizi seguenti che scrivono dati di diagnostica nell'archivio tabelle o log IIS scritti nell'archivio blob:

* Cluster di Service Fabric (Anteprima)
* Macchine virtuali
* Ruoli di lavoro/Web

Prima di monitoraggio di Azure può raccogliere i dati in un'area di lavoro di Log Analitica per queste risorse, è necessario abilitare diagnostica di Azure.

Dopo l'abilitazione della diagnostica, è possibile usare il portale di Azure o PowerShell per configurare l'area di lavoro per raccogliere i log.

Diagnostica di Azure è un'estensione di Azure che consente di raccogliere i dati di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati vengono archiviati in un account di archiviazione di Azure e possono quindi essere raccolti da monitoraggio di Azure.

Per monitoraggio di Azure per raccogliere questi log di diagnostica di Azure, i log devono trovarsi nei percorsi seguenti:

| Tipo di log | Tipo di risorsa | Località |
| --- | --- | --- |
| Log di IIS |Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |wad-iis-logfiles (archivio BLOB) |
| syslog |Macchine virtuali |LinuxsyslogVer2v0 (archivio tabelle) |
| Eventi operativi di Service Fabric |Nodi di Service Fabric |WADServiceFabricSystemEventTable |
| Eventi di Reliable Actor di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventi di Reliable Service di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableServiceEventTable |
| Log eventi di Windows |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADWindowsEventLogsTable (archivio tabelle) |
| Log di Windows ETW |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADETWEventTable (archivio tabelle) |

> [!NOTE]
> I log IIS provenienti dai siti Web di Azure non sono attualmente supportati.
>
>

Per le macchine virtuali è anche possibile installare l'[agente di Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) nella macchina virtuale per abilitare la raccolta di informazioni aggiuntive. In questo modo è possibile analizzare i log IIS e i registri eventi, oltre che eseguire ulteriori analisi, tra cui il rilevamento delle modifiche alla configurazione, la valutazione degli aggiornamenti e la valutazione di SQL.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log eventi e IIS

Usare la procedura seguente per abilitare la Diagnostica di Azure in una macchina virtuale per la raccolta di log di eventi e IIS tramite il portale di Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Per abilitare la Diagnostica di Azure in una macchina virtuale con il portale di Azure

1. Installare l'agente di macchine virtuali quando si crea una macchina virtuale. Se la macchina virtuale esiste già, verificare che l'agente di macchine virtuali sia già installato.

   * Nel portale di Azure passare alla macchina virtuale, selezionare **Configurazione facoltativa**, quindi **Diagnostica** e impostare lo **Stato** su **Sì**.

     Al termine, nella VM risulta installata e in esecuzione l'estensione di Diagnostica di Azure, che ha il compito di raccogliere i dati di diagnostica.
2. Abilitare il monitoraggio e configurare la registrazione degli eventi su una macchina virtuale esistente. La diagnostica può essere abilitata a livello di macchina virtuale. Per abilitare la diagnostica e quindi configurare la registrazione degli eventi, eseguire la procedura seguente:

   1. Selezionare la macchina virtuale.
   2. Fare clic su **Monitoraggio**.
   3. Fare clic su **Diagnostica**.
   4. Impostare lo **Stato** su **SÌ**.
   5. Selezionare ogni log di diagnostica da raccogliere.
   6. Fare clic su **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Abilitare Diagnostica di Azure in un ruolo Web per la raccolta di eventi e log IIS

Consultare l'argomento su [come abilitare la diagnostica in un servizio cloud](../../cloud-services/cloud-services-dotnet-diagnostics.md) per una procedura generale di abilitazione della Diagnostica di Azure. Le istruzioni seguenti usano queste informazioni, personalizzandole per l'uso con Log Analytics.

Con Diagnostica di Azure abilitata:

* Per impostazione predefinita, i log di IIS vengono archiviati e i dati dei log vengono trasferiti in base all'intervallo di trasferimento scheduledTransferPeriod.
* Per impostazione predefinita, i log eventi di Windows non vengono trasferiti.

### <a name="to-enable-diagnostics"></a>Per abilitare la diagnostica

Per abilitare i log eventi di Windows o per modificare scheduledTransferPeriod, configurare Diagnostica di Azure con il file di configurazione XML (diagnostics.wadcfg), come mostrato in [Passaggio 4: Creare il file di configurazione della diagnostica e installare l'estensione](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Il file di configurazione di esempio seguente raccoglie i log IIS e tutti gli eventi dai log di applicazione e sistema:

```xml
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

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

I valori **AccountName** e **AccountKey** sono disponibili nel dashboard dell'account di archiviazione del portale di Azure in Gestisci chiavi di accesso. Il protocollo per la stringa di connessione deve essere **https**.

Dopo la configurazione della diagnostica aggiornata è stata applicata al servizio cloud e sta scrivendo la diagnostica in archiviazione di Azure, si è pronti per configurare l'area di lavoro di Log Analitica.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usare il portale di Azure per raccogliere log da Archiviazione di Azure

È possibile usare il portale di Azure per configurare un'area di lavoro di Log Analitica in Monitoraggio di Azure per raccogliere i log per i servizi di Azure seguenti:

* Cluster di Service Fabric
* Macchine virtuali
* Ruoli di lavoro/Web

Nel portale di Azure passare all'area di lavoro Log Analytics ed eseguire queste attività:

1. Fare clic su *Log account di archiviazione*
2. Fare clic sull'attività *Aggiungi*
3. Selezionare l'account di archiviazione che contiene i log di diagnostica
   * L'account di archiviazione può essere di tipo classico oppure un account di archiviazione di Azure Resource Manager
4. Selezionare il tipo di dati per cui si vogliono raccogliere i log
   * È possibile scegliere tra log IIS, log eventi, SysLog (Linux), log ETW ed eventi Service Fabric.
5. Il valore per l'origine viene compilato automaticamente in base al tipo di dati e non può essere modificato
6. Fare clic su OK per salvare la configurazione

Ripetere i passaggi da 2 a 6 per account di archiviazione aggiuntivi e i tipi di dati che si desidera raccogliere nell'area di lavoro.

Dopo circa 30 minuti, si è in grado di visualizzare i dati dall'account di archiviazione nell'area di lavoro di Log Analitica. Verranno visualizzati solo i dati scritti nell'archivio dopo l'applicazione della configurazione. L'area di lavoro non legge i dati preesistenti dall'account di archiviazione.

> [!NOTE]
> Il portale non conferma l'esistenza dell'origine nell'account di archiviazione e non verifica se vengono scritti nuovi dati.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log eventi e log IIS tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usare la procedura descritta in [configurazione di monitoraggio di Azure per indicizzare diagnostica di Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) usare PowerShell per leggere da diagnostica di Azure che viene scritti in archiviazione tabelle.

Con Azure PowerShell è possibile specificare in modo più preciso gli eventi che vengono scritti nell'Archiviazione di Azure.
Per altre informazioni, vedere [Abilitare la diagnostica nelle macchine virtuali di Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

È possibile abilitare e aggiornare la Diagnostica di Azure con il seguente script PowerShell.
È possibile usare questo script anche con una configurazione della registrazione personalizzata.
Modificare lo script per impostare l'account di archiviazione, il nome del servizio e il nome della macchina virtuale.
Lo script usa i cmdlet per le macchine virtuali di tipo classico.

Esaminare il seguente script di esempio, copiarlo, modificarlo se necessario, salvare l'esempio come file script di PowerShell e quindi eseguire lo script.

```powershell
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

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Passaggi successivi

* [Raccogliere i log e le metriche per i servizi di Azure](collect-azure-metrics-logs.md) per i servizi supportati di Azure.
* [Abilitare soluzioni](../../azure-monitor/insights/solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](../../azure-monitor/log-query/log-query-overview.md) per analizzare i dati.