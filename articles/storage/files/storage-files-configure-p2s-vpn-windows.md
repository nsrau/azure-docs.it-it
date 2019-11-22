---
title: Configurare una VPN da punto a sito in Windows per l'uso con File di Azure | Microsoft Docs
description: Come configurare una VPN da punto a sito in Windows per l'uso con File di Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 527ab905997d18433d1dba5c16ee67c8146f5afa
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126457"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Configurare una VPN da punto a sito in Windows per l'uso con File di Azure
È possibile usare una connessione VPN da punto a sito per montare le condivisioni file di Azure su SMB dall'esterno di Azure, senza aprire la porta 445. Una connessione VPN da punto a sito è una connessione VPN tra Azure e un singolo client. Per usare una connessione VPN da punto a sito con File di Azure, è necessario configurarla per ogni client da connettere. Se è necessario connettere molti client alle condivisioni file di Azure dalla rete locale, è possibile usare una connessione VPN da sito a sito invece di una da punto a sito per ogni client. Per altre informazioni, vedere [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).

Per una descrizione completa delle opzioni di rete disponibili per File di Azure, è consigliabile leggere l'articolo [Considerazioni sulla rete per l'accesso diretto alle condivisioni file di Azure](storage-files-networking-overview.md) prima di procedere con questo articolo.

Questo articolo illustra i passaggi per configurare una VPN da punto a sito in Windows (client Windows e Windows Server) per montare le condivisioni file di Azure direttamente in locale. Se si vuole instradare il traffico di Sincronizzazione file di Azure tramite una VPN, vedere come [configurare le impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Prerequisiti
- La versione più recente del modulo Azure PowerShell. Per altre informazioni su come installare Azure PowerShell, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) e selezionare il sistema operativo in uso. Se si preferisce, è anche possibile usare l'interfaccia della riga di comando di Azure in Windows, ma le istruzioni seguenti vengono presentate per Azure PowerShell.

- Il modulo DNS privato di Azure PowerShell. Questo modulo non viene attualmente distribuito con il modulo Azure PowerShell, quindi è possibile installarlo con il metodo seguente:
    ```PowerShell
    if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
        Install-Module -Name Az.PrivateDns -AllowClobber -AllowPrerelease
    } else {
        Install-Module -Name Az.PrivateDns -RequiredVersion "0.1.3"
    }

    Import-Module -Name Az.PrivateDns
    ```  

- Una condivisione file di Azure che si vuole montare in locale. Con la VPN da punto a sito, è possibile usare una condivisione file di Azure [standard](storage-how-to-create-file-share.md) o [Premium](storage-how-to-create-premium-fileshare.md).

## <a name="deploy-a-virtual-network"></a>Distribuire una rete virtuale
Per accedere alla condivisione file di Azure e ad altre risorse di Azure dall'ambiente locale tramite una VPN da punto a sito, è necessario creare una rete virtuale. La connessione VPN da punto a sito che verrà creata automaticamente è un bridge tra il computer Windows locale e questa rete virtuale di Azure.

Lo script di PowerShell seguente creerà una rete virtuale di Azure con tre subnet: una per l'endpoint di servizio dell'account di archiviazione, una per l'endpoint privato dell'account di archiviazione (necessaria per accedere all'account di archiviazione in locale senza creare un routing personalizzato per l'IP pubblico dell'account di archiviazione, che potrebbe cambiare), e una per il gateway di rete virtuale che fornisce il servizio VPN. 

Ricordarsi di sostituire `<region>`, `<resource-group>` e `<desired-vnet-name>` con i valori appropriati per l'ambiente.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Limitare l'account di archiviazione alla rete virtuale
Per impostazione predefinita, quando si crea un account di archiviazione, è possibile accedervi da qualsiasi parte del mondo, purché si abbiano i mezzi per autenticare la richiesta, ad esempio con l'identità di Active Directory o con la chiave dell'account di archiviazione. Per limitare l'accesso a questo account di archiviazione alla rete virtuale appena creata, è necessario creare un set di regole di rete che consenta l'accesso all'interno della rete virtuale e neghi tutti gli altri accessi.

Per limitare l'account di archiviazione alla rete virtuale, è necessario usare un endpoint di servizio. L'endpoint di servizio è un costrutto di rete mediante il quale l'accesso all'IP pubblico/DNS pubblico è consentito solo dall'interno della rete virtuale. Poiché non è garantito che l'indirizzo IP pubblico rimanga invariato, in definitiva è preferibile usare un endpoint privato invece di un endpoint di servizio per l'account di archiviazione, ma non è possibile limitare l'account di archiviazione se non viene esposto anche un endpoint di servizio.

Ricordarsi di sostituire `<storage-account-name>` con l'account di archiviazione a cui si vuole accedere.

```PowerShell
$storageAccountName = "<storage-account-name>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$networkRule = Add-AzStorageAccountNetworkRule `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -VirtualNetworkResourceId $serviceEndpointSubnet.Id

Update-AzStorageAccountNetworkRuleSet `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Bypass AzureServices `
    -DefaultAction Deny `
    -VirtualNetworkRule $networkRule | Out-Null
``` 

## <a name="create-a-private-endpoint-preview"></a>Creare un endpoint privato (anteprima)
La creazione di un endpoint privato per l'account di archiviazione fornisce all'account di archiviazione di un indirizzo IP incluso nello spazio degli indirizzi IP della rete virtuale. Quando si monta la condivisione file di Azure dall'ambiente locale usando questo indirizzo IP privato, le regole di routing definite automaticamente dall'installazione della VPN instraderanno la richiesta di montaggio all'account di archiviazione tramite la VPN. 

```PowerShell
$internalVnet = Get-AzResource `
    -ResourceId $virtualNetwork.Id `
    -ApiVersion "2019-04-01"

$internalVnet.Properties.subnets[1].properties.privateEndpointNetworkPolicies = "Disabled"
$internalVnet | Set-AzResource -Force | Out-Null

$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
    -Name "myConnection" `
    -PrivateLinkServiceId $storageAccount.Id `
    -GroupId "file"

$privateEndpoint = New-AzPrivateEndpoint `
    -ResourceGroupName $resourceGroupName `
    -Name "$storageAccountName-privateEndpoint" `
    -Location $region `
    -Subnet $privateEndpointSubnet `
    -PrivateLinkServiceConnection $privateEndpointConnection

$zone = Get-AzPrivateDnsZone -ResourceGroupName $resourceGroupName
if ($null -eq $zone) {
    $zone = New-AzPrivateDnsZone `
        -ResourceGroupName $resourceGroupName `
        -Name "privatelink.file.core.windows.net"
} else {
    $zone = $zone[0]
}

$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName $resourceGroupName `
    -ZoneName $zone.Name `
    -Name ($virtualNetwork.Name + "-link") `
    -VirtualNetworkId $virtualNetwork.Id

$internalNic = Get-AzResource `
    -ResourceId $privateEndpoint.NetworkInterfaces[0].Id `
    -ApiVersion "2019-04-01"

foreach($ipconfig in $internalNic.Properties.ipConfigurations) {
    foreach($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.', 2)[0]
        $dnsZone = $fqdn.split('.', 2)[1]
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $resourceGroupName ` 
            -Name $recordName `
            -RecordType A `
            -ZoneName $zone.Name `
            -Ttl 600 `
            -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
                -IPv4Address $ipconfig.properties.privateIPAddress) | Out-Null
    }
}
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Creare il certificato radice per l'autenticazione della VPN
Per consentire l'autenticazione delle connessioni VPN da computer Windows locali per l'accesso alla rete virtuale, è necessario creare due certificati: un certificato radice, che verrà fornito al gateway della macchina virtuale, e un certificato client, che verrà firmato con il certificato radice. Lo script di PowerShell seguente crea il certificato radice. Il certificato client verrà creato dopo che verrà creato il gateway di rete virtuale di Azure con le informazioni del gateway. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Distribuire il gateway di rete virtuale
Il gateway di rete virtuale di Azure è il servizio a cui si connetteranno i computer Windows locali. La distribuzione di questo servizio richiede due componenti di base: un indirizzo IP pubblico, che identificherà il gateway per i client ovunque si trovino nel mondo, e un certificato radice creato in precedenza, che verrà usato per autenticare i client.

Ricordarsi di sostituire `<desired-vpn-name-here>` con il nome che si vuole usare per queste risorse.

> [!Note]  
> La distribuzione del gateway di rete virtuale di Azure può richiedere fino a 45 minuti. Durante la distribuzione di questa risorsa, questo script di PowerShell verrà bloccato per consentire il completamento dell'operazione. Si tratta di un comportamento previsto.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName ` 
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Creare il certificato client
Il certificato client viene creato con l'URI del gateway di rete virtuale. Questo certificato è firmato con il certificato radice creato in precedenza.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Configurare il client VPN
Il gateway di rete virtuale di Azure creerà un pacchetto scaricabile con i file di configurazione necessari per inizializzare la connessione VPN nel computer Windows locale. La connessione VPN viene configurata usando la funzionalità [VPN Always On](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) di Windows 10/Windows Server 2016 e versioni successive. Questo pacchetto contiene anche pacchetti eseguibili che consentiranno di configurare il client VPN Windows legacy, se necessario. Questa guida usa la funzionalità VPN Always On invece del client VPN Windows legacy perché il client VPN Always On consente agli utenti finali di connettersi e disconnettersi dalla VPN di Azure senza avere autorizzazioni di amministratore per il loro computer. 

Lo script seguente installerà il certificato client necessario per l'autenticazione con il gateway di rete virtuale, quindi scaricherà e installerà il pacchetto VPN. Ricordarsi di sostituire `<computer1>` e `<computer2>` con i computer desiderati. È possibile eseguire questo script in tutti i computer necessari aggiungendo più sessioni di PowerShell alla matrice `$sessions`. L'account utente deve essere un amministratore in ognuno di questi computer. Se uno di questi computer è il computer locale in cui viene eseguito lo script, è necessario eseguire lo script da una sessione di PowerShell con privilegi elevati. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer ` 
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Montare la condivisione file di Azure
Dopo aver configurato la VPN da punto a sito, è possibile usarla per montare la condivisione file di Azure nei computer configurati tramite PowerShell. L'esempio seguente monta la condivisione, elenca il contenuto della relativa directory radice per dimostrare che è stata effettivamente montata, quindi la smonta. Purtroppo, non è possibile montare la condivisione in modo persistente tramite la comunicazione remota di PowerShell. Per informazioni sul montaggio persistente, vedere [Usare una condivisione file di Azure con Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Vedere anche
- [Considerazioni sulla rete per l'accesso diretto alle condivisioni file di Azure](storage-files-networking-overview.md)
- [Configurare una VPN da punto a sito in Linux per l'uso con File di Azure](storage-files-configure-p2s-vpn-linux.md)
- [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md)