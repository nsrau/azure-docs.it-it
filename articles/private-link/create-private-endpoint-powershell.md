---
title: Avvio rapido - Creare un endpoint privato di Azure con Azure PowerShell
description: Questa guida di avvio rapido illustra come creare un endpoint privato con Azure PowerShell.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: 7add424c23e430a8ca5059d45acd037fff8836ad
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368662"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Avvio rapido: Creare un endpoint privato con Azure PowerShell

Introduzione al collegamento privato di Azure, che consente di connettersi in modo sicuro a un'app Web di Azure tramite un endpoint privato.

In questa guida di avvio rapido si creerà un endpoint privato per un'app Web di Azure e si distribuirà una macchina virtuale per testare la connessione privata.  

È possibile creare endpoint privati per diversi tipi di servizi di Azure, ad esempio Azure SQL e Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un'app Web di Azure con un piano di servizio app **PremiumV2-tier** o superiore distribuita nella sottoscrizione di Azure.  
    * Per altre informazioni e un esempio, vedere [Avvio rapido: Creare un'app Web ASP.NET Core in Azure](../app-service/quickstart-dotnetcore.md). 
    * Per un'esercitazione dettagliata sulla creazione di un'app Web e di un endpoint, vedere [Esercitazione: Connettersi a un'app Web con un endpoint privato di Azure](tutorial-private-endpoint-webapp-portal.md).

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Creare una rete virtuale e un host bastion

In questa sezione si creeranno una rete virtuale, una subnet e un host bastion. 

L'host bastion verrà usato per connettersi in modo sicuro alla macchina virtuale per testare l'endpoint privato.

Creare una rete virtuale e un host bastion con:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

## <a name="create-test-virtual-machine"></a>Creare una macchina virtuale di test

In questa sezione si creerà una macchina virtuale che verrà usata per testare l'endpoint privato.

Creare la macchina virtuale con:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà l'endpoint privato e la connessione usando:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato

In questa sezione si creerà e si configurerà la zona DNS privata usando:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi al server SQL tramite l'endpoint privato.

1. Accedere al [portale di Azure](https://portal.azure.com) 
 
2. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

3. Selezionare **CreatePrivateEndpointQS-rg**.

4. Selezionare **myVM**.

5. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

6. Selezionare il pulsante blu **Usa Bastion**.

7. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

8. Aprire Windows PowerShell nel server dopo la connessione.

9. Immettere `nslookup <your-webapp-name>.azurewebsites.net`. Sostituire **\<your-webapp-name>** con il nome dell'app Web creata nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Per il nome dell'app Web viene restituito l'indirizzo IP privato **10.0.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.

10. Nella connessione bastion a **myVM** aprire Internet Explorer.

11. Immettere l'URL dell'app Web **https://\<your-webapp-name>.azurewebsites.net**.

12. Se l'applicazione non è stata distribuita, si riceverà la pagina dell'app Web predefinita:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Pagina dell'app Web predefinita." border="true":::

13. Chiudere la connessione a **myVM**.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non si ha più bisogno dell'endpoint privato e della macchina virtuale, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse che contiene:

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un endpoint privato per un'app Web di Azure.

La macchina virtuale è stata usata per testare la connettività in modo sicuro all'app Web tramite l'endpoint privato.

Per altre informazioni sui servizi che supportano un endpoint privato, vedere:
> [!div class="nextstepaction"]
> [Disponibilità del collegamento privato](private-link-overview.md#availability)
