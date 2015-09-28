<properties
   pageTitle="Analizzare i dati dai server in Microsoft Azure"
   description="Abilitazione della diagnostica Azure per cercare log di eventi e IIS per servizi cloud e macchine virtuali."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders"/>
# Analizzare i dati dai server in Microsoft Azure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights usa i dati provenienti dai server nell'infrastruttura locale o cloud. È possibile raccogliere i dati del computer dall'archiviazione di Azure quando vengono generati dalla diagnostica di Azure.

Usando i dati raccolti dall'archiviazione di Azure, è possibile cercare rapidamente in log di eventi e IIS i servizi cloud e le macchine virtuali. È inoltre possibile ottenere informazioni aggiuntive dalle macchine virtuali mediante l'installazione di Microsoft Monitoring Agent.

Le soluzioni di valutazione aggiornamento, rilevamento delle modifiche e valutazione SQL funzionano con Microsoft Monitoring Agent per fornire informazioni approfondite sulle macchine virtuali. Se non è già stato fatto, è possibile [aggiungere soluzioni](operational-insights-setup-workspace.md) dopo avere effettuato l'accesso al [portale di Operational Insights](https://www.microsoft.com/oms/).

Per Macchine virtuali di Azure, sono disponibili due semplici modi per abilitare la raccolta di dati basata su agente:

- Nel portale di gestione di Microsoft Azure

- Uso di PowerShell

Quando si usa la raccolta basata su agente per i dati di log, è necessario configurare i log per la raccolta nella pagina di configurazione Log Management nel [portale di Operational Insights](https://www.microsoft.com/oms/).

 >[AZURE.NOTE]Se si è configurato Operational Insights per indicizzare i dati di log mediante la diagnostica di Azure e si configura l'agente per la raccolta di log, gli stessi log saranno indicizzati due volte. Verranno addebitati velocità dati normali per entrambe le origini dati. Se è installato l'agente, è necessario raccogliere i dati di log mediante l'agente e non indicizzare i log raccolti dalla diagnostica di Azure.

## Portale di gestione di Microsoft Azure

È possibile installare l'agente per Operational Insights dal [portale di Azure](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces).

### Per installare l'agente per Operational Insights
1. Nel portale di Azure andare all'area di lavoro di Operational Insights e selezionare la scheda **Servers**.
2. Nella scheda verrà visualizzato l'elenco delle macchine virtuali. Selezionare la macchina virtuale in cui si vuole installare l'agente e fare clic sul pulsante **Enable Op Insights**.

L'agente viene automaticamente installato e configurato per l'area di lavoro di Operational Insights.

![Immagine della pagina dei server di Operational Insights](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]L'[agente VM di Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) deve essere installato per installare automaticamente Operational Insights. Se si dispone di una macchina virtuale di gestione risorse di Azure non verrà visualizzato nell'elenco ed è necessario utilizzare PowerShell o creare un modello ARM per installare l'agente.



## PowerShell

Se si preferisce usare script per apportare modifiche alle macchine virtuali di Azure, è possibile abilitare Microsoft Monitoring Agent con PowerShell.

Microsoft Monitoring Agent è una [estensione della macchina virtuale di Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) che può essere gestita tramite PowerShell, come illustrato nell'esempio riportato di seguito.

Utilizzare questo PowerShell per macchine virtuali "classiche” di Azure:

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```
Utilizzare questo PowerShell per macchine virtuali di Gestione risorse di Azure:

```powershell
Add-AzureAccount
Switch-AzureMode -Name AzureResourceManager

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"

$resourcegroup = "enter resource group"
$resourcename = "enter resource group"

$vm = Get-AzureVM -ResourceGroupName $resourcegroup -Name $resourcename
$location = $vm.Location

Set-AzureVMExtension -ResourceGroupName $resourcegroup -VMName $resourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"

```

Quando si esegue la configurazione tramite PowerShell, è necessario specificare l'ID e la chiave primaria dell'area di lavoro, che sono riportati nella pagina **Impostazioni** del portale di Operational Insights.

![origini](./media/operational-insights-analyze-data-azure/sources01.png)

## Raccogliere i dati usando la diagnostica di Azure

Operational Insights consente di analizzate i dati scritti dagli strumenti di diagnostica di Azure nella risorsa di archiviazione di Azure. I passaggi da eseguire sono due:

- Configurare la raccolta dei dati di diagnostica nell'archiviazione di Azure
- Configurare Operational Insights per l'analisi dei dati nell'account di archiviazione

Gli argomenti seguenti descrivono come abilitare la raccolta dei dati di diagnostica nell'archiviazione di Azure e come configurare Operational Insights per l'analisi dei dati nell'archiviazione di Azure.

Diagnostica di Azure è un'estensione di Azure che consente di raccogliere i dati di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati vengono memorizzati in un account di archiviazione di Azure e possono quindi essere usati da Operational Insights.

>[AZURE.NOTE]Verranno addebitati la velocità normale per l'archiviazione e le transazioni quando si invia la diagnostica a un account di archiviazione e quando Operational Insights legge i dati dall'account di archiviazione.

Diagnostica Azure può raccogliere i tipi di telemetria seguenti:

Origine dati|Descrizione
 ---|---
Log di IIS|Informazioni sui siti Web di IIS.
Log dell'infrastruttura diagnostica di Azure|Informazioni su Diagnostica.
Log delle richieste non riuscite di IIS |Informazioni sulle richieste non riuscite a un sito o a un'applicazione di IIS.
Registri eventi di Windows|Informazioni inviate al sistema di registrazione eventi di Windows.
Contatori delle prestazioni|Contatori delle prestazioni personalizzati e del sistema operativo.
Dump di arresto anomalo del sistema|Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione.
Log degli errori personalizzati|Log creati dall'applicazione o dal servizio.
EventSource .NET|Eventi generati dal codice con .NET [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx)
ETW basato su manifesto|Eventi ETW generati da qualsiasi processo
Syslog|Eventi inviati ai daemon Syslog o Rsyslog


Attualmente, Operational Insights è in grado di analizzare:

- Log di IIS da macchine virtuali e ruoli Web
- Log di eventi di Windows da ruoli Web, ruoli di lavoro e macchine virtuali di Azure in esecuzione un sistema operativo Windows
- Syslog da macchine virtuali di Azure che eseguono un sistema operativo Linux

I log devono trovarsi nei percorsi seguenti:

- WADWindowsEventLogsTable (Archiviazione tabelle) – Contiene le informazioni dei registri eventi di Windows.
- wad-iis-logfiles (Archivio BLOB) – Contiene informazioni sui log IIS.
- LinuxsyslogVer2v0 (Archiviazione tabelle) – Contiene eventi syslog di Linux.

 >[AZURE.NOTE]I log IIS provenienti dai siti Web di Azure non sono attualmente supportati.

Per le macchine virtuali è anche possibile installare [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) nella macchina virtuale per abilitare la raccolta di informazioni aggiuntive. In questo modo sarà possibile analizzare i log IIS e i registri eventi, oltre che eseguire ulteriori analisi, tra cui il rilevamento delle modifiche alla configurazione, la valutazione degli aggiornamenti e la valutazione di SQL.

È possibile classificare in ordine di priorità i log da aggiungere a quelli supportati per l'analisi in Operational Insights nella pagina dei [commenti e suggerimenti](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

## Abilitare Diagnostica di Azure in un ruolo Web per la raccolta di eventi e log IIS

Vedere [Come abilitare il modulo Diagnostica in un servizio cloud](https://msdn.microsoft.com/library/azure/dn482131.aspx). Le informazioni di base contenute in tale articolo consentiranno di personalizzare i passaggi da eseguire qui per usare Microsoft Azure Operational Insights.

Con Diagnostica di Azure abilitata:

- Per impostazione predefinita, i log di IIS vengono archiviati e i dati dei log vengono trasferiti in base all'intervallo di trasferimento scheduledTransferPeriod.

- Per impostazione predefinita, i registri eventi di Windows non vengono trasferiti.

### Per abilitare la diagnostica

Per abilitare i registri eventi di Windows o per modificare scheduledTransferPeriod, configurare la diagnostica di Azure usando il file di configurazione XML (diagnostics.wadcfg), come illustrato in [Passaggio 2: aggiungere il file diagnostics.wadcfg alla soluzione Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) e [Passaggio 3: Configurare la diagnostica per l'applicazione](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) nell'argomento Come abilitare la diagnostica in un servizio cloud. Il file di configurazione di esempio seguente raccoglie i log IIS e tutti gli eventi dai log di applicazione e sistema:

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


In [Passaggio 4: Configurare l'archiviazione dei dati di diagnostica](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) dell'argomento Come abilitare la diagnostica in un servizio cloud, assicurarsi che ConfigurationSettings specifichi un account di archiviazione, come nell'esempio seguente:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


I valori **AccountName** e **AccountKey** sono disponibili nel dashboard dell'account di archiviazione del portale di gestione di Microsoft Azure in Gestisci chiavi di accesso. Il protocollo per la stringa di connessione deve essere **https**.

Dopo che la configurazione della diagnostica aggiornata è stata applicata al servizio cloud e ha iniziato a scrivere la diagnostica in Archiviazione di Azure, è possibile configurare Operational Insights.

## Abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log eventi e IIS

Usare la procedura seguente per abilitare la diagnostica di Azure in una macchina virtuale per la raccolta di log di eventi e IIS tramite il portale di gestione di Microsoft Azure.

### Per abilitare la diagnostica di Azure in una macchina virtuale con il portale di gestione di Azure

1. Installare l'agente di macchine virtuali quando si crea una macchina virtuale. Se la macchina virtuale esiste già, verificare che l'agente di macchine virtuali sia già installato.
	- Se si usa il portale di gestione di Azure predefinito per creare la macchina virtuale, eseguire una **Creazione personalizzata** e selezionare **Installa l'agente VM**.
	- Se si usa il nuovo portale di gestione di Azure per creare una macchina virtuale, selezionare **Configurazione facoltativa**, quindi **Diagnostica** e impostare **Stato** su **Attivato**.

	Al termine, nella macchina virtuale risulterà automaticamente installata e in esecuzione l'estensione Diagnostica di Azure che sarà responsabile della raccolta dei dati di diagnostica.

2. Abilitare il monitoraggio e configurare la registrazione degli eventi su una macchina virtuale esistente. La diagnostica può essere abilitata a livello di macchina virtuale. Per abilitare la diagnostica e quindi configurare la registrazione degli eventi, eseguire la procedura seguente:
	1. Selezionare la macchina virtuale.
	2. Fare clic su **Monitoraggio**.
	3. Fare clic su **Diagnostica**.
	4. Impostare **Stato** su **ATTIVO**.
	5. Selezionare ogni metrica di diagnostica che si desidera usare. Operational Insights può analizzare log eventi del sistema di Windows, log eventi dell'applicazione di Windows e log di IIS.
	7. Fare clic su **OK**.

Con Azure PowerShell è possibile specificare in modo più preciso gli eventi che vengono scritti nell'Archiviazione di Azure. Fare riferimento allo Schema di configurazione di Diagnostica di Azure 1.2 per un file di configurazione di esempio e la documentazione dettagliata sullo schema. Assicurarsi di installare e configurare Azure PowerShell 0.8.7 o versione successiva da [Come installare e configurare Azure PowerShell](powershell-install-configure). Se è installata una versione di Diagnostica di Microsoft Azure precedente alla versione 1.2, non è possibile usare il nuovo portale per abilitare o configurare la diagnostica.

È possibile abilitare e aggiornare l'agente con il seguente script di PowerShell. È possibile usare questo script anche con la configurazione della registrazione personalizzata. Sarà necessario modificare lo script per impostare l'account di archiviazione, il nome del servizio e il nome della macchina virtuale.

### Per abilitare la diagnostica in una macchina virtuale con Azure PowerShell

Esaminare il seguente script di esempio, copiarlo, modificarlo se necessario, salvare l'esempio come file script di PowerShell e quindi eseguire lo script.


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


## Abilitare l'analisi di Archiviazione di Azure con Operational Insights

Per configurare Operational Insights per la lettura dall'account di archiviazione di Azure con Diagnostica di Azure, usare la procedura seguente.

### Per abilitare l'analisi con Operational Insights

1. Nel portale di Azure predefinito, passare all'area di lavoro di Operational Insights e selezionare la scheda **Archiviazione**. ![scheda archiviazione dell'area di lavoro](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. Fare clic su **Aggiungi un account di archiviazione** per aprire la casella **Aggiungi un account di archiviazione**.
3. Selezionare l'account di archiviazione che si desidera usare.
4. Nell'elenco **Tipo di dati** selezionare un tipo di dati: **Eventi**, **Log IIS** o **Syslog (Linux)**.
5. Fare clic sull'immagine **OK**.<br> ![casella account di archiviazione](./media/operational-insights-analyze-data-azure/storage-account.png)
6. Ripetere i passaggi precedenti per ogni combinazione di tipo di dati e account di archiviazione che si vuole raccogliere.

Entro un'ora circa i dati dell'account di archiviazione inizieranno a essere disponibili per l'analisi in Operational Insights.

## Contenuti correlati

- [Connettere i computer direttamente a Operational Insights](operational-insights-direct-agent)
- [Post di log: Abilitare Operational Insights per Macchine virtuali di Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Passaggi successivi

[Configurare le impostazioni di proxy e firewall (facoltativo)](../operational-insights-proxy-filewall.md)

<!---HONumber=Sept15_HO3-->