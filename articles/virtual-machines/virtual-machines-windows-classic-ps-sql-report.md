<properties 
	pageTitle="Usare PowerShell per creare una macchina virtuale con un server di report in modalità nativa | Microsoft Azure"
	description="Questo argomento descrive e illustra la distribuzione e la configurazione di un server di report di SQL Server Reporting Services in modalità nativa in una macchina virtuale di Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/11/2015"
	ms.author="jroth" />

# Usare PowerShell per creare una macchina virtuale di Azure con un server di report in modalità nativa

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.
 

Questo argomento descrive e illustra la distribuzione e la configurazione di un server di report di SQL Server Reporting Services in modalità nativa in una macchina virtuale di Azure. I passaggi descritti in questo documento usano una combinazione di operazioni manuali per creare la macchina virtuale e uno script di Windows PowerShell per configurare Reporting Services nella macchina virtuale. Lo script di configurazione include l'apertura di una porta del firewall per HTTP o HTTPS.

>[AZURE.NOTE] Se non è necessario **HTTPS** nel server di report, **saltare il passaggio 2**.
>
>Dopo aver creato la macchina virtuale con il passaggio 1, vedere la sezione Usare lo script per configurare il server di report e HTTP. Dopo aver eseguito lo script, il server di report è pronto all'uso.

## Prerequisiti e presupposti

- **Sottoscrizione di Azure**: verificare il numero di core disponibili nella sottoscrizione di Azure. Se si crea la dimensione di macchina virtuale consigliata **A3**, sono necessari **4** core disponibili. Se si usa la dimensione di macchina virtuale **A2**, sono necessari **2** core disponibili.
	
	- Per verificare il limite di core della sottoscrizione, nel portale di Azure classico fare clic su IMPOSTAZIONI nel riquadro a sinistra e quindi su UTILIZZO nel menu in alto.
	
	- Per aumentare la quota di core, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/). Per informazioni sulle dimensioni delle macchine virtuali, vedere [Dimensioni delle macchine virtuali per Azure](virtual-machines-linux-sizes.md).

- **Scripting di Windows PowerShell**: l'argomento presuppone una conoscenza di base di Windows PowerShell. Per altre informazioni sull'uso di Windows PowerShell, vedere gli argomenti seguenti:

	- [Avvio di Windows PowerShell in Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
	
	- [Introduzione a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## Passaggio 1: Eseguire il provisioning di una macchina virtuale di Azure

1. Passare al portale di Azure classico.

1. Fare clic su **Macchine virtuali** nel riquadro sinistro.

	![macchine virtuali di microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Fare clic su **Nuovo**.

	![pulsante nuovo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Fare clic su **Da raccolta**.

	![nuova macchina virtuale dalla raccolta](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Scegliere **SQL Server 2014 RTM Standard - Windows Server 2012 R2** e fare clic sulla freccia per continuare.

	![avanti](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

	Se è necessaria la funzionalità per le sottoscrizioni basate sui dati di Reporting Services, scegliere **SQL Server 2014 RTM Enterprise - Windows Server 2012 R2**. Per altre informazioni sulle edizioni di SQL Server e sul supporto delle funzionalità, vedere [Funzionalità supportate dalle edizioni di SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Nella pagina **Configurazione macchina virtuale** modificare i campi seguenti:
									
	- Se è presente più di una **DATA DI RILASCIO VERSIONE**, selezionare la versione più recente.
	
	- **Nome macchina virtuale**: il nome della macchina virtuale viene usato anche nella pagina di configurazione successiva come nome DNS del servizio cloud predefinito. Il nome DNS deve essere univoco nel servizio Azure. È consigliabile configurare la macchina virtuale con un nome di computer che descriva ciò per cui viene usata la macchina virtuale. Ad esempio, ssrsnativecloud.
	
	- **Piano**: Standard.
	
	- **Dimensioni: A3** è la dimensione di macchina virtuale consigliata per i carichi di lavoro di SQL Server. Se una macchina virtuale viene usata solo come server di report, è sufficiente la dimensione di macchina virtuale A2, a meno che il server di report non debba supportare un elevato carico di lavoro. Per informazioni sui prezzi delle macchine virtuali, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/).
	
	- **Nuovo nome utente**: il nome specificato viene creato come amministratore nella macchina virtuale.
	
	- **Nuova password** e **Conferma**. Questa password viene usata per il nuovo account di amministratore ed è consigliabile usare una password complessa.
	
	- Fare clic su **Avanti**. ![avanti](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Nella pagina successiva, modificare i campi seguenti:

	- **Servizio cloud**: selezionare **Crea un nuovo servizio cloud**.
	
	- **Nome DNS del servizio cloud**: è il nome DNS pubblico del servizio cloud associato alla macchina virtuale. Il nome predefinito è il nome immesso per la macchina virtuale. Se nei passaggi successivi dell'argomento si crea un certificato SSL attendibile, il nome DNS viene usato come valore di "**Rilasciato a**" per il certificato.
	
	- **Area/Gruppo di affinità/Rete virtuale**: scegliere l'area più vicina agli utenti finali.
	
	- **Account di archiviazione**: usare un account di archiviazione generato automaticamente.
	
	- **Set di disponibilità**: nessuno.
	
	- **ENDPOINT**: mantenere gli endpoint **Desktop remoto** e **PowerShell** e quindi aggiungere un endpoint HTTP o HTTPS, a seconda dell'ambiente.

		- **HTTP**: le porte pubbliche e private predefinite sono **80**. Si noti che se si usa una porta privata diversa dalla porta 80, sarà necessario modificare il parametro **$HTTPport = 80** nello script HTTP.

		- **HTTPS**: le porte pubbliche e private predefinite hanno il numero **443**. Come procedura consigliata, modificare la porta privata e configurare il firewall e il server di report per usare la porta privata. Per altre informazioni sugli endpoint, vedere [Come configurare le comunicazioni con una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md). Si noti che se si usa una porta diversa dalla 443, è necessario modificare il parametro **$HTTPsport = 443** nello script HTTPS.
	
	- Fare clic su Avanti. ![avanti](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Nell'ultima pagina della procedura guidata lasciare selezionata l'impostazione predefinita **Installa l'agente di macchine virtuali**. I passaggi descritti in questo argomento non usano l'agente di macchine virtuali, ma se si prevede di usare questa macchina virtuale, le estensioni e l'agente di macchine virtuali consentono di migliorare la gestione delle comunicazioni. Per altre informazioni sull'agente di macchine virtuali, vedere l'articolo relativo ad [agente di macchine virtuali ed estensioni - Parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Una delle estensioni predefinite installate e in esecuzione è l'estensione "BGINFO" che visualizza sul desktop della macchina virtuale le informazioni di sistema, ad esempio l'IP interno e lo spazio libero su disco.

1. Fare clic su Completa. ![ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. Lo **Stato** della macchina virtuale viene visualizzato come **Avvio (provisioning)** durante il processo di provisioning e come **In esecuzione** quando la macchina virtuale è disponibile e pronta all'uso.

## Passaggio 2: Creare un certificato del server

>[AZURE.NOTE] Se HTTPS non è necessario nel server di report, è possibile **ignorare il passaggio 2** e passare alla sezione **Usare lo script per configurare il server di report e HTTP**. Usare lo script HTTP per configurare rapidamente il server di report e renderlo pronto all'uso.

Per usare HTTPS nella macchina virtuale, è necessario un certificato SSL attendibile. A seconda dello scenario, è possibile usare uno dei due metodi seguenti:

- Un certificato SSL valido emesso da un'autorità di certificazione (CA) e ritenuto attendibile da Microsoft. I certificati dell'autorità di certificazione radice devono essere distribuiti tramite il programma Microsoft Root Certificate. Per altre informazioni su questo programma, vedere l'articolo relativo al [programma SSL Root Certificate per Windows e Windows Phone 8 (CA membro)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e l'articolo di [introduzione al programma Microsoft Root Certificate](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Un certificato autofirmato. I certificati autofirmati non sono consigliati per gli ambienti di produzione.

### Per usare un certificato creato da un'autorità di certificazione (CA) attendibile

1. **Richiedere un certificato del server per il sito Web di un'autorità di certificazione**. 

	È possibile usare la Gestione guidata certificati server Web per generare un file di richiesta certificato (Certreq.txt) che si invia a un'autorità di certificazione o per generare una richiesta per un'autorità di certificazione online. Ad esempio, Servizi certificati Microsoft in Windows Server 2012. A seconda del livello di garanzia di identificazione offerto dal certificato del server, possono trascorrere da alcuni giorni a diversi mesi perché l'autorità di certificazione approvi la richiesta e invii un file di certificato.

	Per altre informazioni su come richiedere i certificati del server, vedere gli argomenti seguenti:
	
	- Usare [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
	
	- Strumenti di sicurezza per l'amministrazione di Windows Server 2012.

	[Strumenti di sicurezza per l'amministrazione di Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

	>[AZURE.NOTE] Il campo **Rilasciato a** del certificato SSL attendibile deve essere uguale al **nome DNS del servizio cloud** usato per la nuova macchina virtuale.

1. **Installare il certificato nel server Web**. In questo caso, il server Web è la macchina virtuale che ospita il server di report. Il sito Web verrà creato nei passaggi successivi durante la configurazione di Reporting Services. Per altre informazioni sull'installazione del certificato nel server Web tramite lo snap-in MMC Certificati, vedere l'articolo relativo all'[installazione di un certificato del server](https://technet.microsoft.com/library/cc740068).
	
	Se si desidera usare lo script incluso in questo argomento per configurare il server di report, viene richiesto il valore **Identificazione personale** dei certificati come parametro dello script. Vedere la sezione successiva per informazioni dettagliate su come ottenere l'identificazione personale del certificato.

1. Assegnare il certificato al server di report. L'assegnazione viene completata nella sezione successiva, quando si configura il server di report.

### Per usare il certificato autofirmato delle macchine virtuali

Un certificato autofirmato viene creato nella macchina virtuale quando viene eseguito il provisioning della macchina virtuale. Il nome del certificato è uguale al nome DNS della macchina virtuale. Per evitare errori relativi al certificato, è necessario che il certificato sia attendibile per la macchina virtuale e tutti gli utenti del sito.

1. Per rendere attendibile l'autorità di certificazione radice del certificato nella macchina virtuale locale, aggiungere il certificato alle **Autorità di certificazione radice attendibili**. Di seguito è riportato un riepilogo dei passaggi necessari. Per informazioni dettagliate su come rendere attendibile l'autorità di certificazione, vedere l'articolo relativo all'[installazione di un certificato del server](https://technet.microsoft.com/library/cc740068).

	1. Nel portale di Azure classico selezionare la macchina virtuale e fare clic su Connetti. A seconda della configurazione del browser, potrebbe essere richiesto di salvare un file con estensione rdp per la connessione alla macchina virtuale.
	
		![connettersi alla macchina virtuale di azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Usare il nome della macchina virtuale, il nome utente e la password configurati durante la creazione della macchina virtuale.
	
		Nella figura seguente, ad esempio, il nome della macchina virtuale è **ssrsnativecloud** e il nome utente è **testuser**.
		
		![account di accesso con il nome della macchina virtuale](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
	
	1. Eseguire mmc.exe. Per altre informazioni, vedere [Procedura: Visualizzare certificati con lo snap-in MMC](https://msdn.microsoft.com/library/ms788967.aspx).
	
	1. Nel menu **File** dell'applicazione console aggiungere lo snap-in **Certificati**, selezionare **Account computer** quando richiesto e quindi fare clic su **Avanti**.
	
	1. Selezionare il **Computer locale** da gestire e quindi fare clic su **Fine**.
	
	1. Fare clic su **OK**, espandere i nodi **Certificati - Personale** e quindi fare clic su **Certificati**. Il nome del certificato è costituito dal nome DNS della macchina virtuale e termina con **cloudapp.net**. Fare clic con il pulsante destro del mouse sul nome del certificato e scegliere **Copia**.
	
	1. Espandere il nodo **Autorità di certificazione radice attendibili**, fare clic con il pulsante destro del mouse su **Certificati** e quindi scegliere **Incolla**.
	
	1. Per convalidare, fare doppio clic sul nome del certificato in **Autorità di certificazione radice attendibili** e, se non si sono verificati errori, il certificato è visibile. Se si vuole usare lo script HTTPS incluso in questo argomento per configurare il server di report, è necessario specificare il valore di **Identificazione personale** dei certificati come parametro dello script. **Per ottenere il valore di identificazione personale**, seguire questa procedura. È disponibile anche un esempio di PowerShell per recuperare l'identificazione personale nella sezione [Usare lo script per configurare il server di report e HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
		
		1. Fare doppio clic sul nome del certificato, ad esempio ssrsnativecloud.cloudapp.net.
		
		1. Fare clic sulla scheda **Dettagli**.
		
		1. Fare clic su **Identificazione personale**. Il valore dell'identificazione personale viene visualizzato nel campo dei dettagli, ad esempio ‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
		
		1. Copiare l'identificazione personale e salvare il valore per usarlo successivamente o modificare lo script.
		
		1. (*) Prima di eseguire lo script, rimuovere gli spazi tra le coppie di valori. Ad esempio, l'identificazione personale citata in precedenza ora sarebbe ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
		
		1. Assegnare il certificato al server di report. L'assegnazione viene completata nella sezione successiva, quando si configura il server di report.

Se si usa un certificato SSL autofirmato, il nome del certificato corrisponde già al nome host della macchina virtuale. Pertanto, il DNS del computer è già registrato a livello globale ed è accessibile da qualsiasi client.

## Passaggio 3: Configurare il server di report

Questa sezione illustra la configurazione della macchina virtuale come server di report di Reporting Services in modalità nativa. Per configurare il server di report, è possibile usare uno dei metodi seguenti:

- Usare lo script per configurare il server di report.

- Usare Gestione configurazione per configurare il server di report.

Per istruzioni più dettagliate, vedere la sezione [Connettersi alla macchina virtuale e avviare Gestione configurazione di Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Nota sull'autenticazione:** l'autenticazione di Windows è il metodo di autenticazione consigliato ed è l'autenticazione predefinita di Reporting Services. Solo gli utenti configurati nella macchina virtuale possono accedere a Reporting Services ed essere assegnati ai ruoli di Reporting Services.

### Usare lo script per configurare il server di report e HTTP

Per usare lo script di Windows PowerShell per configurare il server di report, completare i passaggi seguenti. La configurazione include HTTP e non HTTPS.

1. Nel portale di Azure classico selezionare la macchina virtuale e fare clic su Connetti. A seconda della configurazione del browser, potrebbe essere richiesto di salvare un file con estensione rdp per la connessione alla macchina virtuale.

	![connettersi alla macchina virtuale di azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Usare il nome della macchina virtuale, il nome utente e la password configurati durante la creazione della macchina virtuale.

	Nella figura seguente, ad esempio, il nome della macchina virtuale è **ssrsnativecloud** e il nome utente è **testuser**.
	
	![account di accesso con il nome della macchina virtuale](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Nella macchina virtuale aprire **Windows PowerShell ISE** con privilegi amministrativi. PowerShell ISE è installato per impostazione predefinita in Windows Server 2012. È consigliabile usare ISE anziché una finestra standard di Windows PowerShell in modo da poter incollare lo script in ISE, modificare e quindi eseguire lo script.

1. In Windows PowerShell ISE scegliere **Mostra riquadro di script** dal menu **Visualizza**.

1. Copiare lo script seguente e incollarlo nel riquadro di script di Windows PowerShell ISE.

		## This script configures a Native mode report server without HTTPS
		$ErrorActionPreference = "Stop"
		
		$server = $env:COMPUTERNAME
		$HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
		
		## Set PowerShell execution policy to be able to run scripts
		Set-ExecutionPolicy RemoteSigned -Force
		
		## Utility method for verifying an operation's result
		function CheckResult
		{
		    param($wmi_result, $actionname)
		    if ($wmi_result.HRESULT -ne 0) {
		        write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
		    }
		}
		
		$starttime=Get-Date
		write-host -foregroundcolor DarkGray $starttime StartTime
		
		## ReportServer Database name - this can be changed if needed
		$dbName='ReportServer'
		
		## Register for MSReportServer_ConfigurationSetting
		## Change the version portion of the path to "v11" to use the script for SQL Server 2012
		$RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
		
		## Report Server Configuration Steps
		
		## Setting the web service URL ##
		write-host -foregroundcolor green "Setting the web service URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for ReportServer site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
		    CheckResult $r "SetVirtualDirectory for ReportServer"
		
		## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
		    write-host "Calling ReserveURL port $HTTPport"
		    $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
		    CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
		   
		## Setting the Database ##
		write-host -foregroundcolor green "Setting the Database"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## GenerateDatabaseScript - for creating the database
		    write-host "Calling GenerateDatabaseCreationScript for database $dbName"
		    $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
		    CheckResult $r "GenerateDatabaseCreationScript"
		    $script = $r.Script
		
		## Execute sql script to create the database
		    write-host 'Executing Database Creation Script'
		    $savedcvd = Get-Location
		    Import-Module SQLPS              ## this automatically changes to sqlserver provider
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		  
		## GenerateGrantRightsScript 
		    $DBUser = "NT Service\ReportServer"
		    write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
		    $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
		    CheckResult $r "GenerateDatabaseRightsScript"
		    $script = $r.Script
		
		## Execute grant rights script
		    write-host 'Executing Database Rights Script'
		    $savedcvd = Get-Location
		    cd sqlserver:\
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		
		## SetDBConnection - uses Windows Service (type 2), username is ignored
		    write-host "Calling SetDatabaseConnection server $server, DB $dbName"
		    $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
		    CheckResult $r "SetDatabaseConnection"  
		
		## Setting the Report Manager URL ##
		
		write-host -foregroundcolor green "Setting the Report Manager URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for Reports (Report Manager) site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
		    CheckResult $r "SetVirtualDirectory"
		
		## ReserveURL for ReportManager  - port $HTTPport
		    write-host "Calling ReserveURL for ReportManager, port $HTTPport"
		    $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
		    CheckResult $r "ReserveURL for ReportManager port $HTTPport"
		
		write-host -foregroundcolor green "Open Firewall port for $HTTPport"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## Open Firewall port for $HTTPport
		    New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
		    write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
		
		write-host 'Operations completed, Report Server is ready'
		write-host -foregroundcolor DarkGray $starttime StartTime
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time

1. Se la macchina virtuale è stata creata con una porta HTTP diversa da 80, modificare il parametro $HTTPport = 80.

1. Lo script è attualmente configurato per Reporting Services. Se si desidera eseguire lo script per Reporting Services, modificare la parte della versione del percorso dello spazio dei nomi su "v11", nell'istruzione Get-WmiObject.

1. Eseguire lo script.

**Convalida**: per verificare la funzionalità di base del server di report, vedere [Verificare la configurazione](#verify-the-configuration) più avanti in questo argomento.

### Usare lo script per configurare il server di report e HTTPS

Per usare Windows PowerShell per configurare il server di report, completare i passaggi seguenti. La configurazione include HTTPS e non HTTP.

1. Nel portale di Azure classico selezionare la macchina virtuale e fare clic su Connetti. A seconda della configurazione del browser, potrebbe essere richiesto di salvare un file con estensione rdp per la connessione alla macchina virtuale.

	![connettersi alla macchina virtuale di azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Usare il nome della macchina virtuale, il nome utente e la password configurati durante la creazione della macchina virtuale.

	Nella figura seguente, ad esempio, il nome della macchina virtuale è **ssrsnativecloud** e il nome utente è **testuser**.

	![account di accesso con il nome della macchina virtuale](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Nella macchina virtuale aprire **Windows PowerShell ISE** con privilegi amministrativi. PowerShell ISE è installato per impostazione predefinita in Windows Server 2012. È consigliabile usare ISE anziché una finestra standard di Windows PowerShell in modo da poter incollare lo script in ISE, modificare e quindi eseguire lo script.

1. Per abilitare l'esecuzione di script, eseguire il comando di Windows PowerShell seguente:

		Set-ExecutionPolicy RemoteSigned

	Quindi per verificare i criteri, seguire questa procedura:

		Get-ExecutionPolicy

1. In **Windows PowerShell ISE** scegliere **Mostra riquadro di script** dal menu **Visualizza**.

1. Copiare lo script seguente e incollarlo nel riquadro di script Windows PowerShell ISE.

		## This script configures the report server, including HTTPS
		$ErrorActionPreference = "Stop"
		$httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
		
		# You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
		#dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
		#
		# The certifacte hash is a REQUIRED parameter
		$certificatehash="" 
		# the certificate hash should not contain spaces
		
		if ($certificatehash.Length -lt 1) 
		{
		    write-error "certificatehash is a required parameter"
		} 
		# Certificates should be all lower case
		$certificatehash=$certificatehash.ToLower()
		$server = $env:COMPUTERNAME
		# If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
		$DNSName="+"
		#$DNSName="$server.cloudapp.net"
		$DNSNameAndPort = $DNSName + ":$httpsport"
		
		## Utility method for verifying an operation's result
		function CheckResult
		{
		    param($wmi_result, $actionname)
		    if ($wmi_result.HRESULT -ne 0) {
		        write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
		    }
		}
		
		$starttime=Get-Date
		write-host -foregroundcolor DarkGray $starttime StartTime
		
		## ReportServer Database name - this can be changed if needed
		$dbName='ReportServer'
		
		write-host "The script will use $DNSNameAndPort as the DNS name and port" 
		
		## Register for MSReportServer_ConfigurationSetting
		## Change the version portion of the path to "v11" to use the script for SQL Server 2012
		$RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
		
		## Reporting Services Report Server Configuration Steps
		
		## 1. Setting the web service URL ##
		write-host -foregroundcolor green "Setting the web service URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for ReportServer site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
		    CheckResult $r "SetVirtualDirectory for ReportServer"
		
		## ReserveURL for ReportServerWebService - port 80 (for local usage)
		    write-host 'Calling ReserveURL port 80'
		    $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
		    CheckResult $r "ReserveURL for ReportServer port 80" 
		
		## ReserveURL for ReportServerWebService - port $httpsport
		    write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
		    $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
		    CheckResult $r "ReserveURL for ReportServer port $httpsport" 
		
		## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
		    write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
		    $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
		    CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
		    
		## 2. Setting the Database ##
		write-host -foregroundcolor green "Setting the Database"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## GenerateDatabaseScript - for creating the database
		    write-host "Calling GenerateDatabaseCreationScript for database $dbName"
		    $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
		    CheckResult $r "GenerateDatabaseCreationScript"
		    $script = $r.Script
		
		## Execute sql script to create the database
		    write-host 'Executing Database Creation Script'
		    $savedcvd = Get-Location
		    Import-Module SQLPS                    ## this automatically changes to sqlserver provider
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		  
		## GenerateGrantRightsScript 
		    $DBUser = "NT Service\ReportServer"
		    write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
		    $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
		    CheckResult $r "GenerateDatabaseRightsScript"
		    $script = $r.Script
		
		## Execute grant rights script
		    write-host 'Executing Database Rights Script'
		    $savedcvd = Get-Location
		    cd sqlserver:\
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		
		## SetDBConnection - uses Windows Service (type 2), username is ignored
		    write-host "Calling SetDatabaseConnection server $server, DB $dbName"
		    $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
		    CheckResult $r "SetDatabaseConnection"  
		
		## 3. Setting the Report Manager URL ##
		
		write-host -foregroundcolor green "Setting the Report Manager URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for Reports (Report Manager) site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
		    CheckResult $r "SetVirtualDirectory"
		
		## ReserveURL for ReportManager  - port 80
		    write-host 'Calling ReserveURL for ReportManager, port 80'
		    $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
		    CheckResult $r "ReserveURL for ReportManager port 80"
		
		## ReserveURL for ReportManager - port $httpsport
		    write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
		    $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
		    CheckResult $r "ReserveURL for ReportManager port $httpsport" 
		
		## CreateSSLCertificateBinding for ReportManager port $httpsport
		    write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
		    $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
		    CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
		
		write-host -foregroundcolor green "Open Firewall port for $httpsport"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## Open Firewall port for $httpsport
		    New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
		    write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
		
		write-host 'Operations completed, Report Server is ready'
		write-host -foregroundcolor DarkGray $starttime StartTime
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time

1. Modificare il parametro **$certificatehash** nello script:

	- Si tratta di un parametro **obbligatorio**. Se il valore del certificato non è stato salvato nei passaggi precedenti, usare uno dei metodi seguenti per copiare il valore hash del certificato dall'identificazione personale del certificato.

		Nella macchina virtuale, aprire Windows PowerShell ISE ed eseguire il comando seguente:

			dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

		L'output sarà simile al seguente: Se lo script restituisce una riga vuota, la macchina virtuale non dispone, ad esempio, di un certificato configurato. Per informazioni, vedere la sezione [Per usare il certificato autofirmato delle macchine virtuali](#to-use-the-virtual-machines-self-signed-certificate).
	
	OPPURE
	
	- Eseguire mmc.exe nella macchina virtuale e quindi aggiungere lo snap-in **Certificati**.
	
	- Nel nodo **Autorità di certificazione radice attendibili** fare doppio clic sul nome del certificato. Se si usa il certificato autofirmato della macchina virtuale, il nome del certificato è costituito dal nome DNS della macchina virtuale e termina con **cloudapp.net**.
	
	- Fare clic sulla scheda **Dettagli**.
	
	- Fare clic su **Identificazione personale**. Il valore dell'identificazione personale viene visualizzato nel campo dei dettagli, ad esempio af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
	
	- **Prima di eseguire lo script**, rimuovere gli spazi tra le coppie di valori. Ad esempio, af1160b64b288d890a8212ff6ba9c3664f319048

1. Modificare il parametro **$httpsport**:

	- Se si usa la porta 443 per l'endpoint HTTPS, non è necessario aggiornare questo parametro nello script. In caso contrario, usare il valore della porta selezionato durante la configurazione dell'endpoint privato HTTPS nella macchina virtuale.

1. Modificare il parametro **$DNSName**:

	- Lo script è configurato per un certificato con caratteri jolly $DNSName="+". Se non si desidera configurare un'associazione del certificato con caratteri jolly, rimuovere il commento $DNSName="+" e abilitare la riga seguente, il riferimento $DNSNAme completo, ##$DNSName="$server.cloudapp.net".

		Modificare il valore $DNSName se non si desidera usare il nome DNS della macchina virtuale per Reporting Services. Se si usa il parametro, anche il certificato deve usare questo nome ed è necessario registrare il nome a livello globale in un server DNS.

1. Lo script è attualmente configurato per Reporting Services. Se si desidera eseguire lo script per Reporting Services, modificare la parte della versione del percorso dello spazio dei nomi su "v11", nell'istruzione Get-WmiObject.

1. Eseguire lo script.

**Convalida**: per verificare la corretta esecuzione della funzionalità di base del server di report, vedere [Verificare la configurazione](#verify-the-connection) più avanti in questo argomento. Per verificare l'associazione del certificato, aprire un prompt dei comandi con privilegi amministrativi e quindi eseguire il comando seguente:

	netsh http show sslcert

Il risultato include quanto segue:

	IP:port                      : 0.0.0.0:443

	Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### Usare Gestione configurazione per configurare il server di report

Se non si desidera eseguire lo script di PowerShell per configurare il server di report, seguire i passaggi descritti in questa sezione per usare Gestione configurazione di Reporting Services in modalità nativa per configurare il server di report.

1. Nel portale di Azure classico selezionare la macchina virtuale e fare clic su Connetti. Usare il nome utente e la password configurati durante la creazione della macchina virtuale.

	![connettersi alla macchina virtuale di azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Eseguire Windows Update e installare gli aggiornamenti nella macchina virtuale. Se è necessario un riavvio della macchina virtuale, riavviare la macchina virtuale e riconnettersi alla macchina virtuale dal portale di Azure classico.

1. Dal menu Start della macchina virtuale digitare **Reporting Services** e aprire **Gestione configurazione Reporting Services**.

1. Lasciare i valori predefiniti per **Nome server** e **Istanza server di report**. Fare clic su **Connect**.

1. Nel riquadro a sinistra fare clic su **URL servizio Web**.

1. Per impostazione predefinita, Reporting Services è configurato per la porta HTTP 80 con IP "Tutti assegnati". Per aggiungere HTTPS:

	1. In **Certificato SSL** selezionare il certificato da usare, ad esempio [nome macchina virtuale].cloudapp.net. Se non è elencato alcun certificato, vedere la sezione **Passaggio 2: Creare un certificato del server** per informazioni su come installare e rendere attendibile il certificato nella macchina virtuale.
	
	1. In **Porta SSL** scegliere 443. Se l'endpoint privato HTTPS è configurato nella macchina virtuale con una porta privata diversa, usare quel valore.
	
	1. Fare clic su **Applica** e attendere il completamento dell'operazione.

1. Nel riquadro a sinistra fare clic su **Database**.

	1. Fare clic su **Modifica database**.
	
	1. Fare clic su **Crea un nuovo database del server di report** e quindi su **Avanti**.
	
	1. Come nome di macchina virtuale lasciare il **Nome server** predefinito e come **Tipo di autenticazione** lasciare il valore predefinito **Utente corrente** - **Sicurezza integrata**. Fare clic su **Avanti**.
	
	1. Lasciare il **Nome database** predefinito come **ReportServer** e fare clic su **Avanti**.
	
	1. Lasciare **Credenziali del servizio** come **Tipo di autenticazione** predefinito e fare clic su **Avanti**.
	
	1. Fare clic su **Avanti** nella pagina **Riepilogo**.
	
	1. Dopo aver completato la configurazione, fare clic su **Fine**.

1. Nel riquadro sinistro fare clic su **URL Gestione report**. Lasciare **Report** come **Directory virtuale** predefinita e fare clic su **Applica**.

1. Fare clic su **Esci** per chiudere Gestione configurazione di Reporting Services.

## Passaggio 4: Aprire la porta di Windows Firewall

>[AZURE.NOTE] Se si usa uno script per configurare il server di report, è possibile ignorare questa sezione. Lo script include un passaggio per aprire la porta del firewall. I valori predefiniti sono la porta 80 per HTTP e la porta 443 per HTTPS.

Per connettersi in modalità remota a Gestione report o al server di report sulla macchina virtuale, è necessario un endpoint TCP nella macchina virtuale. È necessario per aprire la stessa porta nel firewall della macchina virtuale. L'endpoint è stato creato quando è stato eseguito il provisioning della macchina virtuale.

Questa sezione fornisce le informazioni di base su come aprire la porta del firewall. Per altre informazioni, vedere [Configurare un firewall per l'accesso al server di report](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Se si usa uno script per configurare il server di report, è possibile ignorare questa sezione. Lo script include un passaggio per aprire la porta del firewall.

Se per HTTPS è configurata una porta privata diversa da 443, modificare lo script seguente in modo appropriato. Per aprire la porta **443** in Windows Firewall, seguire questa procedura:

1. Aprire una finestra di Windows PowerShell con privilegi amministrativi.

1. Se è stata usata una porta diversa da 443 quando è stato configurato l'endpoint HTTPS nella macchina virtuale, aggiornare la porta nel comando seguente e quindi eseguire il comando:

		New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Completata l'esecuzione del comando, verrà visualizzato **OK** al prompt dei comandi.

Per verificare che la porta è aperta, aprire una finestra di Windows PowerShell ed eseguire il comando seguente:

	get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## Verificare la configurazione

Per verificare che la funzionalità di base del server di report funzioni, aprire il browser con privilegi amministrativi e quindi passare ai seguenti URL del server di report e della gestione report:

- Nella macchina virtuale passare all'URL del server di report:

		http://localhost/reportserver

- Nella macchina virtuale passare all'URL della gestione report:

		http://localhost/Reports

- Dal computer locale passare a Gestione report **remota** nella macchina virtuale. Aggiornare il nome DNS nell'esempio seguente nel modo appropriato. Quando viene richiesta una password, usare le credenziali di amministratore create quando è stato eseguito il provisioning della macchina virtuale. Il nome utente è nel formato [dominio] \[nome utente], dove il dominio è il nome computer della macchina virtuale, ad esempio ssrsnativecloud\\testuser. Se non si usa HTTP**S**, rimuovere la **S** dall'URL. Vedere la sezione successiva per informazioni sulla creazione di utenti aggiuntivi nella macchina virtuale.

		https://ssrsnativecloud.cloudapp.net/Reports

- Dal computer locale passare all'URL del server di report remoto. Aggiornare il nome DNS nell'esempio seguente nel modo appropriato. Se non si usa HTTPS, rimuovere la S dall'URL.

		https://ssrsnativecloud.cloudapp.net/ReportServer

## Creare utenti e assegnare ruoli

Dopo aver configurato e verificato il server di report, un'attività amministrativa comune consiste nel creare uno o più utenti e assegnare agli utenti i ruoli di Reporting Services. Per altre informazioni, vedere quanto segue:

- [Creare un account utente locale](https://technet.microsoft.com/library/cc770642.aspx)

- [Concedere l'accesso utente a un server di report (Gestione report)](https://msdn.microsoft.com/library/ms156034.aspx)

- [Creare e gestire le assegnazioni di ruoli](https://msdn.microsoft.com/library/ms155843.aspx)

## Per creare e pubblicare i report in una macchina virtuale di Azure

Nella tabella seguente vengono riepilogate alcune delle opzioni disponibili per la pubblicazione di report esistenti da un computer locale nel server di report ospitato nella macchina virtuale di Microsoft Azure:

- **RS.exe script**: usare lo script RS.exe per copiare gli elementi del report da un server di report esistente nella macchina virtuale di Microsoft Azure. Per altre informazioni, vedere la sezione "Da modalità nativa a modalità nativa - Macchina virtuale di Microsoft Azure" in [Script di esempio rs.exe di Reporting Services per la migrazione del contenuto tra server di report](https://msdn.microsoft.com/library/dn531017.aspx).

- **Generatore report**: la macchina virtuale include la versione ClickOnce di Generatore report di Microsoft SQL Server. Per avviare il Generatore report per la prima volta sulla macchina virtuale:

	1. Avviare il browser con privilegi amministrativi.
	
	1. Passare a Gestione report nella macchina virtuale e fare clic su **Generatore report** sulla barra multifunzione.

	Per altre informazioni, vedere [Installazione, disinstallazione e supporto di Generatore report](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools - Macchina virtuale**: se la macchina virtuale è stata creata con SQL Server 2012, SQL Server Data Tools è installato nella macchina virtuale e può essere usato per creare **progetti server di report** e report nella macchina virtuale. SQL Server Data Tools è in grado di pubblicare i report nel server di report sulla macchina virtuale.
	
	Se la macchina virtuale è stata creata con SQL server 2014, è possibile installare SQL Server Data Tools - Business Intelligence per Visual Studio. Per altre informazioni, vedere quanto segue:

	- [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

	- [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

	- [SQL Server Data Tools e SQL Server Business Intelligence (SSDT-BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools - Remoto**: nel computer locale creare un progetto di Reporting Services in SQL Server Data Tools contenente i report di Reporting Services. Configurare il progetto per connettersi all'URL del servizio Web.

	![proprietà del progetto ssdt per un progetto SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Utilizzare script**: usare lo script per copiare il contenuto del server di report. Per altre informazioni, vedere [Script di esempio rs.exe di Reporting Services per la migrazione del contenuto tra server di report](https://msdn.microsoft.com/library/dn531017.aspx).

## Ridurre i costi se non si usa la macchina virtuale

>[AZURE.NOTE] Per ridurre i costi delle macchine virtuali di Azure quando non sono in uso, spegnere la macchina virtuale dal portale di Azure classico. Se per spegnere la macchina virtuale si usano le opzioni di risparmio energia di Windows, i costi associati alla macchina virtuale verranno addebitati comunque. Per ridurre i costi, è necessario spegnere la macchina virtuale nel portale di Azure classico. Se la macchina virtuale non è più necessaria, eliminarla insieme ai file con estensione vhd associati per evitare l'addebito dei costi di archiviazione. Per altre informazioni, vedere la sezione Domande frequenti in [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/).

## Altre informazioni

### Risorse

- Per contenuti analoghi relativi alla distribuzione di un server singolo di SQL Server Business Intelligence e SharePoint 2013, vedere l'articolo relativo all'[uso di Windows PowerShell per creare una macchina virtuale di Azure con SQL Server BI e SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Per contenuti analoghi relativi a una distribuzione con più server di SQL Server Business Intelligence e SharePoint 2013, vedere l'articolo relativo alla [distribuzione di SQL Server Business Intelligence in Macchine virtuali di Azure](https://msdn.microsoft.com/library/dn321998.aspx).

- Per informazioni generali relative alle distribuzioni di SQL Server Business Intelligence in Macchine virtuali di Azure, vedere [SQL Server Business Intelligence in Macchine virtuali di Azure](virtual-machines-windows-classic-ps-sql-bi.md).

- Per altre informazioni sul calcolo delle spese di calcolo di Azure, vedere la scheda Macchine virtuali del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### Contenuti della community

- Per istruzioni dettagliate su come creare un server di report di Reporting Services in modalità nativa senza usare lo script, vedere l'articolo relativo all'[hosting del servizio di report SQL in macchine virtuali di Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### Collegamenti ad altre risorse di SQL Server in Macchine virtuali di Azure

[Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-classic-sql-overview.md)

<!---HONumber=AcomDC_0323_2016-->