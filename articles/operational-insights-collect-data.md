<properties 
   pageTitle="Raccogliere i dati del computer" 
   description="Raccogliere i dati del computer dai server nell'infrastruttura locale o cloud" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
#Raccogliere i dati del computer

Operational Insights usa i dati provenienti dai server nell'infrastruttura locale o cloud. È possibile raccogliere i dati del computer dalle origini seguenti:

- Agenti di Operations Manager
- Computer connessi direttamente a Operational Insights
- Dati di diagnostica delle macchine virtuali nei servizi di Archiviazione di Azure

Dopo la raccolta, i dati vengono inviati al servizio Operational Insights.

## Connettersi a Operational Insights da System Center Operations Manager 

È possibile connettere Operational Insights a un ambiente esistente di System Center Operations Manager. Ciò permetterà di usare gli agenti esistenti di Operations Manager come agenti di Operational Insights. Per altre informazioni sull'uso di Operations Manager con Operational Insights, vedere la pagina relativa alle [considerazioni su Operations Manager con Operational Insights](operational-insights-operations-manager.md).

Se si usa Operations Manager per monitorare uno dei carichi di lavoro seguenti, sarà necessario configurare account RunAs di Operations Manager corrispondenti. Vedere [Account RunAs di Operations Manager per Operational Insights](operational-insights-run-as.md) per altre informazioni sulla configurazione degli account.

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] Il supporto per Operational Insights è disponibile a partire dalle versioni Operations Manager 2012 SP1 UR6 e Operations Manager 2012 R2 UR2. Il supporto per il proxy è stato aggiunto in System Center Operations Manager 2012 SP1 UR7 e System Center Operations Manager 2012 R2 UR3.

#### Per connettere Operations Manager a Operational Insights e aggiungere agenti

1. Nella console di Operations Manager fare clic su **Amministrazione**.

2. Espandere il nodo **Operational Insights**, quindi selezionare **Operational Insights Connection**.

3. Fare clic sul collegamento **Register to Operational Insights** e seguire le istruzioni visualizzate sullo schermo. 

4. Dopo il completamento della registrazione guidata, fare clic su **Aggiungi computer/gruppo**.

5. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi da caricare in Operational Insights, fare clic su **Aggiungi** e quindi su **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Connettere i computer direttamente a Operational Insights 

È possibile connettere i computer direttamente a Operational Insights installando l'agente di Operational Insights in ogni computer da caricare. 


###Scaricare e installare l'agente

####Per scaricare il file di installazione dell'agente
1. Nel **portale di Operations Insights**, nella pagina **Overview** fare clic su **Servers and Usage**.
1. In **Servers connected directly** fare clic su **configure**.
1. Accanto ad **Add Agents**, fare clic sul collegamento Agent per scaricare il file di installazione.
1. Nella casella **Primary Workspace Key** selezionare la chiave e copiarla (CTRL+C).


#### Per installare gli agenti con il programma di installazione
1. Eseguire il programma di installazione per installare l'agente in un computer da gestire.
1. Selezionare **Connect the agent to Microsoft Azure Operational Insights**, quindi fare clic su **Next**.
1. Quando richiesto, immettere le informazioni copiate nel passaggio 4.
1. Al termine, l'**agente di gestione Microsoft** viene visualizzato nel **Pannello di controllo**.

#### Per installare l'agente mediante la riga di comando
Modificare e usare il seguente esempio per installare l'agente mediante la riga di comando.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Configurare Microsoft Monitoring Agent (facoltativo)
Usare le informazioni seguenti per abilitare un agente a comunicare direttamente con il servizio Microsoft Azure Operational Insights. Dopo aver configurato l'agente, questo verrà registrato con il servizio agente e otterrà le informazioni di configurazione necessarie nonché i Management Pack contenenti le informazioni dell'Intelligence Pack.

Dopo la raccolta dei dati dai computer monitorati dall'agente, nella pagina Usage in **Directly Connected Agents** del portale di Operational Insights verrà visualizzato il numero di computer monitorati. Nel portale di Operational Insights è possibile visualizzare le informazioni relative a dati e valutazioni per qualsiasi computer che invia dati.

Se necessario, è anche possibile disabilitare l'agente o abilitarlo, usando la riga di comando o uno script.

#### Per configurare l'agente
- Dopo avere installato **Microsoft Monitoring Agent**, aprire il **Pannello di controllo**.
- Aprire Microsoft Monitoring Agent e fare clic sulla scheda Azure Operational Insights.
- Selezionare **Connect to Azure Operational Insights**.
- Nella casella **Workspace ID** digitare l'ID dell'area di lavoro fornito nel portale di Operational Insights.
- Nella casella Account Key incollare il valore di **Primary Workspace Key** copiati durante l'installazione dell'agente e fare clic su **OK**.

#### Per disabilitare l'agente
- Dopo aver installato l'agente, aprire il **Pannello di controllo**.
- Aprire Microsoft Monitoring Agent e fare clic sulla scheda **Azure Operational Insights**.
- Deselezionare **Connect to Azure Operational Insights**.

#### Per abilitare l'agente usando la riga di comando o uno script
È possibile usare Windows PowerShell or o uno script VB script come quello nell'esempio seguente.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Configurare le impostazioni di proxy e firewall (facoltativo)
Se l'ambiente include server proxy o firewall che limitano l'accesso a Internet, potrebbe essere necessario eseguire le procedure seguenti per abilitare Operations Manager e/o gli agenti alla comunicazione con il servizio Operational Insights 



- [Configurare le impostazioni di proxy e firewall (facoltativo)](operational-insights-proxy-filewall.md) 

## Analizzare i dati dai server in Microsoft Azure

Con Operational Insights è possibile eseguire ricerche rapide in log di eventi e IIS per servizi cloud e macchine virtuali abilitando [Diagnostica nei servizi cloud di Azure](operational-insights-log-collection.md). È inoltre possibile ottenere informazioni aggiuntive dalle macchine virtuali mediante l'installazione di Microsoft Monitoring Agent.

Gli Intelligence Pack di Update Assessment, Change Tracking e SQL Assessment interagiscono tutti con Microsoft Monitoring Agent per fornire informazioni più approfondite sulle macchine virtuali. Se non è già stato fatto, è possibile abilitare questi Intelligence Pack dopo avere effettuato l'accesso al [portale di Operational Insights](https://preview.opinsights.azure.com/).

Per Macchine virtuali di Azure, sono disponibili due semplici modi per abilitare la raccolta di dati basata su agente:

- Nel portale di gestione di Microsoft Azure

- Uso di PowerShell

Quando si usa la raccolta basata su agente per i dati di log, è necessario configurare i log per la raccolta nella pagina di configurazione Log Management nel [portale di Operational Insights](https://preview.opinsights.azure.com/).

Se si è configurato Operational Insights per indicizzare i dati di log mediante Diagnostica nei servizi cloud di Azure e si configura l'agente per la raccolta di log, gli stessi log di eventi avranno due copie dei dati indicizzati. Se è installato l'agente, è necessario raccogliere i dati di log mediante l'agente e non indicizzare i log raccolti da Diagnostica nei servizi cloud di Azure.

### Portale di gestione di Microsoft Azure

Nel [portale di Operational Insights](https://preview.opinsights.azure.com/), passare all'area di lavoro di Operational Insights e selezionare la scheda Servers. Nella scheda verrà visualizzato l'elenco delle macchine virtuali. Selezionare la macchina virtuale in cui si vuole installare l'agente e quindi fare clic sul pulsante **Enable Op Insights**.

L'agente viene installato e configurato per l'area di lavoro di Operational Insights.

![Immagine della pagina dei server di Operational Insights](./media/operational-insights-collect-data/servers.png)

### PowerShell

Se si preferisce usare script per apportare modifiche alle macchine virtuali di Azure, è possibile abilitare Microsoft Monitoring Agent con PowerShell.

Microsoft Monitoring Agent è un'[estensione delle macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) e può gestire questo servizio tramite PowerShell, come illustrato nell'esempio seguente.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

Quando si esegue la configurazione tramite PowerShell, è necessario specificare l'ID e la chiave dell'area di lavoro, riportati nella pagina Servers and Usage del portale di Operational Insights.

![Immagine della configurazione dell'agente diretto di Operational Insights](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Raccogliere dati usando la diagnostica dei Servizi cloud di Azure

Operational Insights consente di analizzate i dati scritti dagli strumenti di diagnostica dei Servizi cloud di Azure nei servizi di archiviazione di Azure. I passaggi da eseguire sono due:

- Configurare la raccolta dei dati di diagnostica nell'archiviazione di Azure
- Configurare Operational Insights per l'analisi dei dati nell'account di archiviazione

Gli argomenti seguenti descrivono come abilitare la raccolta dei dati di diagnostica nell'archiviazione di Azure e come configurare Operational Insights per l'analisi dei dati nell'archiviazione di Azure.

Diagnostica di Azure è un'estensione di Azure che consente di raccogliere i dati di telemetria sulla diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati di telemetria vengono memorizzati in un account di archiviazione di Azure e possono quindi essere usati da Operational Insights.

>[AZURE.NOTE] Per l'invio della diagnostica a un account di archiviazione, verranno addebitate le normali tariffe dati per l'archiviazione e le transazioni.

Diagnostica di Azure può raccogliere i seguenti tipi di telemetria:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Origine dati</b></td>
		<td><b>

Descrizione </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Log di IIS</td>
		<td>Informazioni sui siti Web di IIS.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Log dell'infrastruttura Diagnostica di Azure</td>
		<td>Informazioni su Diagnostica.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Log di richieste non riuscite di IIS </td>
		<td>Informazioni sulle richieste non riuscite a un sito o a un'applicazione di IIS.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Registri eventi di Windows</td>
		<td>Informazioni inviate al sistema di registrazione eventi di Windows.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Contatori delle prestazioni</td>
		<td>Contatori delle prestazioni personalizzati e del sistema operativo.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Dump di arresto anomalo del sistema</td>
		<td>Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Log degli errori personalizzati</td>
		<td>Log creati dall'applicazione o dal servizio.</td>
    </tr>
    <tr align="left" valign="top">
		<td>EventSource .NET</td>
		<td>Eventi generati dal codice con la <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">classe EventSource/a> .NET</td>
    </tr>
    <tr align="left" valign="top">
		<td>ETW basato su manifesto</td>
		<td>Eventi ETW generati da un processo.</td>
    </tr>
    </tbody>
    </table>


Attualmente Operational Insights è in grado di analizzare log IIS raccolti dai ruoli Web e registri eventi di Windows raccolti da ruoli Web, ruoli di lavoro e macchine virtuali di Azure. I log devono trovarsi nei percorsi seguenti:

- WADWindowsEventLogsTable(Archivio tabelle) - Contiene le informazioni dei registri eventi di Windows.

- wad-iis-logfiles (Archivio BLOB) - Contiene informazioni sui log IIS.

Per le macchine virtuali è anche possibile installare [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) nella macchina virtuale. In questo modo sarà possibile analizzare i log IIS e i registri eventi, oltre che eseguire ulteriori analisi, tra cui il rilevamento delle modifiche alla configurazione e la valutazione degli aggiornamenti. Abilitare la raccolta di informazioni aggiuntive installando [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] I log IIS provenienti dai siti Web di Azure non sono attualmente supportati.

È possibile classificare in ordine di priorità i log da aggiungere a quelli supportati per l'analisi in Operational Insights nella [pagina dei commenti e suggerimenti](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

### Abilitare Diagnostica di Azure in un ruolo Web per la raccolta di eventi e log IIS

Fare riferimento a [Come abilitare il modulo Diagnostica in un servizio cloud](https://msdn.microsoft.com/library/azure/dn482131.aspx). Le informazioni di base contenute in tale articolo consentiranno di personalizzare i passaggi da eseguire qui per usare Microsoft Azure Operational Insights.

Con Diagnostica di Azure abilitata:

- Per impostazione predefinita, i log di IIS vengono archiviati e i dati dei log vengono trasferiti in base all'intervallo di trasferimento scheduledTransferPeriod.

- Per impostazione predefinita, i registri eventi di Windows non vengono trasferiti.

#### Per abilitare la diagnostica

Per abilitare i registri eventi di Windows o per modificare scheduledTransferPeriod, configurare Diagnostica di Azure con il file di configurazione XML (diagnostics.wadcfg), come mostrato nel [Passaggio 2: Aggiungere il file diagnostics.wadcfg alla soluzione Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) e [Passaggio 3: Configurare la diagnostica per l'applicazione](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) nell'argomento Come abilitare il modulo Diagnostica in un servizio cloud. Il seguente file di configurazione di esempio raccoglie i log di IIS e tutti gli eventi dai registri applicazioni e di sistema:

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


Nel [Passaggio 4: Configurare l'archiviazione dei dati di diagnostica](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) dell'argomento Come abilitare il modulo Diagnostica in un servizio cloud, assicurarsi che ConfigurationSettings specifichi un account di archiviazione, come nell'esempio seguente:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


I valori **AccountName** e **AccountKey** sono disponibili nel dashboard dell'account di archiviazione del portale di gestione di Microsoft Azure in Gestisci chiavi. Il protocollo per la stringa di connessione deve essere **https**.

Dopo che la configurazione della diagnostica aggiornata è stata applicata al servizio cloud e ha iniziato a scrivere la diagnostica in Archiviazione di Azure, è possibile configurare Operational Insights.

### Abilitare la diagnostica di Servizi cloud di Azure in una macchina virtuale per la raccolta di registri eventi e log IIS

Usare la procedura seguente per abilitare la diagnostica di Servizi cloud di Azure in una macchina virtuale per la raccolta di log eventi e di log di IIS con il portale di gestione di Microsoft Azure.

#### Per abilitare la diagnostica di Servizi cloud di Azure in una macchina virtuale con il portale di gestione di Azure

1. Installare l'agente di macchine virtuali quando si crea una macchina virtuale. Se la macchina virtuale esiste già, verificare che l'agente di macchine virtuali sia già installato.
	- Se si usa il portale di gestione di Azure predefinito per creare la macchina virtuale, eseguire una **Creazione personalizzata** e selezionare **Installa l'agente di macchine virtuali**.
	- Se si usa il nuovo portale di gestione di Azure per creare una macchina virtuale, selezionare **Configurazione facoltativa**, quindi **Diagnostica** e impostare **Stato** su **Attivato**.
	
	Al termine, nella macchina virtuale risulterà automaticamente installata e in esecuzione l'estensione Diagnostica di Azure che sarà responsabile della raccolta dei dati di diagnostica.

2. Abilitare il monitoraggio e configurare la registrazione degli eventi su una macchina virtuale esistente. La diagnostica può essere abilitata a livello di macchina virtuale. Per abilitare la diagnostica e quindi configurare la registrazione degli eventi, eseguire la procedura seguente:
	1. Selezionare la macchina virtuale.
	2. Fare clic su **Monitoraggio**.
	3. Fare clic su **Diagnostica**.
	4. Impostare **Stato** su **ATTIVA**.
	5. Selezionare ogni metrica di diagnostica che si desidera usare. Operational Insights può analizzare log eventi del sistema di Windows, log eventi dell'applicazione di Windows e log di IIS.
	7. Fare clic su **OK**.

Con Azure PowerShell è possibile specificare in modo più preciso gli eventi che vengono scritti nell'Archiviazione di Azure. Fare riferimento allo Schema di configurazione di Diagnostica di Azure 1.2 per un file di configurazione di esempio e la documentazione dettagliata sullo schema. Assicurarsi di installare e configurare Azure PowerShell versione 0.8.7 o successiva da [Come installare e configurare Azure PowerShell](install-configure-powershell/). Se è installata una versione di Diagnostica di Microsoft Azure precedente alla versione 1.2, non è possibile usare il nuovo portale per abilitare o configurare la diagnostica.

È possibile abilitare e aggiornare l'agente con il seguente script di PowerShell. È possibile usare questo script anche con la configurazione della registrazione personalizzata. Sarà necessario modificare lo script per impostare l'account di archiviazione, il nome del servizio e il nome della macchina virtuale.

#### Per abilitare la diagnostica in una macchina virtuale con Azure PowerShell

Esaminare il seguente script di esempio, copiarlo, modificarlo se necessario, salvare l'esempio come file script di PowerShell e quindi eseguire lo script.

    # Connect to Azure
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
    

### Abilitare l'analisi di Archiviazione di Azure con Operational Insights

Nel portale di Azure predefinito passare all'area di lavoro di Operational Insights e selezionare la scheda **Archiviazione**. Per configurare Operational Insights per la lettura dall'account di archiviazione di Azure con Diagnostica di Azure, usare le informazioni seguenti.

#### Per abilitare l'analisi con Operational Insights

1. Fare clic su **Aggiungi** per aprire la casella **Aggiungi account di archiviazione**.

2. Selezionare l'**account di archiviazione**.

3. Selezionare un tipo di dati, **Eventi** o **Log IIS**.

4. Fare clic su **OK**.

5. Ripetere i passaggi precedenti per ogni combinazione di tipo di dati e account di archiviazione che si vuole raccogliere.

Entro 1 ora circa i dati dell'account di archiviazione inizieranno a essere disponibili per l'analisi in Operational Insights.




## Contenuti correlati

- [Post di blog: Connettere i server direttamente a Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Post di blog: Abilitare Operational Insights per Macchine virtuali di Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Passaggi successivi

[Configurare le impostazioni di proxy e firewall (facoltativo)](operational-insights-proxy-filewall.md)




<!--HONumber=52-->