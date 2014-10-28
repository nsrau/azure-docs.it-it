<properties linkid="cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging" urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Enable remote debugging with continuous delivery" metaKeywords="" description="Learn how to enable remote debugging when using continuous delivery to deploy to Azure." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Enable remote debugging when using continuous delivery to publish to Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Abilitare il debug remoto con la distribuzione continua per la pubblicazione in Azure

Per abilitare il debug remoto in Azure quando si usa la [distribuzione continua][distribuzione continua] per la pubblicazione in Azure, eseguire la procedura seguente.

Contenuto dell'argomento:

[Abilitazione del debug remoto per i servizi cloud][Abilitazione del debug remoto per i servizi cloud]

[Abilitazione del debug remoto per le macchine virtuali][Abilitazione del debug remoto per le macchine virtuali]

## <a name="cloudservice"></a>Abilitazione del debug remoto per i servizi cloud

1.  Nell'agente di compilazione configurare l'ambiente iniziale per Azure come descritto in [Compilazione da riga di comando per Azure][Compilazione da riga di comando per Azure].
2.  Poiché il pacchetto richiede l'installazione del runtime di debug remoto (msvsmon.exe), installare [Remote Tools per Visual Studio 2013][Remote Tools per Visual Studio 2013] o [Remote Tools per Visual Studio 2012 Update 4][Remote Tools per Visual Studio 2012 Update 4] se è installato Visual Studio 2012. In alternativa, copiare i binari di debug remoto da un sistema in cui è installato Visual Studio.
3.  Creare un certificato come descritto in [Creare un certificato di servizio per Azure][Creare un certificato di servizio per Azure]. Mantenere il file con estensione pfx e l'identificazione personale del certificato RDP e caricare il certificato nel servizio cloud di destinazione.
4.  Nella riga di comando MSBuild usare le opzioni seguenti per eseguire la compilazione e creare un pacchetto con il debug remoto abilitato. Aggiornare il percorso in base al sistema e ai file di progetto in uso.

          /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;
          VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";
          RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";
          RemoteDebuggerConnectorVersion="2.4"
          "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Eseguire la pubblicazione nel servizio cloud di destinazione usando il pacchetto e il file di configurazione (CSCFG) generato nel passaggio precedente.
6.  Importare il certificato (file PFX) nel computer in cui è installato Visual Studio con Azure SDK 2.4.

## <a name="virtualmachine"></a>Abilitazione del debug remoto per le macchine virtuali

1.  Creare una macchina virtuale di Azure. Per informazioni, vedere [Creazione di una macchina virtuale che esegue Windows Server][Creazione di una macchina virtuale che esegue Windows Server] o [Creazione di macchine virtuali di Azure in Visual Studio][Creazione di macchine virtuali di Azure in Visual Studio].
2.  Nella [pagina del portale di Azure][pagina del portale di Azure] visualizzare il dashboard della macchina virtuale per individuare l'identificazione personale certificato RDP della macchina virtuale. L'identificazione digitale viene usata per il valore ServerThumbprint nella configurazione dell'estensione.
3.  Creare un certificato client come descritto in [Creare un certificato di servizio per Azure][Creare un certificato di servizio per Azure] (conservare il file PFX e l'identificazione personale RDP).
4.  Installare [Azure Powershell][Azure Powershell] (versione 0.7.4 o successiva) dall'Area download Microsoft.
5.  Eseguire lo script seguente per abilitare l'estensione RemoteDebug. Sostituire i dati con i dati personali dell'utente, ad esempio nome della sottoscrizione, nome del servizio e identificazione personale. NOTA: questo script è configurato per Visual Studio 2013. Se è installato Visual Studio 2012, specificare "RemoteDebugVS2013" per ReferenceName ed ExtensionName.

	<pre>
    Add-AzureAccount
    
    Select-AzureSubscription "My Microsoft Subscription"
    
    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"
    
    $endpoints = @(
    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
    )
    
    foreach($endpoint in $endpoints)
    {
    Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }
    
    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2013"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"
    
    $vm | Set-AzureVMExtension `
    -ReferenceName $referenceName `
    -Publisher $publisher `
    -ExtensionName $extensionName `
    -Version $version `
    -PublicConfiguration $publicConfiguration
    
    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {   
    if(($extension.ReferenceName -eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) `
    -and ($extension.Name -eq $extensionName) `
    -and ($extension.Version -eq $version))
    {
    $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
    break
    }
    }
    
    $vm | Update-AzureVM 
	</pre>

6.  Importare il certificato (file PFX) nel computer in cui è installato Visual Studio con Azure SDK per .NET 2.4.

  [distribuzione continua]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Abilitazione del debug remoto per i servizi cloud]: #cloudservice
  [Abilitazione del debug remoto per le macchine virtuali]: #virtualmachine
  [Compilazione da riga di comando per Azure]: http://msdn.microsoft.com/it-it/library/hh535755.aspx
  [Remote Tools per Visual Studio 2013]: http://www.microsoft.com/it-it/download/details.aspx?id=40781
  [Remote Tools per Visual Studio 2012 Update 4]: http://www.microsoft.com/it-it/download/details.aspx?id=38184
  [Creare un certificato di servizio per Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Creazione di una macchina virtuale che esegue Windows Server]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-windows-tutorial/
  [Creazione di macchine virtuali di Azure in Visual Studio]: http://msdn.microsoft.com/it-it/library/azure/dn569263.aspx
  [pagina del portale di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [Azure Powershell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
