<properties 
	pageTitle="Connettere un'app a una rete virtuale con PowerShell" 
	description="Istruzioni su come connettere e lavorare con le reti virtuali V1 o V2 tramite PowerShell" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="ccompy"/>

# Connettere l'app alla rete virtuale con PowerShell #

## Panoramica ##

Nel Servizio app di Azure è possibile connettere l'applicazione (Web, mobile o API) a una rete virtuale nella sottoscrizione. Questa funzionalità è denominata Integrazione rete virtuale. La funzionalità Integrazione rete virtuale non deve essere confusa con la funzionalità Ambiente del servizio app, che consente di eseguire un'istanza del Servizio App di Azure nella rete virtuale.

La funzionalità Integrazione rete virtuale dispone di interfaccia utente nel nuovo portale e consente l'integrazione con la rete virtuale V1 o V2. Per altre informazioni sulla funzionalità, vedere Integrare un'app in una rete virtuale di Azure.

In questo articolo non vengono descritte le modalità d'uso dell'interfaccia utente, ma i metodi per abilitare l'integrazione con PowerShell. I comandi per le reti virtuali V1 sono diversi rispetto a quelli per le reti virtuali V2. Pertanto, vengono presentate due sezioni.

Prima di leggere il documento, assicurarsi di disporre:

1. Dell'SDK di Azure PowerShell più recente. Per installarlo, è possibile usare l'Installazione guidata piattaforma Web.
1. Di un'app nel Servizio App di Azure in esecuzione all'interno di uno SKU Standard o Premium.

## Di reti virtuali V1 (classica) ##

Questo documento descrive tre elementi per le reti virtuali V1

- collegamento dell'app a una rete virtuale V1 preesistente, dotata di gateway e configurata da punto a sito
- aggiornamento delle informazioni dell'Integrazione della rete virtuale per l'app
- disconnessione dell'app dalla rete virtuale V1

### Collegare un'app a una rete virtuale V1 (classica) ###

Il collegamento di un'app a una rete virtuale è un processo a tre fasi:

1. Dichiarare l'app Web da aggiungere a una specifica rete virtuale. L'app genera un certificato che viene trasmesso alla rete virtuale per l'esecuzione della connettività da punto a sito
1. Caricare il certificato dell'app Web nella rete virtuale, quindi recuperare l'URI del pacchetto VPN da punto a sito
1. Aggiornare la connessione alla rete virtuale delle app Web con l'URI del pacchetto da punto a sito

I passaggi 1) e 3) riportati in alto sono completamente configurabile tramite script, mentre il passaggio 2) richiede un'unica azione manuale tramite il portale o l'accesso per eseguire azioni PUT o PATCH sull'endpoint ARM della rete virtuale (contattare il supporto tecnico di Azure per richiedere l'attivazione). Prima di iniziare, verificare che la rete virtuale classica con funzionalità da punto a sito sia attiva e che disponga di un gateway creato/distribuito. Per creare il gateway e abilitare la funzionalità da punto a sito è necessario usare il portale come descritto in [Creare un gateway VPN][createvpngateway]

La rete virtuale V1 deve trovarsi nella stessa sottoscrizione del Piano di servizio app che contiene l'applicazione che si sta integrando.

#####Configurare l'SDK di Azure PowerShell#####

Aprire una finestra di PowerShell, quindi configurare l'account e la sottoscrizione Azure tramite:

	Login-AzureRmAccount

Il comando consente di aprire un prompt per ricevere le credenziali di Azure. Dopo aver effettuato l'accesso, usare

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

oppure

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

per selezionare la sottoscrizione da usare. Assicurarsi di usare la sottoscrizione che contiene la rete virtuale e il Piano di servizio app.

#####Variabili presenti in questo documento#####

Per semplificare i comandi seguenti, verrà impostata una variabile di PowerShell $Configuration con la configurazione specifica.

Impostare la variabile in PowerShell secondo la procedura riportata di seguito, usare i seguenti parametri:

	$Configuration = @{} 
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]" 
	$Configuration.VnetResourceGroup = "[Your vnet resource group]" 
	$Configuration.VnetName = "[Your vnet name]"
 
Il percorso dell'app non deve contenere spazi: ad esempio Stati Uniti occidentali diventa statiunitioccidentali

	$Configuration.WebAppLocation = "[Your web app Location]" 

Il prossimo elemento identifica il percorso in cui deve essere scritto il certificato. Deve essere un percorso accessibile in scrittura sul sistema locale. Assicurarsi di includere .cer alla fine.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]" 

Per verificare le impostazioni applicate, digitare $Configuration.

	> $Configuration
	
	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

Nel resto del documento si presuppone che sia stata creata una variabile secondo la procedura descritta.

#####Dichiarare la rete virtuale all'app#####

Indicare l'app che usa la rete virtuale specifica con il seguente comando. In questo modo l'app genererà i certificati necessari:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se questo comando funziona, $vnet deve contenere la variabile Proprietà. La variabile Proprietà deve contenere un'identificazione personale del certificato e i dati del certificato.

#####Caricare il certificato dell'app Web nella rete virtuale#####

Si tratta di un unico passaggio manuale necessario per ogni combinazione di sottoscrizione e rete virtuale. Ovvero, se si connettono le applicazioni della Sottoscrizione A con la Rete virtuale A, è necessario eseguire questo passaggio solo una volta, indipendentemente dal numero di app da configurare. Se si aggiunge una nuova applicazione a un'altra rete virtuale, è necessario eseguire il passaggio indicato. Il motivo è che viene generato un set di certificati a livello di sottoscrizione nel Servizio app di Azure una volta per ogni rete virtuale a cui verrà connessa l'app.

Se è stata eseguita la procedura o se sono stati integrati con la stessa rete virtuale tramite il portale, i certificati saranno già impostati.

Il primo passaggio consiste nel generare il file con estensione .cer. Il secondo passaggio consiste nel caricare il file .cer nella rete virtuale. Per generare il file. cer dalla chiamata dell'API descritta nel passaggio precedente, eseguire i comandi riportati in basso.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Il certificato è situato nel percorso specificato con $Configuration.GeneratedCertificatePath

Per caricare il certificato manualmente, usare il nuovo portale all'interno del [Portale di Azure][azureportal] e selezionare Cerca rete virtuale (classica) -> Connessioni VPN -> Da punto a sito -> Gestione certificati. A questo punto caricare il certificato.

#####Ottenere il pacchetto Da punto a sito#####

Il prossimo passaggio per l'impostazione di una connessione di rete virtuale su un'app Web consiste nell'ottenere il pacchetto Da punto a sito per fornirlo all'app Web.

Salvare il modello seguente in un file denominato GetNetworkPackageUri.json in qualsiasi posizione del computer in uso, ad es. C:\\Azure\\Templates\\GetNetworkPackageUri.json

	{ 
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#", 
		"contentVersion": "1.0.0.0", 
		"parameters": { 
			"certData": { 
				"type": "string" 
			}, 
			"certThumbprint": { 
				"type": "string" 
			}, 
			"networkName": { 
				"type": "string" 
			} 
		}, 
		"variables": { 
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]" 
			}, 
			"resources": [ 
			], 
		"outputs" : { 
			"PackageUri" : 
			{ 
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string" 
			} 
		} 
	} 


Impostare i parametri di input

	$parameters = @{"certData" = $vnet.Properties.certBlob ; 
	certThumbprint = $vnet.Properties.certThumbprint ; 
	"networkName" = $Configuration.VnetName } 

Chiamare lo script:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json 


La variabile $output.Outputs.packageUri conterrà il pacchetto URI da assegnare all'app Web.

#####Caricare il pacchetto da punto a sito nell'app#####

Il passaggio finale consiste nell'assegnazione del pacchetto all'applicazione. È sufficiente eseguire il comando che segue:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation 

È possibile ricevere un messaggio che richiede la conferma della sovrascrittura di una risorsa esistente. Confermare.

Quando il comando termina l'operazione, l'app dovrebbe essere connessa alla rete virtuale. Per verificare, accedere alla console dell'app e digitare quanto segue:

	SET WEBSITE_

Se è presente una variabile Ambiente denominata WEBSITE\_VNETNAME con un valore corrispondente al nome della rete virtuale di destinazione, tutte le configurazioni sono state completate.

###Aggiornare le informazioni sull'integrazione della rete virtuale V1 (classica)###

L'aggiornamento o una nuova sincronizzazione delle informazioni richiede la semplice ripetizione dei passaggi eseguiti durante la prima creazione dell'integrazione. I passaggi sono:

1. definire le informazioni di configurazione
1. dichiarare la rete virtuale all'app
1. ottenere il pacchetto da punto a sito
1. caricare il pacchetto da punto a sito nell'app

###Disconnettere l'app da una rete virtuale V1 (classica)###

Per eseguire la disconnessione sono necessarie le informazioni di configurazione impostate durante l'integrazione della rete virtuale. Con le informazioni, è possibile usare un unico comando per disconnettere l'app dalla rete virtuale.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Reti virtuali V2 (Gestione risorse) ##

Le reti virtuali V2 o Gestione risorse sono dotate di API ARM e consentono di eseguire una procedura più semplice rispetto a quella usata per le reti virtuali V1. Disponiamo di uno script che consente di:

- Creare una rete virtuale V2 e integrarne l'app
- Creare un gateway e configurare la funzionalità da punto a sito all'interno di una rete virtuale V2 preesistente e quindi di integrare l'app
- Integrare l'app con una rete virtuale V2 preesistente dotata di gateway e funzionalità da punto a sito attiva 
- Disconnettere l'app dalla rete virtuale V2

###Script di integrazione del servizio app della rete virtuale V2###

Copiare il seguente script e salvarlo in un file. Se non si desidera usare lo script nella forma in cui si presenta, è possibile imparare dallo script a effettuare la connessione con una rete virtuale V2.


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}
    
	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host
    
	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]
		    
		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }
    
    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }
    
    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }
    
    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	    
	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip
	    
	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData
	    
	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }
    
    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"
	    
	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"
	    
	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"
	    
	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup
	    
	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
		    
		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }
    
	    $ErrorActionPreference = "Stop";
	    
	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.
	    
	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location
	    
	    Write-Host "Creating App assocation to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	       
	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location
	    
	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
	    
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	    
	    Write-Host "Finished!"
    }
    
    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";
		
		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location
		   
		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}
		
		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }
	    
	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames
	    
	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.
	       
	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }
	       
	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."
		    
		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"
		    
		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"
		    
		    $changeRequested = 0
		    
		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }
		    
		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"
		    
		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
			    
			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }
		    
		    $ErrorActionPreference = "Stop";
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }
		    
		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
		    
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]
		       
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName
		    
		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates
		    
		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }
		    
		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }
	    
	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
	    
	    Write-Host "Finished!"
    }
       
    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"
		    
		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }
    
    Write-Host "Please Login"
    Login-AzureRmAccount
    
    # Choose subscription. If there's only one we will choose automatically
    
    $subs = Get-AzureRmSubscription
    $subscriptionId = ""
    
    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }
    
    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()
	       
	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }
	    
	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $resourceGroup = Read-Host "Please enter the Resource Group of your App"
    
    $appName = Read-Host "Please enter the Name of your App"
    
    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options
    
    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }
    
Salvare una copia dello script. In questo documento lo script è stato denominato V2VnetAllinOne.ps1 ma è possibile assegnare qualsiasi nome desiderato. Non sono presenti argomenti su questo script. È sufficiente eseguirlo. La prima azione eseguita dallo script è la richiesta di accesso. Una volta effettuato l'accesso, lo script acquisisce le informazioni sull'account dell'utente e restituisce un elenco di sottoscrizioni tra cui scegliere. Senza la finestra che richiede le credenziali, l'esecuzione dello script iniziale è simile al seguente:

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login
	
	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :
	
	Choose a subscription
	
	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)
	
	Choose an option:
	3
	
	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47
	
	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?
	
	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

Nel resto di questo documento vengono presentate le 3 opzioni.

###Creare una rete virtuale V2(Gestione risorse) ed effettuare l'integrazione###

Per creare una nuova rete virtuale V2 e integrarla con l'app, selezionare 1) Aggiungi una NUOVA rete virtuale a un'app. Viene richiesto il nome della rete virtuale. Nel mio caso presentato in basso è stato assegnato il nome v2pshell.

Lo script mostra i dettagli della rete virtuale che si sta creando. Se si desidera, è possibile modificare qualsiasi valori. Nell'esecuzione di esempio è stata creata una rete virtuale con le seguenti impostazioni:

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

Se si desidera modificare i valori, digitare Y e apportare le modifiche desiderate. Una volta terminate le impostazioni della rete virtuale, digitare N o premere semplicemente Indietro quando richiesto sulla pagina di modifica delle impostazioni. Da quel punto e fino al completamento, lo script indicherà alcune delle attività in esecuzione fino alla creazione del gateway della rete virtuale. Questo passaggio può richiedere fino a un'ora. In questa fase non è disponibile un indicatore di stato, ma è possibile sapere quando il gateway è stato creato.

Al termine di questa attività, lo script visualizza **Operazione completata**. A questo punto si dispone di una rete virtuale V2 creata con il nome e le impostazioni selezionate. Anche questa nuova rete virtuale verrà integrata con l'applicazione.

###Integrare l'app con una rete virtuale V2 preesistente###

Quando si effettua l'integrazione con una rete virtuale preesistente, se si fornisce una rete virtuale V2 che non dispone di un gateway o della funzionalità da punto a sito, lo script procederà alla creazione. Se la rete virtuale dispone già di queste impostazioni, procedere direttamente con l'integrazione dell'app. Per avviare questo processo è sufficiente selezionare l'opzione 2) Aggiungi una rete virtuale ESISTENTE a un'app.

Questa opzione funziona solo se si dispone di una rete virtuale V2 esistente all'interno della stessa sottoscrizione dell'app. Dopo aver selezionato l'opzione 2, verrà visualizzato un elenco di reti virtuali V2.

	Select a VNET to integrate with
	
	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2
	
	Choose an option:
	5

È sufficiente selezionare la rete virtuale con cui si desidera effettuare l'integrazione. Se si dispone già di un gateway con la funzionalità da punto a sito abilitata, lo script procede direttamente all'integrazione dell'app con la rete virtuale. Se non si dispone di un gateway, è necessario specificare la subnet del gateway. La subnet del gateway deve essere situata nello spazio degli indirizzi della rete virtuale e non può essere in un'altra subnet. Quindi, se si dispone di una rete virtuale non dotata di gateway e si esegue questo passaggio, l'aspetto sarà simile al seguente:

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

In questo esempio è stata creato un gateway della una rete virtuale con le seguenti impostazioni:

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App assocation to VNET

È possibile modificare le impostazioni. In alternativa, premere Indietro per procedere alla creazione del gateway e all'associazione dell'app alla rete virtuale. Il tempo necessario alla creazione del gateway è un'ora. Accertarsi quindi di ricordarlo. Al termine di tutte le attività, lo script visualizza Operazione completata.

###Disconnettere l'app dalla rete virtuale V2###

La disconnessione dell'app dalla rete virtuale V2 non causa l'arresto del gateway e non disattiva la funzionalità da punto a sito. Dopo tutto è possibile che questi elementi siano in funzione per altri scopi. Non si verifica la disconnessione da qualsiasi altra app diversa da quello fornita. Per eseguire questa operazione, selezionare 3) Rimuovi una rete virtuale da un'app. Viene visualizzata una schermata simile alla seguente:

	Currently connected to VNET v2pshell
	
	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Lo script chiede di rimuovere l'elemento, ma non elimina la rete virtuale. Rimuove solo l'integrazione. Dopo aver confermato, il comando viene elaborato molto rapidamente e al termine visualizza True.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->