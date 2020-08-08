---
title: 'Esercitazione: aggiungere Istanza gestita SQL a un gruppo di failover'
titleSuffix: Azure SQL Managed Instance
description: In questa esercitazione si apprenderà come creare un gruppo di failover tra una Istanza gestita SQL di Azure primaria e secondaria.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: 47f33d8b1a7792487491cbe7f2ddb5c7f5b087af
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002989"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Esercitazione: aggiungere Istanza gestita SQL a un gruppo di failover
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Aggiungere istanze gestite di Istanza gestita SQL di Azure a un gruppo di failover. In questo articolo verrà spiegato come:

> [!div class="checklist"]
> - Creare un'istanza gestita primaria.
> - Creare un'istanza gestita secondaria come parte di un [gruppo di failover](../database/auto-failover-group-overview.md). 
> - Failover di test.

  > [!NOTE]
  > - Quando si esegue questa esercitazione, assicurarsi di configurare le risorse con i [prerequisiti per la configurazione di gruppi di failover per SQL istanza gestita](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - La creazione di un'istanza gestita può richiedere una quantità di tempo significativa. Di conseguenza, per il completamento di questa esercitazione potrebbero essere necessarie diverse ore. Per altre informazioni sui tempi di provisioning, vedere [operazioni di gestione di SQL istanza gestita](sql-managed-instance-paas-overview.md#management-operations). 
  > - Le istanze gestite che fanno parte di un gruppo di failover richiedono [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) o due gateway VPN connessi. Il peering di reti virtuali globale non è supportato. Questa esercitazione illustra la procedura per creare e connettere i gateway VPN. Se ExpressRoute è già configurato, ignorare questa procedura. 


## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per completare l'esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="1---create-a-resource-group-and-primary-managed-instance"></a>1-creare un gruppo di risorse e un'istanza gestita primaria

In questo passaggio si creeranno il gruppo di risorse e l'istanza gestita primaria per il gruppo di failover usando il portale di Azure o PowerShell. 


# <a name="portal"></a>[Portale](#tab/azure-portal) 

Creare il gruppo di risorse e l'istanza gestita primaria usando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare `Azure SQL` nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando **Mostra dettagli** nel riquadro **Database**.
1. Selezionare **Crea** nel riquadro **istanze gestite di SQL** . 

    ![Selezionare Istanza gestita SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Nella pagina **crea istanza gestita di Azure SQL** , nella scheda **nozioni di base** :
    1. In **Dettagli progetto**selezionare la **sottoscrizione** dall'elenco a discesa, quindi scegliere di creare un **nuovo** gruppo di risorse. Digitare un nome per il gruppo di risorse, ad esempio `myResourceGroup` . 
    1. In **SQL istanza gestita Details**specificare il nome dell'istanza gestita e l'area in cui si vuole distribuire l'istanza gestita. Lasciare le risorse di **calcolo e archiviazione** con i valori predefiniti. 
    1. In **account amministratore**specificare un account di accesso amministratore, ad esempio `azureuser` , e una password amministratore complessa. 

    ![Crea istanza gestita primaria](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Lasciare i valori predefiniti per le altre impostazioni e selezionare **Verifica + crea** per esaminare le impostazioni di SQL istanza gestita. 
1. Selezionare **Crea** per creare l'istanza gestita primaria. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di risorse e l'istanza gestita primaria usando PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un gruppo di risorse di Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet a una rete virtuale. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ottiene una rete virtuale in un gruppo di risorse. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene una subnet in una rete virtuale. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un gruppo di sicurezza di rete. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Crea una tabella di route. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aggiorna una configurazione di subnet per una rete virtuale.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aggiorna una rete virtuale.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ottiene un gruppo di sicurezza di rete. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Aggiunge la configurazione di una regola di sicurezza di rete a un gruppo di sicurezza di rete. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aggiorna un gruppo di sicurezza di rete.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Aggiunge una route a una tabella di route. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aggiorna una tabella di route.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita.  |

---

## <a name="2---create-secondary-virtual-network"></a>2-creare una rete virtuale secondaria

Se si usa il portale di Azure per creare un'istanza gestita, è necessario creare la rete virtuale separatamente perché è necessario che la subnet dell'istanza gestita primaria e secondaria non disponga di intervalli sovrapposti. Se si usa PowerShell per configurare l'istanza gestita, procedere con il passaggio 3. 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Per verificare l'intervallo di subnet della rete virtuale primaria, attenersi alla procedura seguente:

1. Nella [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la rete virtuale per l'istanza primaria.  
2. Selezionare **subnet** in **Impostazioni** e prendere nota dell' **intervallo di indirizzi**. L'intervallo di indirizzi della subnet della rete virtuale per l'istanza gestita secondaria non può sovrapporsi a questo. 


   ![Subnet primaria](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Per creare una rete virtuale, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa** e cercare *rete virtuale*. 
1. Selezionare l'opzione **rete virtuale** pubblicata da Microsoft e quindi fare clic su **Crea** nella pagina successiva. 
1. Compilare i campi obbligatori per configurare la rete virtuale per l'istanza gestita secondaria, quindi selezionare **Crea**. 

   La tabella seguente mostra i valori necessari per la rete virtuale secondaria:

    | **Campo** | valore |
    | --- | --- |
    | **Nome** |  Nome della rete virtuale che deve essere utilizzato dall'istanza gestita secondaria, ad esempio `vnet-sql-mi-secondary` . |
    | **Spazio indirizzi** | Spazio degli indirizzi per la rete virtuale, ad esempio `10.128.0.0/16` . | 
    | **Sottoscrizione** | Sottoscrizione in cui risiedono l'istanza gestita primaria e il gruppo di risorse. |
    | **Area** | Il percorso in cui si distribuirà l'istanza gestita secondaria. |
    | **Subnet** | Nome della subnet. `default`viene fornito per impostazione predefinita. |
    | **Intervallo di indirizzi**| Intervallo di indirizzi per la subnet. Questo deve essere diverso dall'intervallo di indirizzi della subnet usato dalla rete virtuale dell'istanza gestita primaria, ad esempio `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Valori della rete virtuale secondaria](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Questo passaggio è necessario solo se si sta usando il portale di Azure per distribuire SQL Istanza gestita. Procedere al passaggio 3 Se si usa PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3-creare un'istanza gestita secondaria
In questo passaggio verrà creata un'istanza gestita secondaria nel portale di Azure, che configurerà anche la rete tra le due istanze gestite. 

La seconda istanza gestita deve:
- Essere vuoto. 
- Avere una subnet e un intervallo IP diversi rispetto all'istanza gestita primaria. 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Creare l'istanza gestita secondaria usando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare `Azure SQL` nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando **Mostra dettagli** nel riquadro **Database**.
1. Selezionare **Crea** nel riquadro **istanze gestite di SQL** . 

    ![Selezionare Istanza gestita SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Nella scheda **nozioni di base** della pagina **Create Azure SQL istanza gestita** compilare i campi obbligatori per configurare l'istanza gestita secondaria. 

   Nella tabella seguente sono illustrati i valori necessari per l'istanza gestita secondaria:
 
    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Gruppo di risorse**| Il gruppo di risorse in cui si trova l'istanza gestita primaria. |
    | **Nome del Istanza gestita SQL** | Nome della nuova istanza gestita secondaria, ad esempio `sql-mi-secondary` .  | 
    | **Area**| Percorso dell'istanza gestita secondaria.  |
    | **Account di accesso amministratore di SQL Istanza gestita** | Account di accesso che si desidera utilizzare per la nuova istanza gestita secondaria, ad esempio `azureuser` . |
    | **Password** | Password complessa che verrà utilizzata dall'account di accesso amministratore per la nuova istanza gestita secondaria.  |
    | &nbsp; | &nbsp; |

1. **Nella scheda rete** , per la **rete virtuale**, selezionare la rete virtuale creata per l'istanza gestita secondaria dall'elenco a discesa.

   ![Rete MI secondaria](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Nella scheda **Impostazioni aggiuntive** , per la **replica geografica**, scegliere **Sì** per _usare come database secondario di failover_. Selezionare l'istanza gestita primaria dall'elenco a discesa. 
    
   Assicurarsi che le regole di confronto e il fuso orario corrispondano a quelli dell'istanza gestita primaria. Per l'istanza gestita primaria creata in questa esercitazione sono state usate le `SQL_Latin1_General_CP1_CI_AS` regole di confronto predefinite e il `(UTC) Coordinated Universal Time` fuso orario. 

   ![Rete istanza gestita secondaria](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selezionare **Verifica + crea** per esaminare le impostazioni per l'istanza gestita secondaria. 
1. Selezionare **Crea** per creare un'istanza gestita secondaria. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare l'istanza gestita secondaria usando PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un gruppo di risorse di Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet a una rete virtuale. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ottiene una rete virtuale in un gruppo di risorse. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene una subnet in una rete virtuale. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un gruppo di sicurezza di rete. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Crea una tabella di route. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aggiorna una configurazione di subnet per una rete virtuale.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aggiorna una rete virtuale.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ottiene un gruppo di sicurezza di rete. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Aggiunge la configurazione di una regola di sicurezza di rete a un gruppo di sicurezza di rete. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aggiorna un gruppo di sicurezza di rete.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Aggiunge una route a una tabella di route. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aggiorna una tabella di route.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita.  |

---

## <a name="4---create-a-primary-gateway"></a>4-creare un gateway primario 

Affinché due istanze gestite partecipino a un gruppo di failover, è necessario che ExpressRoute o un gateway sia configurato tra le reti virtuali delle due istanze gestite per consentire la comunicazione di rete. Se si sceglie di configurare [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) anziché connettere due gateway VPN, procedere con il [passaggio 7](#7---create-a-failover-group).  

Questo articolo illustra la procedura per creare i due gateway VPN e connetterli, ma è possibile passare alla creazione del gruppo di failover se è stato configurato ExpressRoute. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gateway per la rete virtuale dell'istanza gestita primaria usando il portale di Azure. 


1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la risorsa di **rete virtuale** per l'istanza gestita primaria. 
1. Selezionare **subnet** in **Impostazioni** e quindi selezionare per aggiungere una nuova **subnet del gateway**. Lasciare i valori predefiniti. 

   ![Aggiungi gateway per istanza gestita primaria](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Dopo aver creato il gateway della subnet, selezionare **Crea una risorsa** nel riquadro di spostamento a sinistra e quindi digitare `Virtual network gateway` nella casella di ricerca. Selezionare la risorsa **gateway di rete virtuale** pubblicata da **Microsoft**. 

   ![Creare un nuovo gateway di rete virtuale](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita primaria. 

   La tabella seguente mostra i valori necessari per il gateway per l'istanza gestita primaria:
 
    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Nome** | Nome del gateway di rete virtuale, ad esempio `primary-mi-gateway` . | 
    | **Area** | Area in cui si trova l'istanza gestita primaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo di VPN** | Selezionare **Basato su route**. |
    | **SKU**| Lasciare il valore predefinito di `VpnGw1` . |
    | **Posizione**| Il percorso in cui si trovano l'istanza gestita primaria e la rete virtuale primaria.   |
    | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-primary` . |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Lasciare gli altri valori predefiniti e quindi selezionare **Verifica + crea** per esaminare le impostazioni per il gateway di rete virtuale.

   ![Impostazioni del gateway primario](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Selezionare **Crea** per creare il nuovo gateway di rete virtuale. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gateway per la rete virtuale dell'istanza gestita primaria usando PowerShell. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ottiene una rete virtuale in un gruppo di risorse. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet a una rete virtuale. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aggiorna una rete virtuale.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene una subnet in una rete virtuale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configurazione IP per un gateway di rete virtuale. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea un gateway di rete virtuale. |


---


## <a name="5---create-secondary-gateway"></a>5-creare il gateway secondario 
In questo passaggio, creare il gateway per la rete virtuale dell'istanza gestita secondaria usando il portale di Azure. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Usando il portale di Azure, ripetere i passaggi della sezione precedente per creare la subnet della rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria. 

   La tabella seguente mostra i valori necessari per il gateway per l'istanza gestita secondaria:

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita secondaria. |
   | **Nome** | Nome del gateway di rete virtuale, ad esempio `secondary-mi-gateway` . | 
   | **Area** | Area in cui si trova l'istanza gestita secondaria. |
   | **Tipo di gateway** | Selezionare **VPN**. |
   | **Tipo di VPN** | Selezionare **Basato su route**. |
   | **SKU**| Lasciare il valore predefinito di `VpnGw1` . |
   | **Posizione**| Il percorso in cui si trovano l'istanza gestita secondaria e la rete virtuale secondaria.   |
   | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-secondary` . |
   | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
   | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Impostazioni del gateway secondario](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gateway per la rete virtuale dell'istanza gestita secondaria usando PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ottiene una rete virtuale in un gruppo di risorse. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet a una rete virtuale. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aggiorna una rete virtuale.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene una subnet in una rete virtuale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configurazione IP per un gateway di rete virtuale. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea un gateway di rete virtuale. |

---


## <a name="6---connect-the-gateways"></a>6-connettere i gateway
In questo passaggio viene creata una connessione bidirezionale tra i due gateway delle due reti virtuali. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Connettere i due gateway usando il portale di Azure. 


1. Selezionare **Crea una risorsa** dal [portale di Azure](https://portal.azure.com).
1. Digitare `connection` nella casella di ricerca, quindi premere INVIO per eseguire la ricerca, che consente di accedere alla risorsa di **connessione** pubblicata da Microsoft.
1. Selezionare **Crea** per creare la connessione. 
1. Nella scheda **nozioni di base** selezionare i valori seguenti e quindi fare clic su **OK**. 
    1. Selezionare `VNet-to-VNet` per il **tipo di connessione**. 
    1. Selezionare la sottoscrizione dall'elenco a discesa. 
    1. Selezionare il gruppo di risorse per SQL Istanza gestita nell'elenco a discesa. 
    1. Selezionare il percorso dell'istanza gestita primaria dall'elenco a discesa. 
1. Nella scheda **Impostazioni** selezionare o immettere i valori seguenti e quindi selezionare **OK**:
    1. Scegliere il gateway di rete primario per il **primo gateway di rete virtuale**, ad esempio `Primary-Gateway` .  
    1. Scegliere il gateway di rete secondario per il **secondo gateway di rete virtuale**, ad esempio `Secondary-Gateway` . 
    1. Selezionare la casella di controllo accanto a **Crea connettività bidirezionale**. 
    1. Lasciare il nome predefinito della connessione primaria o rinominarlo con un valore di propria scelta. 
    1. Fornire una **chiave condivisa (PSK)** per la connessione, ad esempio `mi1m2psk` . 

   ![Crea connessione gateway](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

1. Nella scheda **Riepilogo** esaminare le impostazioni per la connessione bidirezionale e quindi selezionare **OK** per creare la connessione. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Connettere i due gateway usando PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Questa parte dell'esercitazione usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |

---


## <a name="7---create-a-failover-group"></a>7-creare un gruppo di failover
In questo passaggio si creerà il gruppo di failover e si aggiungeranno entrambe le istanze gestite. 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover usando il portale di Azure. 


1. Selezionare **Azure SQL** nel menu a sinistra del [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare `Azure SQL` nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare l'istanza gestita primaria creata nella prima sezione, ad esempio `sql-mi-primary` . 
1. In **Impostazioni**passare a **gruppi di failover istanza** , quindi scegliere **Aggiungi gruppo** per aprire la pagina **gruppo di failover dell'istanza** . 

   ![Aggiungere un gruppo di failover](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Nella pagina **gruppo di failover dell'istanza** Digitare il nome del gruppo di failover, ad esempio `failovergrouptutorial` . Scegliere quindi l'istanza gestita secondaria, ad esempio `sql-mi-secondary` , dall'elenco a discesa. Selezionare **Crea** per creare il gruppo di failover. 

   ![Create failover group](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Una volta completata la distribuzione del gruppo di failover, si tornerà alla pagina del **gruppo di failover** . 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Creare il gruppo di failover usando PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Questa parte dell'esercitazione usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita di SQL di Azure.  |


---


## <a name="8---test-failover"></a>8-failover di test
In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback utilizzando il portale di Azure. 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Failover di test con il portale di Azure. 


1. Passare all'istanza gestita _secondaria_ all'interno del [portale di Azure](https://portal.azure.com) e selezionare **gruppi di failover dell'istanza** in impostazioni. 
1. Verificare quale istanza gestita è la primaria e quale istanza gestita è la replica secondaria. 
1. Selezionare **failover** e quindi fare clic su **Sì** nell'avviso relativo alle sessioni TDS disconnesse. 

   ![Eseguire il failover del gruppo di failover](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Verificare quale istanza gestita è la primaria e quale istanza gestita è la replica secondaria. Se il failover ha avuto esito positivo, le due istanze dovrebbero avere ruoli passati. 

   ![Istanze gestite con ruoli passati dopo il failover](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Passare alla nuova istanza gestita _secondaria_ e selezionare di nuovo **failover** per non restituire l'istanza primaria al ruolo primario. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Failover di test con PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Ripristinare il gruppo di failover al server primario:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita di SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita di SQL. | 

---



## <a name="clean-up-resources"></a>Pulire le risorse
Pulire le risorse eliminando prima di tutto le istanze gestite, il cluster virtuale, le eventuali risorse rimanenti e infine il gruppo di risorse. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
1. Selezionare l'istanza o le istanze gestite, quindi selezionare **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. L'esecuzione di questa procedura in background può richiedere del tempo e, finché non viene completata, non sarà possibile eliminare il *cluster virtuale* o altre risorse dipendenti. Monitorare l'eliminazione nella scheda **attività** per confermare che l'istanza gestita è stata eliminata. 
1. Una volta eliminata l'istanza gestita, eliminare il *cluster virtuale* selezionandolo nel gruppo di risorse e quindi scegliendo **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare eventuali risorse rimanenti. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare il gruppo di risorse selezionando **Elimina gruppo di risorse**, digitando il relativo nome, `myResourceGroup`, quindi selezionando **Elimina**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sarà necessario rimuovere due volte il gruppo di risorse. Rimuovendo il gruppo di risorse la prima volta, verranno rimosse le istanze gestite e i cluster virtuali, ma il messaggio di errore avrà esito negativo `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Eseguire una seconda volta il comando Remove-AzResourceGroup per rimuovere tutte le risorse residue e il gruppo di risorse.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

Questa parte dell'esercitazione usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. |

---

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un gruppo di risorse di Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet a una rete virtuale. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ottiene una rete virtuale in un gruppo di risorse. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene una subnet in una rete virtuale. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un gruppo di sicurezza di rete. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Crea una tabella di route. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aggiorna una configurazione di subnet per una rete virtuale.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aggiorna una rete virtuale.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ottiene un gruppo di sicurezza di rete. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Aggiunge la configurazione di una regola di sicurezza di rete a un gruppo di sicurezza di rete. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aggiorna un gruppo di sicurezza di rete.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Aggiunge una route a una tabella di route. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aggiorna una tabella di route.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Restituisce informazioni sull'istanza gestita di SQL di Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configurazione IP per un gateway di rete virtuale. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea un gateway di rete virtuale. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Consente di creare un nuovo gruppo di failover di SQL Istanza gestita.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita di SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita di SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. | 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Non sono disponibili script per la portale di Azure.

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un gruppo di failover tra due istanze gestite. Si è appreso come:

> [!div class="checklist"]
> - Creare un'istanza gestita primaria.
> - Creare un'istanza gestita secondaria come parte di un [gruppo di failover](../database/auto-failover-group-overview.md). 
> - Failover di test.

Passare alla Guida introduttiva successiva su come connettersi a SQL Istanza gestita e come ripristinare un database in SQL Istanza gestita: 

> [!div class="nextstepaction"]
> [Connetti a SQL istanza gestita](connect-vm-instance-configure.md) 
>  [Ripristinare un database a SQL istanza gestita](restore-sample-database-quickstart.md)


