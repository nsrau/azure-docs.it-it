<properties 
   pageTitle="Compilazione di configurazioni in Azure Automation DSC | Microsoft Azure" 
   description="Panoramica di due modi per compilare configurazioni dello stato desiderato (DSC, Desired State Configuration): nel portale di Azure e con Windows PowerShell." 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="01/25/2016"
   ms.author="coreyp"/>
   
#Compilazione di configurazioni in Azure Automation DSC#

È possibile compilare configurazioni dello stato desiderato (DSC, Desired State Configuration) in due modi con Automazione di Azure: nel portale di Azure e con Windows PowerShell. La tabella seguente consente di determinare quando usare ciascun metodo in base alle caratteristiche specifiche:

###Portale di anteprima di Azure###
- Metodo più semplice con interfaccia utente interattiva
- Modulo per specificare valori di parametri semplici
- Facilità di controllo dello stato dei processi
- Accesso autenticato con l'accesso di Azure

###Windows PowerShell###
- Chiamata mediante cmdlet di Windows PowerShell nella riga di comando
- Possibilità di inclusione in una soluzione automatizzata con più passaggi
- Possibilità di specificare valori di parametri semplici e complessi
- Possibilità di controllare lo stato dei processi
- Obbligo per il client di supporto dei cmdlet di PowerShell
- Passaggio di ConfigurationData
- Compilazione di configurazioni che usano credenziali

Una volta scelto il metodo di compilazione, è possibile seguire le rispettive procedure indicate sotto per iniziare la compilazione.

##Compilazione di una configurazione DSC con il portale di Azure##

1.  Nell'account di automazione fare clic su **Configurazioni**.
2.  Fare clic su una configurazione per aprirne il pannello.
3.  Fare clic su **Compila**.
4.  Se la configurazione non ha alcun parametro, verrà richiesto di confermare se compilarla. Se la configurazione contiene parametri, verrà aperto il pannello **Compila configurazione** in cui sarà possibile specificare i valori dei parametri. Per altri dettagli sui parametri, vedere la sezione <a href="#basic-parameters">**Parametri di base**</a> più avanti.
5.  Viene aperto il pannello **Processo di compilazione** in cui è possibile tenere traccia dello stato del processo di compilazione e delle configurazioni dei nodi (documenti di configurazione MOF) che sono state inserite nel server di pull di Azure Automation DSC.

##Compilazione di una configurazione DSC con Windows PowerShell##

È possibile usare [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) per avviare la compilazione con Windows PowerShell. Il codice di esempio seguente avvia la compilazione di una configurazione DSC denominata **SampleConfig**.

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` restituisce un oggetto processo di compilazione che è possibile usare per tenere traccia dello stato. È quindi possibile usare questo oggetto processo di compilazione con [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) per determinare lo stato del processo di compilazione e con [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) per visualizzarne i flussi (output). Il codice di esempio seguente avvia la compilazione della configurazione **SampleConfig**, attende che venga completata e quindi ne visualizza i flussi.
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {
    	$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##Parametri di base##

La dichiarazione dei parametri nelle configurazioni DSC, inclusi i tipi e le proprietà dei parametri, funziona esattamente come nei runbook di Automazione di Azure. Per altre informazioni sui parametri dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).

L'esempio seguente usa due parametri denominati **FeatureName** e **IsPresent**, per determinare i valori delle proprietà nella configurazione del nodo **ParametersExample.sample**, generati durante la compilazione.

    Configuration ParametersExample
    {
    	param(
    		[Parameter(Mandatory=$true)]
    
    		[string] $FeatureName,
    
    		[Parameter(Mandatory=$true)]
    		[boolean] $IsPresent
    	)
    
    	$EnsureString = "Present"
    	if($IsPresent -eq $false)
    	{
    		$EnsureString = "Absent"
    	}
    
    	Node "sample"
    	{
    		WindowsFeature ($FeatureName + "Feature")
    		{
    			Ensure = $EnsureString
    			Name = $FeatureName
    		}
    	}
    }

È possibile compilare configurazioni DSC che usano parametri di base nel portale di Automation DSC per Azure o con Azure PowerShell:

###Portale###

Nel portale è possibile immettere i valori dei parametri dopo avere fatto clic su **Compila**.

![testo alternativo](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell richiede i parametri in un elemento [hashtable](http://technet.microsoft.com/library/hh847780.aspx) dove la chiave corrisponde al nome del parametro e il valore è uguale al valore del parametro.

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

Per informazioni sul passaggio di PSCredentials come parametri, vedere <a href="#credential-assets">**Asset credenziali**</a> più avanti.

##ConfigurationData##

**ConfigurationData** consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Vedere il blog relativo alla [distinzione tra "cosa" e "dove" in PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) per altre informazioni su **ConfigurationData**.

>[AZURE.NOTE] È possibile usare **ConfigurationData** quando si compila in Azure Automation DSC con Azure PowerShell, ma non nel portale di Azure.

La configurazione DSC di esempio seguente usa **ConfigurationData** con le parole chiave **$ConfigurationData** e **$AllNodes**. Per questo esempio sarà necessario anche il [modulo **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

     Configuration ConfigurationDataSample
     {
    	Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    
    	Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 
    
    	Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    	{
    		xWebsite Site
    		{
    			Name = $Node.SiteName
    			PhysicalPath = $Node.SiteContents
    			Ensure   = "Present"
    		}
    	}
    }

È possibile compilare la configurazione DSC precedente con PowerShell. Il cmdlet di PowerShell seguente aggiunge due configurazioni di nodo al server di pull di Automation DSC per Azure, **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

    $ConfigData = @{
    	AllNodes = @(
			@{
    			NodeName = "MyVM1"
    			Role = "WebServer"
    		},
    		@{
    			NodeName = "MyVM2"
    			Role = "SQLServer"
    		},
    		@{
    			NodeName = "MyVM3"
    			Role = "WebServer"
    
    		}
    
    	)
    
    	NonNodeData = @{
    		SomeMessage = "I love Azure Automation DSC!"
    
    	}
    
    } 
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


##Asset##

I riferimenti agli asset sono gli stessi nelle configurazioni di Azure Automation DSC e nei runbook. Per altre informazioni, vedere quanto segue:

- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Variabili](automation-variables.md)

###Asset credenziali###
Anche se le configurazioni DSC in Automazione di Azure possono fare riferimento ad asset delle credenziali con **Get-AutomationPSCredential**, gli asset delle credenziali possono essere passati anche con i parametri, se necessario. Se una configurazione accetta un parametro di tipo **PSCredential**, è necessario passare il nome stringa di un asset credenziale di Automazione di Azure come valore di tale parametro, invece di un oggetto PSCredential. In background, l'asset credenziali di Automazione di Azure con tale nome verrà recuperato e passato alla configurazione.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. Automazione di Azure va oltre e crittografa l'intero file MOF. Attualmente è però necessario comunicare a PowerShell DSC che l'output delle credenziali in testo normale durante la generazione dei file MOF delle configurazioni dei nodi è corretto, perché PowerShell DSC non sa che dopo la generazione Automazione di Azure crittograferà l'intero file MOF tramite un processo di compilazione.

Per comunicare a PowerShell DSC che l'output delle credenziali in testo normale nei file MOF delle configurazioni dei nodi generati è corretto, è possibile usare <a href="#configurationdata">**ConfigurationData**</a>. È consigliabile passare `PSDscAllowPlainTextPassword = $true` tramite **ConfigurationData** per ogni nome del blocco di nodi che viene visualizzato nella configurazione DSC e usa le credenziali.

L'esempio seguente mostra una configurazione DSC che usa un asset credenziali di Automazione.

    Configuration CredentialSample
    {
       $Cred = Get-AutomationPSCredential -Name "SomeCredentialAsset"
    
    	Node $AllNodes.NodeName
    	{ 
    		File ExampleFile
    		{ 
    			SourcePath = "\\Server\share\path\file.ext" 
    			DestinationPath = "C:\destinationPath" 
    			Credential = $Cred 
       		}
    	}
    }

È possibile compilare la configurazione DSC precedente con PowerShell. Il cmdlet di PowerShell seguente aggiunge due configurazioni di nodo al server di pull di Automation DSC per Azure, **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.


    $ConfigData = @{
    	AllNodes = @(
    		@{
    			NodeName = "*"
    			PSDscAllowPlainTextPassword = $True
    		},
    		@{
    			NodeName = "MyVM1"
    		},
    		@{
    			NodeName = "MyVM2"
    		}
    	)
    }
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData

<!---HONumber=AcomDC_0518_2016-->