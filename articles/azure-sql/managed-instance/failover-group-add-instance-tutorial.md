---
title: 'Esercitazione: Aggiungere Istanza gestita di SQL a un gruppo di failover'
titleSuffix: Azure SQL Managed Instance
description: In questa esercitazione viene illustrato come creare un gruppo di failover tra un'istanza gestita di SQL di Azure primaria e una secondaria.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 92d1ce51306e846e2d842bef33bb9782da14019a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593995"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Esercitazione: Aggiungere Istanza gestita di SQL a un gruppo di failover
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Aggiungere istanze gestite di Istanza gestita di SQL di Azure a un gruppo di failover. In questo articolo verrà spiegato come:

> [!div class="checklist"]
> - Creare un'istanza gestita primaria.
> - Creare un'istanza gestita secondaria come parte di un [gruppo di failover](../database/auto-failover-group-overview.md). 
> - Testare il failover.

  > [!NOTE]
  > - Quando si esegue questa esercitazione, assicurarsi di configurare le risorse con i [prerequisiti per configurare i gruppi di failover per Istanza gestita di SQL](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - La creazione di un'istanza gestita può richiedere molto tempo. Potrebbero quindi essere necessarie diverse ore per completare questa esercitazione. Per altre informazioni sui tempi di provisioning, vedere [Operazioni di gestione di istanze gestite di SQL](sql-managed-instance-paas-overview.md#management-operations). 
  > - Le istanze gestite che partecipano a un gruppo di failover richiedono [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) o due gateway VPN connessi. Il peering di reti virtuali globale non è supportato. Questa esercitazione illustra i passaggi per creare e connettere i gateway VPN. Se ExpressRoute è già configurato, ignorare questi passaggi. 


## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/), se non ne è già disponibile uno.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per completare questa esercitazione, verificare di avere gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/), se non ne è già disponibile uno.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Creare un gruppo di risorse e un'istanza gestita primaria

In questo passaggio si userà il portale di Azure o PowerShell per creare il gruppo di risorse e l'istanza gestita primaria per il gruppo di failover. 

Distribuire entrambe le istanze gestite in [aree associate](../../best-practices-availability-paired-regions.md) per motivi di prestazioni. Le istanze gestite che si trovano in aree geografiche associate hanno prestazioni di gran lunga migliori di quelle che si trovano in aree non associate. 


# <a name="portal"></a>[Portale](#tab/azure-portal) 

Usare il portale di Azure per creare il gruppo di risorse e l'istanza gestita primaria. 

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare `Azure SQL` nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando **Mostra dettagli** nel riquadro **Database**.
1. Selezionare **Crea** nel riquadro **Istanze gestite di SQL**. 

    ![Selezionare Istanza gestita di SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Nella scheda **Generale** della pagina **Crea Istanza gestita di database SQL di Azure** :
    1. In **Dettagli del progetto** selezionare la **sottoscrizione** dall'elenco a discesa e quindi scegliere **Crea nuovo** per creare il gruppo di risorse. Digitare un nome per il gruppo di risorse, ad esempio `myResourceGroup`. 
    1. In **Dettagli dell'istanza gestita** immettere il nome dell'istanza gestita e l'area in cui si vuole distribuire l'istanza gestita. Lasciare i valore predefiniti per **Calcolo e archiviazione**. 
    1. In **Account amministratore** immettere un account di accesso amministratore, ad esempio `azureuser`, e una password amministratore complessa. 

    ![Creare l'istanza gestita primaria](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Lasciare i valori predefiniti per le altre impostazioni e selezionare **Rivedi e crea** per rivedere le impostazioni di Istanza gestita di SQL. 
1. Selezionare **Crea** per creare l'istanza gestita primaria. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare PowerShell per creare il gruppo di risorse e l'istanza gestita primaria. 

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
   
   # Set the subscription context
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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

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

## <a name="create-secondary-virtual-network"></a>Creare la rete virtuale secondaria

Se si usa il portale di Azure per creare l'istanza gestita, sarà necessario creare la rete virtuale separatamente perché la subnet dell'istanza gestita primaria e di quella secondaria non devono avere intervalli sovrapposti. Se si usa PowerShell per configurare l'istanza gestita, continuare direttamente con il passaggio 3. 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Per verificare l'intervallo di subnet della rete virtuale primaria, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse e selezionare la rete virtuale per l'istanza primaria.  
2. Selezionare **Subnet** in **Impostazioni** e prendere nota dell' **intervallo di indirizzi**. L'intervallo di indirizzi della subnet della rete virtuale per l'istanza gestita secondaria non può sovrapporsi a questo intervallo. 


   ![Subnet primaria](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Per creare una rete virtuale, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea risorsa** e cercare *rete virtuale*. 
1. Selezionare l'opzione **Rete virtuale** pubblicata da Microsoft e quindi selezionare **Crea** nella pagina successiva. 
1. Compilare i campi obbligatori per configurare la rete virtuale per l'istanza gestita secondaria e quindi selezionare **Crea**. 

   La tabella seguente mostra i valori necessari per la rete virtuale secondaria:

    | **Campo** | valore |
    | --- | --- |
    | **Nome** |  Nome della rete virtuale che deve essere usata dall'istanza gestita secondaria, ad esempio `vnet-sql-mi-secondary`. |
    | **Spazio indirizzi** | Spazio indirizzi della rete virtuale, ad esempio `10.128.0.0/16`. | 
    | **Sottoscrizione** | Sottoscrizione in cui trovano l'istanza gestita primaria e il gruppo di risorse. |
    | **Area** | Località in cui si distribuirà l'istanza gestita secondaria. |
    | **Subnet** | Nome della subnet. Per impostazione predefinita, è specificato `default`. |
    | **Intervallo di indirizzi**| Intervallo di indirizzi per la subnet. Deve essere diverso dall'intervallo di indirizzi della subnet usato dalla rete virtuale dell'istanza gestita primaria, ad esempio `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Valori della rete virtuale secondaria](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Questo passaggio è necessario solo se si usa il portale di Azure per distribuire Istanza gestita di SQL. Procedere al passaggio 3 se si usa PowerShell. 

---

## <a name="create-a-secondary-managed-instance"></a>Creare un'istanza gestita secondaria
In questo passaggio si creerà un'istanza gestita secondaria nel portale di Azure, che configurerà anche la rete tra le due istanze gestite. 

La seconda istanza gestita deve:
- Essere vuota. 
- Avere una subnet e un intervallo IP diversi da quello dell'istanza gestita primaria. 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Creare l'istanza gestita secondaria usando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare `Azure SQL` nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando **Mostra dettagli** nel riquadro **Database**.
1. Selezionare **Crea** nel riquadro **Istanze gestite di SQL**. 

    ![Selezionare Istanza gestita di SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Nella scheda **Generale** della pagina **Crea Istanza gestita di database SQL di Azure** compilare i campi obbligatori per configurare l'istanza gestita secondaria. 

   La tabella seguente mostra i valori necessari per l'istanza gestita secondaria:
 
    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui trova l'istanza gestita primaria. |
    | **Gruppo di risorse**| Gruppo di risorse in cui trova l'istanza gestita primaria. |
    | **SQL Managed Instance name** (Nome dell'istanza gestita di SQL) | Nome della nuova istanza gestita secondaria, ad esempio `sql-mi-secondary`.  | 
    | **Area**| Località dell'istanza gestita secondaria.  |
    | **SQL Managed Instance admin login** (Account di accesso amministratore per l'istanza gestita di SQL) | Account di accesso per la nuova istanza gestita secondaria, ad esempio `azureuser`. |
    | **Password** | Password complessa che verrà usata dall'account di accesso amministratore per la nuova istanza gestita secondaria.  |
    | &nbsp; | &nbsp; |

1. Nella scheda **Rete** per **Rete virtuale** selezionare dall'elenco a discesa la rete virtuale creata per l'istanza gestita secondaria.

   ![Rete dell'istanza gestita secondaria](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Nella scheda **Impostazioni aggiuntive** per **Replica geografica** scegliere **Sì** per _Usa come failover secondario_. Selezionare dall'elenco a discesa l'istanza gestita primaria. 
    
   Verificare che le regole di confronto e il fuso orario corrispondano a quelli dell'istanza gestita primaria. L'istanza gestita primaria creata in questa esercitazione usava l'impostazione predefinita delle regole di confronto `SQL_Latin1_General_CP1_CI_AS` e il fuso orario `(UTC) Coordinated Universal Time`. 

   ![Rete dell'istanza gestita secondaria](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selezionare **Rivedi e crea** per rivedere le impostazioni per l'istanza gestita secondaria. 
1. Selezionare **Crea** per creare l'istanza gestita secondaria. 

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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

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

## <a name="create-a-primary-gateway"></a>Creare un gateway primario 

Per fare in modo che due istanze gestite partecipino a un gruppo di failover, è necessario che sia configurato ExpressRoute o un gateway tra le reti virtuali delle due istanze gestite per consentire la comunicazione di rete. Se si sceglie di configurare [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) invece di connettere due gateway VPN, procedere al [passaggio 7](#create-a-failover-group).  

Questo articolo illustra la procedura per creare i due gateway VPN e per connetterli, ma è possibile passare direttamente alla creazione del gruppo di failover se invece è stato configurato ExpressRoute. 

> [!NOTE]
> Lo SKU del gateway influisce sulle prestazioni della velocità effettiva. Questa esercitazione distribuisce un gateway con lo SKU di base (`HwGw1`). Per ottenere una velocità effettiva più elevata, distribuire uno SKU superiore (ad esempio: `VpnGw3`). Per tutte le opzioni disponibili, vedere [SKU del gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gateway per la rete virtuale dell'istanza gestita primaria usando il portale di Azure. 


1. Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse e selezionare la risorsa **Rete virtuale** per l'istanza gestita primaria. 
1. Selezionare **Subnet** in **Impostazioni** e quindi selezionare l'opzione per aggiungere una nuova **subnet del gateway**. Lasciare invariati i valori predefiniti. 

   ![Aggiungere il gateway per l'istanza gestita primaria](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Dopo aver creato il gateway della subnet, selezionare **Crea risorsa** dal riquadro di spostamento a sinistra e quindi digitare `Virtual network gateway` nella casella di ricerca. Selezionare la risorsa **Gateway di rete virtuale** pubblicata da **Microsoft**. 

   ![Creare un nuovo gateway di rete virtuale](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita primaria. 

   La tabella seguente mostra i valori necessari per il gateway dell'istanza gestita primaria:
 
    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui trova l'istanza gestita primaria. |
    | **Nome** | Nome del gateway di rete virtuale, ad esempio `primary-mi-gateway`. | 
    | **Area** | Area in cui trova l'istanza gestita primaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo di VPN** | Selezionare **Basato su route**. |
    | **SKU**| Lasciare l'impostazione predefinita `VpnGw1`. |
    | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-primary`. |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Lasciare invariati gli altri valori predefiniti e quindi selezionare **Rivedi e crea** per rivedere le impostazioni del gateway di rete virtuale.

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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

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


## <a name="create-secondary-gateway"></a>Creare il gateway secondario 
In questo passaggio creare il gateway per la rete virtuale dell'istanza gestita secondaria usando il portale di Azure. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Usando il portale di Azure, ripetere i passaggi della sezione precedente per creare la subnet della rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria. 

   La tabella seguente mostra i valori necessari per il gateway dell'istanza gestita secondaria:

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui trova l'istanza gestita secondaria. |
   | **Nome** | Nome del gateway di rete virtuale, ad esempio `secondary-mi-gateway`. | 
   | **Area** | Area in cui trova l'istanza gestita secondaria. |
   | **Tipo di gateway** | Selezionare **VPN**. |
   | **Tipo di VPN** | Selezionare **Basato su route**. |
   | **SKU**| Lasciare l'impostazione predefinita `VpnGw1`. |
   | **Rete virtuale**| Selezionare la rete virtuale per l'istanza gestita secondaria, ad esempio `vnet-sql-mi-secondary`. |
   | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
   | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `secondary-gateway-IP`. |
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
   
   Write-host "Creating secondary gateway..."
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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

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


## <a name="connect-the-gateways"></a>Connettere i gateway
In questo passaggio creare una connessione bidirezionale tra i due gateway delle due reti virtuali. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Connettere i due gateway usando il portale di Azure. 


1. Selezionare **Crea una risorsa** nel [portale di Azure](https://portal.azure.com).
1. Digitare `connection` nella casella di ricerca e quindi premere INVIO per avviare la ricerca. Verrà visualizzala la risorsa **Connessione** , pubblicata da Microsoft.
1. Fare clic su **Crea** per creare la connessione. 
1. Nella pagina **Generale** selezionare i valori seguenti e quindi scegliere **OK**. 
    1. Selezionare `VNet-to-VNet` per **Tipo di connessione**. 
    1. Selezionare la sottoscrizione dall'elenco a discesa. 
    1. Selezionare il gruppo di risorse per Istanza gestita di SQL nell'elenco a discesa. 
    1. Selezionare dall'elenco a discesa la località dell'istanza gestita primaria. 
1. Nella pagina **Impostazioni** selezionare o immettere i valori seguenti e quindi scegliere **OK** :
    1. Scegliere il gateway di rete primario per **Primo gateway di rete virtuale** , ad esempio `primaryGateway`.  
    1. Scegliere il gateway di rete secondario per **Secondo gateway di rete virtuale** , ad esempio `secondaryGateway`. 
    1. Selezionare la casella di controllo accanto a **Stabilisci connettività bidirezionale**. 
    1. Lasciare il nome predefinito della connessione primaria o rinominarla con un valore di propria scelta. 
    1. Specificare una **chiave condivisa (PSK)** per la connessione, ad esempio `mi1m2psk`. 
    1. Selezionare **OK** per salvare le impostazioni. 

    ![Creare una connessione gateway](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Nella pagina **Rivedi e crea** rivedere le impostazioni per la connessione bidirezionale e quindi selezionare **OK** per creare la connessione. 


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

In questa parte dell'esercitazione si usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |

---


## <a name="create-a-failover-group"></a>Creare un gruppo di failover
In questo passaggio si creerà il gruppo di failover e si aggiungeranno le due istanze gestite. 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover con il portale di Azure. 


1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare `Azure SQL` nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare l'istanza gestita primaria creata nella prima sezione, ad esempio `sql-mi-primary`. 
1. In **Impostazioni** passare a **Gruppi di failover dell'istanza** e quindi scegliere **Aggiungi gruppo** per aprire la pagina **Gruppo di failover dell'istanza**. 

   ![Aggiungere un gruppo di failover](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Nella pagina **Gruppo di failover dell'istanza** digitare il nome del gruppo di failover, ad esempio `failovergrouptutorial`, quindi scegliere l'istanza gestita secondaria, ad esempio `sql-mi-secondary`, dall'elenco a discesa. Fare clic su **Crea** per creare il gruppo di failover. 

   ![Create failover group](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Al termine della distribuzione del gruppo di failover, si tornerà alla pagina **Gruppo di failover**. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Creare il gruppo di failover con PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

In questa parte dell'esercitazione si usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita di SQL di Azure.  |


---


## <a name="test-failover"></a>Failover di test
In questo passaggio verrà eseguito il failover del gruppo di failover nel server secondario e quindi il failback usando il portale di Azure. 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Testare il failover con il portale di Azure. 


1. Passare all'istanza gestita _secondaria_ con il [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di failover dell'istanza** nelle impostazioni. 
1. Verificare quale istanza gestita è quella primaria e quale la secondaria. 
1. Selezionare **Failover** e quindi selezionare **Sì** nell'avviso relativo alla disconnessione delle sessioni TDS. 

   ![Eseguire il failover del gruppo di failover](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Verificare quale istanza gestita è quella primaria e quale la secondaria. Se il failover è riuscito, le due istanze dovrebbero essersi scambiate i ruoli. 

   ![Le istanze gestite si sono scambiate i ruoli dopo il failover](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Passare alla nuova istanza gestita _secondaria_ e selezionare **Failover** ancora una volta per eseguire il failback dell'istanza primaria al ruolo primario. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testare il failover con PowerShell. 

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


Ripristinare il gruppo di failover nel server primario:

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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita di SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita di SQL. | 

---



## <a name="clean-up-resources"></a>Pulire le risorse
Pulire le risorse eliminando prima le istanze gestite, quindi il cluster virtuale, poi le eventuali risorse rimanenti e infine il gruppo di risorse. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
1. Selezionare l'istanza o le istanze gestite, quindi selezionare **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. L'esecuzione di questa procedura in background può richiedere del tempo e, finché non viene completata, non sarà possibile eliminare il *cluster virtuale* o altre risorse dipendenti. Monitorare l'eliminazione nella scheda **Attività** per verificare che l'istanza gestita sia stata eliminata. 
1. Una volta eliminata l'istanza gestita, eliminare il *cluster virtuale* selezionandolo nel gruppo di risorse e quindi scegliendo **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare eventuali risorse rimanenti. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare il gruppo di risorse selezionando **Elimina gruppo di risorse** , digitando il relativo nome, `myResourceGroup`, quindi selezionando **Elimina**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sarà necessario rimuovere due volte il gruppo di risorse. Quando si rimuove il gruppo di risorse la prima volta, verranno rimossi le istanze gestite e i cluster virtuali, ma l'operazione non verrà completata e verrà visualizzato il messaggio di errore `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'`. Eseguire una seconda volta il comando Remove-AzResourceGroup per rimuovere tutte le risorse residue e il gruppo di risorse.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

In questa parte dell'esercitazione si usa il cmdlet di PowerShell seguente:

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
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita di SQL.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita di SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita di SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. | 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Non sono disponibili script per il portale di Azure.

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è configurato un gruppo di failover tra due istanze gestite. Si è appreso come:

> [!div class="checklist"]
> - Creare un'istanza gestita primaria.
> - Creare un'istanza gestita secondaria come parte di un [gruppo di failover](../database/auto-failover-group-overview.md). 
> - Testare il failover.

Passare all'argomento di avvio rapido successivo che illustra come connettersi a Istanza gestita di SQL e come ripristinare un database in Istanza gestita di SQL: 

> [!div class="nextstepaction"]
> [Connettersi a Istanza gestita di SQL](connect-vm-instance-configure.md)
> [Ripristinare un database in Istanza gestita di SQL](restore-sample-database-quickstart.md)


