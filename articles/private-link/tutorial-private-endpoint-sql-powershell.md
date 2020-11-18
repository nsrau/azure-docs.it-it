---
title: 'Esercitazione: Connettersi a un server SQL di Azure con un endpoint privato di Azure - PowerShell'
description: Questa esercitazione illustra come creare un server SQL di Azure con un endpoint privato usando Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.openlocfilehash: 36b952131c2050230de89064adc586c5a2851b65
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147628"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>Esercitazione: Connettersi a un server SQL di Azure con un endpoint privato di Azure - Azure PowerShell

Un endpoint privato di Azure è il blocco costitutivo fondamentale del collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con le risorse collegamento privato.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale e un host bastion.
> * Creare una macchina virtuale.
> * Creare un server di Azure SQL e un endpoint privato.
> * Testare la connettività con l'endpoint privato del server SQL.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
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
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>Creare un server SQL di Azure

In questa sezione verranno creati un server e un database SQL usando:

* [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [New-AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

Creare il server e il database SQL. Sostituire **\<sql-server-name>** con il nome del server univoco:

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà l'endpoint privato e la connessione usando:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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

3. Selezionare **CreateSQLEndpointTutorial-rg**.

4. Selezionare **myVM**.

5. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

6. Selezionare il pulsante blu **Usa Bastion**.

7. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

8. Aprire Windows PowerShell nel server dopo la connessione.

9. Immettere `nslookup <sqlserver-name>.database.windows.net`. Sostituire **\<sqlserver-name>** con il nome del server SQL creato nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Per il nome del server SQL viene restituito l'indirizzo IP privato **10.0.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.


10. Installare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true) in **myVM**.

11. Aprire **SQL Server Management Studio**.

12. In **Connetti a server** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Tipo di server | Selezionare **Motore di database**.|
    | Nome server | Immettere **\<sql-server-name>.database.windows.net** |
    | Autenticazione | Selezionare **Autenticazione di SQL Server**. |
    | Nome utente | Immettere il nome utente specificato durante la creazione del server |
    | Password | Immettere la password specificata durante la creazione del server |
    | Memorizza password | Selezionare **Sì**. |

13. Selezionare **Connetti**.

14. Esplorare i database dal menu a sinistra.

15. (Facoltativo) Creare o eseguire query sulle informazioni di **mysqldatabase**.

16. Chiudere la connessione bastion a **myVM**. 

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non si ha più bisogno dell'endpoint privato, del server SQL e della macchina virtuale, rimuovere il gruppo di risorse e tutte le risorse che contiene: 

1. Immettere **CreateSQLEndpointTutorial-rg** nella casella **Cerca** nella parte superiore del portale e selezionare **CreateSQLEndpointTutorial-rg** nei risultati della ricerca. 

2. Selezionare **Elimina gruppo di risorse**. 

3. Immettere **CreateSQLEndpointTutorial-rg** per **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati i componenti seguenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un server SQL con un endpoint privato.

La macchina virtuale è stata usata per testare la connettività in modo sicuro al server SQL tramite l'endpoint privato.

Informazioni su come creare un servizio Collegamento privato:
> [!div class="nextstepaction"]
> [Creare un servizio Collegamento privato](create-private-link-service-portal.md)
