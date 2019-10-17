---
title: Creare un endpoint privato di Azure usando Azure PowerShell | Microsoft Docs
description: Informazioni sul collegamento privato di Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 43b8dfd571537aaaf6753d6b762ab84cfe4cfd0d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376160"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Creare un endpoint privato usando Azure PowerShell
Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. 

Questa guida di avvio rapido illustrerà come creare una VM in una rete virtuale di Azure e un server di database SQL con un endpoint privato di Azure usando Azure PowerShell. Si potrà quindi accedere in modo sicuro al server di database SQL dalla VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare le risorse, è necessario creare un gruppo di risorse che ospiti la rete virtuale e l'endpoint privato con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nella posizione *westus* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
In questa sezione viene creata una rete virtuale e una subnet. Successivamente, associare la subnet alla rete virtuale.

### <a name="create-a-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale per l'endpoint privato con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Aggiungere una subnet

Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, pertanto è necessario creare una subnet. Creare una *configurazione di subnet denominata subnet* con [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Nell'esempio seguente viene creata una subnet *denominata subnet* con il flag di criteri di rete dell'endpoint privato impostato su **disabilitato**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associare la subnet alla rete virtuale

È possibile scrivere la configurazione della subnet nella rete virtuale con [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Questo comando crea la subnet:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM nella rete virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue il comandi seguente, viene chiesto di immettere le credenziali. Immettere un nome utente e una password per la macchina virtuale:

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

## <a name="create-a-sql-database-server"></a>Creare un server di database SQL 

Per creare un server di database SQL, usare il comando New-AzSqlServer. Tenere presente che il nome del server di database SQL deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi quadre con il proprio valore univoco:

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

Endpoint privato per il server di database SQL nella rete virtuale con [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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

## <a name="configure-the-private-dns-zone"></a>Configurare la zona di DNS privato 
Creare una zona DNS privata per il dominio del server di database SQL e creare un collegamento di associazione con la rete virtuale: 

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

Usare [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) per per restituire l'indirizzo IP pubblico di una macchina virtuale. Questo esempio restituisce l'indirizzo IP pubblico della macchina virtuale *myVM* :

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Aprire un prompt dei comandi nel computer locale. Eseguire il comando mstsc. Sostituire @ no__t-0 @ no__t-1With l'indirizzo IP pubblico restituito dall'ultimo passaggio: 


> [!NOTE]
> Se questi comandi sono in esecuzione da un prompt di PowerShell nel computer locale, con il modulo Az PowerShell versione 1.0 o successiva, è possibile continuare in tale interfaccia.
```
mstsc /v:<publicIpAddress>
```

1. Quando richiesto, selezionare **Connetti**. 
2. Immettere il nome utente e la password specificati al momento della creazione della VM.
  > [!NOTE]
  > Potrebbe essere necessario selezionare altre opzioni > usare un account diverso per specificare le credenziali immesse durante la creazione della macchina virtuale. 
  
3. Selezionare **OK**. 
4. Si potrebbe ricevere un avviso del certificato. Se sì, selezionare **Sì** oppure **Continua**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Accedere al server di database SQL privatamente dalla macchina virtuale

1. Nel Desktop remoto di myVM aprire PowerShell.
2. Immettere  `nslookup myserver.database.windows.net`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Installa SQL Server Management Studio
4. In Connetti al server immettere o selezionare le informazioni seguenti: impostazione tipo di server valore selezionare motore di database.
      Nome server selezionare myserver.database.windows.net nomeutente immettere un nome utente specificato durante la creazione.
      Password immettere una password specificata durante la creazione.
      Ricorda password selezionare Sì.
5. Selezionare Connetti.
6. Esplorare i database dal menu a sinistra. 
7. Facoltativamente Creare o eseguire query sulle informazioni da database
8. Chiudere la connessione Desktop remoto a *myVM*. 

## <a name="clean-up-resources"></a>Pulire le risorse 
Al termine dell'uso dell'endpoint privato, del server di database SQL e della VM, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse disponibili:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi
- Scopri di più sul [collegamento privato di Azure](private-link-overview.md)
