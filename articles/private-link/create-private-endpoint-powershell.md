---
title: Create an Azure Private Endpoint using Azure PowerShell| Microsoft Docs
description: Learn about Azure Private Link
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 83f1cbc3f8da61370c90744be3f0a7b230e016c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229394"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Create a private endpoint using Azure PowerShell
Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. 

Questa guida di avvio rapido illustrerà come creare una VM in una rete virtuale di Azure e un server di database SQL con un endpoint privato di Azure usando Azure PowerShell. Si potrà quindi accedere in modo sicuro al server di database SQL dalla VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Before you can create your resources, you must create a resource group that hosts the Virtual Network and the private endpoint with [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). The following example creates a resource group named *myResourceGroup* in the *WestUS* location:

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
In this section, you create a virtual network and a subnet. Next, you associate the subnet to your Virtual Network.

### <a name="create-a-virtual-network"></a>Creare una rete virtuale

Create a Virtual network for your private endpoint with [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). The following example creates a Virtual Network named *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Add a Subnet

Azure deploys resources to a subnet within a Virtual Network, so you need to create a subnet. Create a subnet configuration named *mySubnet* with [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). The following example creates a subnet named *mySubnet* with the private endpoint network policy flag set to **Disabled**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associate the Subnet to the Virtual Network

You can write the subnet configuration to the Virtual Network with [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Questo comando crea la subnet:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Create a VM in the Virtual Network with [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue il comandi seguente, viene chiesto di immettere le credenziali. Immettere un nome utente e una password per la macchina virtuale:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

L'opzione `-AsJob` consente di creare la macchina virtuale in background. È possibile continuare con il passaggio successivo.

Quando Azure inizia a creare la macchina virtuale in background, il risultato sarà qualcosa di simile a quanto segue:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Create a SQL Database Server 

Create a  SQL Database Server by using the New-AzSqlServer command. Remember that the name of your SQL Database server must be unique across Azure, so replace the placeholder value in brackets with your own unique value:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

Private Endpoint for the SQL Database Server in your Virtual Network with [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configure the Private DNS Zone 
Create a private DNS zone for SQL Database Server domain and create an association link with the virtual network: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Usare [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. This example returns the public IP address of the *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Aprire un prompt dei comandi nel computer locale. Run the mstsc command. Sostituire <publicIpAddress> con l'indirizzo IP pubblico ottenuto nell'ultimo passaggio: 


> [!NOTE]
> Se questi comandi sono in esecuzione da un prompt di PowerShell nel computer locale, con il modulo Az PowerShell versione 1.0 o successiva, è possibile continuare in tale interfaccia.
```
mstsc /v:<publicIpAddress>
```

1. Quando richiesto, selezionare **Connetti**. 
2. Immettere il nome utente e la password specificati al momento della creazione della VM.
  > [!NOTE]
  > You may need to select More choices > Use a different account, to specify the credentials you entered when you created the VM. 
  
3. Selezionare **OK**. 
4. Si potrebbe ricevere un avviso del certificato. Se sì, selezionare **Sì** oppure **Continua**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privately from the VM

1. In the Remote Desktop of myVM, open PowerShell.
2. Immettere `nslookup myserver.database.windows.net`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information: Setting Value Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## <a name="clean-up-resources"></a>Pulire le risorse 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi
- Learn more about [Azure Private Link](private-link-overview.md)
