---
title: Connettere l&quot;app alla rete virtuale tramite PowerShell
description: Istruzioni sulla connessione e l&quot;uso di reti virtuali con PowerShell
services: app-service
documentationcenter: 
author: ccompy
manager: wpickett
editor: cephalin
ms.assetid: a5c76e77-972a-431c-b14b-3611dae1631b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7b0dcf833981364abfbc77d0cd6dfde8beb081b7


---
# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Connettere l'app alla rete virtuale tramite PowerShell
## <a name="overview"></a>Overview
Nel servizio app di Azure è possibile connettere l'app Web, l'app per dispositivi mobili o l'app per le API a una rete virtuale (VNet) di Azure nella sottoscrizione. Questa funzionalità è detta integrazione rete virtuale. La funzionalità di integrazione rete virtuale non deve essere confusa con la funzionalità Ambiente del servizio app, che consente di eseguire un'istanza del servizio app di Azure nella rete virtuale.

La funzionalità di integrazione rete virtuale ha un'interfaccia utente nel nuovo portale che può essere usata per l'integrazione con le reti virtuali che vengono distribuite tramite il modello di distribuzione classica o il modello di distribuzione Azure Resource Manager. Per altre informazioni sulla funzionalità, vedere [Integrare un'app in una rete virtuale di Azure](web-sites-integrate-with-vnet.md).

Questo articolo non illustra l'uso dell'interfaccia utente, ma piuttosto come abilitare l'integrazione usando PowerShell. Poiché ogni modello di distribuzione ha comandi diversi, questo articolo contiene una sezione per ogni modello di distribuzione.  

Prima di continuare con l'articolo, verificare la disponibilità di quanto segue:

* SDK di Azure PowerShell più recente. Per installarlo, è possibile usare l'Installazione guidata piattaforma Web.
* Un'app nel servizio app di Azure in esecuzione all'interno di uno SKU Standard o Premium.

## <a name="classic-virtual-networks"></a>Reti virtuali classiche
Questa sezione illustra tre attività per le reti virtuali che usano il modello di distribuzione classica:

1. Collegare l'app a una rete virtuale preesistente dotata di gateway e configurata per la connettività da punto a sito.
2. Aggiornare le informazioni di integrazione della rete virtuale per l'app
3. Disconnettere l'app dalla rete virtuale.

### <a name="connect-an-app-to-a-classic-vnet"></a>Connettere un'app a una rete virtuale classica
Per connettere un'app a una rete virtuale, seguire questa procedura:

1. Dichiarare all'app Web l'aggiunta a una rete virtuale specifica. L'app genera un certificato che viene trasmesso alla rete virtuale per la connettività da punto a sito.
2. Caricare il certificato dell'app Web nella rete virtuale e quindi recuperare l'URI del pacchetto VPN da punto a sito.
3. Aggiornare la connessione alla rete virtuale dell'app Web con l'URI del pacchetto da punto a sito.

Il primo e il terzo passaggio sono completamente configurabili tramite script, mentre il secondo passaggio richiede un'unica azione manuale tramite il portale o l'accesso per eseguire azioni **PUT** o **PATCH** sull'endpoint di Azure Resource Manager della rete virtuale. Contattare il supporto di Azure per questa opzione. Prima di iniziare, assicurarsi che sia già abilitata una rete virtuale classica con connettività da punto a sito e che sia disponibile un gateway distribuito. Per creare il gateway e abilitare la connettività da punto a sito è necessario usare il portale, come descritto in [Creazione di un gateway VPN][createvpngateway].

La rete virtuale classica deve essere nella stessa sottoscrizione del piano di servizio app che contiene l'app con cui si sta eseguendo l'integrazione.

##### <a name="set-up-azure-powershell-sdk"></a>Configurare Azure PowerShell SDK
Aprire una finestra di PowerShell e configurare l'account e la sottoscrizione di Azure con:

    Login-AzureRmAccount

Il comando consente di aprire un prompt per ricevere le credenziali di Azure. Dopo l'accesso, usare uno dei comandi seguenti per selezionare la sottoscrizione da usare. Assicurarsi di usare la sottoscrizione che include la rete virtuale e il piano di servizio app usati.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

oppure

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variabili usate in questo articolo
Per semplificare i comandi, verrà impostata una variabile **$Configuration** di PowerShell con la configurazione specifica.

Impostare la variabile in PowerShell secondo la procedura riportata di seguito, usare i seguenti parametri:

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

La località dell'app non deve contenere spazi. Ad esempio West US, per gli Stati Uniti occidentali, sarà westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

Il prossimo elemento identifica il percorso in cui deve essere scritto il certificato. Deve essere un percorso accessibile in scrittura nel computer locale. Assicurarsi di includere .cer alla fine.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Per verificare le impostazioni applicate, digitare **$Configuration**.

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

Nella parte restante di questa sezione si presuppone che sia stata creata una variabile come descritto in precedenza.

##### <a name="declare-the-virtual-network-to-the-app"></a>Dichiarare la rete virtuale all'app
Usare il comando seguente per indicare all'app che verrà usata questa rete virtuale specifica. In questo modo l'app genererà i certificati necessari:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se il comando ha esito positivo, **$vnet** deve contenere una variabile **Properties**. La variabile **Properties** deve contenere sia un'identificazione personale del certificato che i dati del certificato.

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Caricare il certificato dell'app Web nella rete virtuale
Per ogni combinazione di sottoscrizione e rete virtuale è necessario eseguire un unico passaggio manuale. Se, ad esempio, si connettono le app della Sottoscrizione A alla Rete virtuale A, è necessario eseguire questo passaggio solo una volta, indipendentemente dal numero di app da configurare. Se si aggiunge una nuova app a un'altra rete virtuale, sarà necessario ripetere l'operazione. Il motivo è che il set di certificati generato a livello di sottoscrizione nel servizio app di Azure viene generato una sola volta per ogni rete virtuale a cui le app si devono connettere.

Se è stata seguita la procedura oppure è stata eseguita l'integrazione con la stessa rete virtuale tramite il portale, i certificati sono già impostati.

Il primo passaggio consiste nel generare il file con estensione .cer. Il secondo passaggio consiste nel caricare il file CER nella rete virtuale. Per generare il file. cer dalla chiamata dell'API descritta nel passaggio precedente, eseguire i comandi riportati in basso.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Il certificato si trova nel percorso specificato da **$Configuration.GeneratedCertificatePath** .

Per caricare il certificato manualmente, usare il [portale di Azure][azureportal] e selezionare **Esplora > Rete virtuale (versione classica)** > **Connessioni VPN** > **Da punto a sito** > **Gestisci certificati**. A questo punto, caricare il certificato.

##### <a name="get-the-point-to-site-package"></a>Ottenere il pacchetto da punto a sito
Il passaggio successivo nella configurazione di una connessione di rete virtuale in un'app Web consiste nell'ottenere il pacchetto da punto a sito per fornirlo all'app Web.

Salvare il modello seguente in un file denominato GetNetworkPackageUri.json in un percorso nel computer, ad esempio in C:\Azure\Templates\GetNetworkPackageUri.json.

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


Impostare i parametri di input:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

Chiamare lo script:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


La variabile **$output.Outputs.packageUri** contiene ora il pacchetto URI da fornire all'app Web.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Caricare il pacchetto da punto a sito nell'app
Il passaggio finale consiste nell'assegnazione del pacchetto all'applicazione. È sufficiente eseguire questo comando:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Potrebbe essere visualizzato un messaggio che richiede la conferma della sovrascrittura di una risorsa esistente. Confermare l'operazione.

Se il comando ha esito positivo, l'app dovrebbe essere connessa alla rete virtuale. Verificare passando alla console dell'app e digitando quanto segue:

    SET WEBSITE_

Se è presente una variabile di ambiente denominata WEBSITE_VNETNAME con un valore corrispondente al nome della rete virtuale di destinazione, tutte le configurazioni sono state completate.

### <a name="update-classic-vnet-integration-information"></a>Aggiornare le informazioni di integrazione rete virtuale classica
Per aggiornare o risincronizzare le informazioni è sufficiente ripetere i passaggi seguiti per la creazione dell'integrazione. I passaggi sono:

1. Definire le informazioni di configurazione.
2. Dichiarare la rete virtuale all'app.
3. Ottenere il pacchetto da punto a sito.
4. Caricare il pacchetto da punto a sito nell'app.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Disconnettere l'app da una rete virtuale classica
Per disconnettere l'app sono necessarie le informazioni di configurazione impostate durante l'integrazione della rete virtuale. Con tali informazioni, è possibile usare un unico comando per disconnettere l'app dalla rete virtuale.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Reti virtuali di Azure Resource Manager
Le reti virtuali di Azure Resource Manager usano le API di Azure Resource Manager, che permettono di semplificare alcuni processi rispetto alle reti virtuali classiche. È disponibile uno script che consente completare le attività seguenti:

* Creare una rete virtuale di Azure Resource Manager e integrarla con l'app.
* Creare un gateway, configurare la connettività da punto a sito in una rete virtuale di Azure Resource Manager esistente e quindi integrarla con l'app.
* Integrare l'app con una rete virtuale di Azure Resource Manager esistente che abbia un gateway e la connettività da punto a sito abilitata.
* Disconnettere l'app dalla rete virtuale.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Script di integrazione del servizio app della rete virtuale di Azure Resource Manager
Copiare lo script seguente e salvarlo in un file. Se si preferisce non usare lo script, è comunque possibile prenderne spunto per la configurazione di una rete virtuale di Azure Resource Manager.

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

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
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

            Write-Host "Creating App association to VNET"
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

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
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
        Write-Host "Retrieving VPN Package and supplying to App"
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

Salvare una copia dello script. In questo articolo è denominato V2VnetAllinOne.ps1, ma è possibile usare un altro nome. Non sono presenti argomenti su questo script. È sufficiente eseguirlo. La prima azione eseguita dallo script è la richiesta di accesso. Dopo l'accesso, lo script ottiene i dettagli sull'account dell'utente e restituisce un elenco di sottoscrizioni. Senza la richiesta di credenziali, l'esecuzione dello script iniziale è simile a quanto segue:

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

    Choose an option: 3

    Account      : ccompy@microsoft.com Environment  : AzureCloud Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d Tenant       : 722278f-fef1-499f-91ab-2323d011db47

    Please enter the Resource Group of your App: hcdemo-rg Please enter the Name of your App: v2vnetpowershell What do you want to do?

    1) Add a NEW Virtual Network to an App
    2) Add an EXISTING Virtual Network to an App
    3) Remove a Virtual Network from an App

La parte restante di questa sezione illustra ognuna delle tre opzioni.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Creare una rete virtuale di Azure Resource Manager ed eseguire l'integrazione
Per creare una nuova rete virtuale che usa il modello di distribuzione Azure Resource Manager e integrarla con l'app, selezionare l'opzione **1) Add a NEW Virtual Network to an App**. Verrà richiesto il nome della rete virtuale. In questo caso è stato usato il nome v2pshell, come risulta nelle impostazioni seguenti.

Lo script restituisce i dettagli della rete virtuale che si sta creando. Se si desidera, è possibile modificare qualsiasi valori. In questo esempio è stata creata una rete virtuale con le impostazioni seguenti:

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

Per modificare uno dei valori, digitare **Y** e apportare le modifiche. Dopo aver configurato tutte le impostazioni della rete virtuale, digitare **N** o premere INVIO quando viene richiesto di modificare le impostazioni. Da quel punto in poi, lo script indicherà alcune delle attività in esecuzione fino alla creazione del gateway di rete virtuale. Questo passaggio può richiedere fino a un'ora. In questa fase non è disponibile un indicatore di stato, ma verrà comunicata l'avvenuta creazione del gateway.

Al termine dell'operazione, lo script visualizza il messaggio **Finished**. A questo punto è disponibile una rete virtuale di Azure Resource Manager con il nome e le impostazioni selezionate. La nuova rete virtuale è anche integrata con l'app.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Integrare l'app con una rete di Azure Resource Manager preesistente
Quando si esegue l'integrazione specificando una rete virtuale di Azure Resource Manager esistente priva di gateway e connettività da punto a sito, questi vengono impostati dallo script. Se la rete virtuale ha già queste impostazioni, lo script passa direttamente all'integrazione dell'app. Per avviare il processo è sufficiente selezionare l'opzione **2) Add an EXISTING Virtual Network to an App**.

Questa opzione funziona solo se la rete virtuale di Azure Resource Manager preesistente è nella stessa sottoscrizione dell'app. Dopo aver selezionato l'opzione, viene visualizzato un elenco delle reti virtuali di Azure Resource Manager.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Choose an option: 5

Selezionare la rete virtuale con cui si vuole eseguire l'integrazione. Se è già disponibile un gateway con la connettività da punto a sito abilitata, lo script procede direttamente all'integrazione dell'app con la rete virtuale. Se non è disponibile un gateway, è necessario specificare la subnet del gateway. La subnet del gateway deve trovarsi nello spazio di indirizzi della rete virtuale e non può essere in un'altra subnet. Se si esegue questo passaggio con una rete virtuale priva di gateway, il risultato sarà simile al seguente:

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

In questo esempio è stato creato un gateway di rete virtuale con le impostazioni seguenti:

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
    Creating App association to VNET

Le impostazioni possono essere modificate in base alle preferenze. In caso contrario, premere INVIO. Lo script creerà il gateway e collegherà l'app alla rete virtuale. Tenere presente che il tempo necessario per la creazione del gateway è un'ora. Al termine di tutte le operazioni, lo script visualizza il messaggio **Finished**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Disconnettere l'app da una rete virtuale di Azure Resource Manager
La disconnessione dell'app dalla rete virtuale non provoca l'arresto del gateway e non disabilita la connettività da punto a sito, perché potrebbero essere usati da altri processi. La rete virtuale viene disconnessa unicamente dall'app specificata e da nessun'altra app. Per eseguire questa operazione, selezionare l'opzione **3) Remove a Virtual Network from an App**. Il risultato sarà simile al seguente:

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Anche se lo script usa il termine "delete", non elimina effettivamente la rete virtuale. Rimuove solo l'integrazione. Dopo la conferma dell'utente, il comando viene elaborato molto rapidamente e al termine restituisce **True** .

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com



<!--HONumber=Nov16_HO3-->


