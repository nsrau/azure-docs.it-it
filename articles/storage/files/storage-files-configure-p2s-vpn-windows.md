---
title: Configurare una VPN da punto a sito in Windows per l'uso con File di Azure | Microsoft Docs
description: Come configurare una VPN da punto a sito in Windows per l'uso con File di Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da49d1c94584393bfef066d61c1caf360b249c3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515314"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Configurare una VPN da punto a sito in Windows per l'uso con File di Azure
È possibile usare una connessione VPN da punto a sito per montare le condivisioni file di Azure su SMB dall'esterno di Azure, senza aprire la porta 445. Una connessione VPN da punto a sito è una connessione VPN tra Azure e un singolo client. Per usare una connessione VPN da punto a sito con File di Azure, è necessario configurarla per ogni client da connettere. Se è necessario connettere molti client alle condivisioni file di Azure dalla rete locale, è possibile usare una connessione VPN da sito a sito invece di una da punto a sito per ogni client. Per altre informazioni, vedere [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).

Per una descrizione completa delle opzioni di rete disponibili per File di Azure, è consigliabile leggere l'articolo [Considerazioni sulla rete per l'accesso diretto alle condivisioni file di Azure](storage-files-networking-overview.md) prima di procedere con questo articolo.

Questo articolo illustra i passaggi per configurare una VPN da punto a sito in Windows (client Windows e Windows Server) per montare le condivisioni file di Azure direttamente in locale. Se si vuole instradare il traffico di Sincronizzazione file di Azure tramite una VPN, vedere come [configurare le impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Prerequisiti
- La versione più recente del modulo Azure PowerShell. Per altre informazioni su come installare Azure PowerShell, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) e selezionare il sistema operativo in uso. Se si preferisce, è anche possibile usare l'interfaccia della riga di comando di Azure in Windows, ma le istruzioni seguenti vengono presentate per Azure PowerShell.

- Una condivisione file di Azure che si vuole montare in locale. Le condivisioni file di Azure vengono distribuite all'interno degli account di archiviazione, ovvero costrutti di gestione che rappresentano un pool di archiviazione condiviso in cui è possibile distribuire più condivisione file, oltra ad altre risorse come contenitori BLOB o code. Per altre informazioni su come distribuire condivisioni file di Azure e account di archiviazione, vedere [Creare una condivisione file di Azure](storage-how-to-create-file-share.md).

- Un endpoint privato per l'account di archiviazione contenente l'archiviazione file di Azure da montare in locale. Per altre informazioni su come creare un endpoint privato, vedere [Configurazione degli endpoint di rete di File di Azure](storage-files-networking-endpoints.md?tabs=azure-powershell). 

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
            $virtualNetworkName `
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
