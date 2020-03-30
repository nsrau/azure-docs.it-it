---
title: "Esercitazione: Aggiungere un'istanza gestita a un gruppo di failoverTutorial: Add a managed instance to a failover group"
description: Informazioni su come configurare un gruppo di failover per l'istanza gestita del database SQL di Azure.Learn to configure a failover group for your Azure SQL Database managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462650"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Esercitazione: Aggiungere un'istanza gestita del database SQL a un gruppo di failoverTutorial: Add a SQL Database managed instance to a failover group

Aggiungere un'istanza gestita di database SQL a un gruppo di failover. In questo articolo verrà spiegato come:

> [!div class="checklist"]
> - Creare un'istanza gestita primariaCreate a primary managed instance
> - Creare un'istanza gestita secondaria come parte di un gruppo di [failover.](sql-database-auto-failover-group.md) 
> - Failover di test

  > [!NOTE]
  > - Quando si esegue questa esercitazione, assicurarsi di configurare le risorse con i prerequisiti per l'impostazione dei gruppi di [failover per l'istanza gestita.](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets) 
  > - La creazione di un'istanza gestita può richiedere una notevole quantità di tempo. Di conseguenza, il completamento di questa esercitazione potrebbe richiedere diverse ore. Per ulteriori informazioni sui tempi di provisioning, vedere Operazioni di gestione delle [istanze gestite](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - Le istanze gestite che partecipano a un gruppo di failover richiedono [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) o due gateway VPN connessi. Questa esercitazione illustra i passaggi per la creazione e la connessione dei gateway VPN. Ignorare questi passaggi se ExpressRoute è già configurato. 


## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Crea un account gratuito](https://azure.microsoft.com/free/) se non ne hai già uno.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Per completare l'esercitazione, assicurarsi di disporre dei seguenti elementi:

- Una sottoscrizione di Azure. [Crea un account gratuito](https://azure.microsoft.com/free/) se non ne hai già uno.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Creare il gruppo di risorse e l'istanza gestita primaria
In this step, you will create the resource group and the primary managed instance for your failover group using the Azure portal or PowerShell. 


# <a name="portal"></a>[Portale](#tab/azure-portal) 

Creare il gruppo di risorse e l'istanza gestita primaria usando il portale di Azure.Create the resource group and your primary managed instance using the Azure portal. 

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando Mostra dettagli nel riquadro Database.
1. Selezionare **Crea** nel riquadro **Istanze gestite SQL.** 

    ![Selezionare l'istanza gestitaSelect managed instance](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Nella pagina **Crea istanza gestita database SQL** di Azure, nella scheda **Nozioni di base**
    1. In **Dettagli progetto**selezionare la **sottoscrizione** dall'elenco a discesa e quindi scegliere Crea **nuovo** gruppo di risorse. Digitare un nome per il `myResourceGroup`gruppo di risorse, ad esempio . 
    1. In **Dettagli istanza gestita**specificare il nome dell'istanza gestita e l'area in cui si desidera distribuire l'istanza gestita. Lasciare i valori predefiniti di **Calcolo e archiviazione.** 
    1. In **Account amministratore**specificare un `azureuser`account di accesso amministratore, ad esempio , e una password di amministratore complessa. 

    ![Crea MI primario](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Revisione : crea** per rivedere le impostazioni dell'istanza gestita. 
1. Selezionare **Crea** per creare l'istanza gestita primaria. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gruppo di risorse e l'istanza gestita primaria usando PowerShell.Create your resource group and the primary managed instance using PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
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
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
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
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
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
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
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
   Write-host "Primary managed instance created successfully."
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita di database SQL di Azure.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Creare una rete virtuale secondaria
Se si usa il portale di Azure per creare l'istanza gestita, sarà necessario creare la rete virtuale separatamente perché è necessario che la subnet dell'istanza gestita primaria e secondaria non abbia intervalli sovrapposti. Se si usa PowerShell per configurare l'istanza gestita, andare al passaggio 3.If you're using PowerShell to configure your managed instance, skip ahead to step 3. 

# <a name="portal"></a>[Portale](#tab/azure-portal) 
Per verificare l'intervallo di subnet della rete virtuale primaria, attenersi alla seguente procedura:
1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la rete virtuale per l'istanza primaria. 
1. Selezionare **Subnet** in **Impostazioni** e prendere nota **dell'intervallo di indirizzi**. L'intervallo di indirizzi di subnet della rete virtuale per l'istanza gestita secondaria non può sovrapporsi. 


   ![Subnet primaria](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Per creare una rete virtuale, attenersi alla seguente procedura:

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa** e cercare una rete *virtuale.* 
1. Selezionare l'opzione **Rete virtuale** pubblicata da Microsoft e quindi selezionare **Crea** nella pagina successiva. 
1. Compilare i campi obbligatori per configurare la rete virtuale per l'istanza gestita secondaria e quindi selezionare **Crea**. 

   Nella tabella seguente vengono illustrati i valori necessari per la rete virtuale secondaria:The following table shows the values necessary for the secondary virtual network:

    | **Campo** | valore |
    | --- | --- |
    | **Nome** |  Nome della rete virtuale che deve essere utilizzato dall'istanza gestita secondaria, ad `vnet-sql-mi-secondary`esempio . |
    | **Spazio di indirizzi** | Spazio di indirizzi per la `10.128.0.0/16`rete virtuale, ad esempio . | 
    | **Sottoscrizione** | Sottoscrizione in cui risiedono l'istanza gestita primaria e il gruppo di risorse. |
    | **Regione** | Percorso in cui verrà distribuita l'istanza gestita secondaria. |
    | **Subnet** | Nome della subnet. `default`viene fornito per impostazione predefinita. |
    | **Intervallo di indirizzi**| Intervallo di indirizzi per la subnet. Deve essere diverso dall'intervallo di indirizzi della subnet utilizzato dalla `10.128.0.0/24`rete virtuale dell'istanza gestita primaria, ad esempio .  |
    | &nbsp; | &nbsp; |

    ![Valori di rete virtuale secondaria](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Questo passaggio è necessario solo se si usa il portale di Azure per distribuire l'istanza gestita. Passare al passaggio 3 se si usa PowerShell.Skip ahead to step 3 if you're using PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Creare un'istanza gestita secondaria
In questo passaggio verrà creata un'istanza gestita secondaria nel portale di Azure, che configurerà anche la rete tra le due istanze gestite. 

La seconda istanza gestita deve:Your second managed instance must:
- Siate vuoti. 
- Avere una subnet e un intervallo IP diversi rispetto all'istanza gestita primaria. 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Creare l'istanza gestita secondaria usando il portale di Azure.Create the secondary managed instance using the Azure portal. 

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando Mostra dettagli nel riquadro Database.
1. Selezionare **Crea** nel riquadro **Istanze gestite SQL.** 

    ![Selezionare l'istanza gestitaSelect managed instance](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Nella scheda **Nozioni di base** della pagina **Crea istanza gestita database SQL** di Azure compilare i campi obbligatori per configurare l'istanza gestita secondaria. 

   Nella tabella seguente vengono illustrati i valori necessari per l'istanza gestita secondaria:The following table shows the values necessary for the secondary managed instance:
 
    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Gruppo di risorse**| Gruppo di risorse in cui si trova l'istanza gestita primaria. |
    | **Nome istanza gestita** | Il nome della nuova istanza gestita secondaria, ad esempio`sql-mi-secondary`  | 
    | **Regione**| Percorso dell'istanza gestita secondaria.  |
    | **Account di accesso amministratore istanza gestita** | L'account di accesso che si desidera utilizzare `azureuser`per la nuova istanza gestita secondaria, ad esempio . |
    | **Password** | Password complessa che verrà utilizzata dall'account di accesso amministratore per la nuova istanza gestita secondaria.  |
    | &nbsp; | &nbsp; |

1. Nella scheda **Rete,** per **Rete virtuale,** selezionare dall'elenco a discesa la rete virtuale creata per l'istanza gestita secondaria.

   ![Rete MI secondaria](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Nella scheda **Impostazioni aggiuntive,** per **Replica geografica**, scegliere **Sì** per utilizzare come _failover secondario_. Selezionare l'istanza gestita primaria dall'elenco a discesa. 
    1. Assicurarsi che le regole di confronto e il fuso orario corrispondano a quelli dell'istanza gestita primaria. L'istanza gestita primaria creata in `SQL_Latin1_General_CP1_CI_AS` questa `(UTC) Coordinated Universal Time` esercitazione ha usato il valore predefinito delle regole di confronto e il fuso orario. 

   ![Rete MI secondaria](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selezionare **Revisione : crea** per rivedere le impostazioni per l'istanza gestita secondaria. 
1. Selezionare **Crea** per creare l'istanza gestita secondaria. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare l'istanza gestita secondaria tramite PowerShell.Create the secondary managed instance using PowerShell. 

   ```powershell-interactive
   # Configure secondary virtual network
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
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
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
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
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
   Write-host "Secondary managed instance created successfully."
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita di database SQL di Azure.  |

---

## <a name="4---create-primary-gateway"></a>4 - Creare un gateway primario 
Affinché due istanze gestite partecipino a un gruppo di failover, è necessario che tra le reti virtuali delle due istanze gestite sia configurato ExpressRoute o un gateway delle due istanze gestite. Se si sceglie di configurare [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) anziché connettere due gateway VPN, andare al [passaggio 7.](#7---create-a-failover-group)  

Questo articolo illustra i passaggi per creare i due gateway VPN e connetterli, ma è possibile passare alla creazione del gruppo di failover se è stato configurato ExpressRoute.This article provides steps to create the two VPN gateways and connect them, but you can skip ahead to creating the failover group if you have configured ExpressRoute instead. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gateway per la rete virtuale dell'istanza gestita primaria usando il portale di Azure.Create the gateway for the virtual network of your primary managed instance using the Azure portal. 


1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la risorsa **di rete virtuale** per l'istanza gestita primaria. 
1. Selezionare **Subnet** in **Impostazioni** e quindi selezionare per aggiungere una nuova **subnet gateway**. Lasciare i valori predefiniti. 

   ![Aggiungere un gateway per l'istanza gestita primariaAdd gateway for primary managed instance](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Dopo aver creato il gateway di subnet, selezionare Crea `Virtual network gateway` una **risorsa** nel riquadro di spostamento sinistro e quindi digitare nella casella di ricerca. Selezionare la risorsa **Gateway di rete virtuale** pubblicata da **Microsoft**. 

   ![Creare un nuovo gateway di rete virtualeCreate a new virtual network gateway](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Compilare i campi obbligatori per configurare il gateway dell'istanza gestita primaria. 

   Nella tabella seguente vengono illustrati i valori necessari per il gateway per l'istanza gestita primaria:The following table shows the values necessary for the gateway for the primary managed instance:
 
    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Nome** | Nome del gateway di rete `primary-mi-gateway`virtuale, ad esempio . | 
    | **Regione** | Area in cui si trova l'istanza gestita secondaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo VPN** | Selezionare **Basato su percorso** |
    | **Sku**| Lasciare il `VpnGw1`valore predefinito di . |
    | **Percorso**| Percorso in cui si trovano l'istanza gestita primaria e la rete virtuale primaria.   |
    | **Rete virtuale**| Selezionare la rete virtuale creata nella `vnet-sql-mi-primary`sezione 2, ad esempio . |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Lasciare gli altri valori come predefiniti e quindi selezionare **Revisione : crea** per esaminare le impostazioni per il gateway di rete virtuale.

   ![Impostazioni del gateway primario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selezionare **Crea** per creare il nuovo gateway di rete virtuale. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gateway per la rete virtuale dell'istanza gestita primaria tramite PowerShell.Create the gateway for the virtual network of your primary managed instance using PowerShell. 

   ```powershell-interactive
   # Create primary gateway
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

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

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


## <a name="5---create-secondary-gateway"></a>5 - Creare un gateway secondario 
In questo passaggio creare il gateway per la rete virtuale dell'istanza gestita secondaria usando il portale di Azure, 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Usando il portale di Azure, ripetere i passaggi nella sezione precedente per creare la subnet della rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria. 

   Nella tabella seguente vengono illustrati i valori necessari per il gateway per l'istanza gestita secondaria:The following table shows the values necessary for the gateway for the secondary managed instance:

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita secondaria. |
   | **Nome** | Nome del gateway di rete `secondary-mi-gateway`virtuale, ad esempio . | 
   | **Regione** | Area in cui si trova l'istanza gestita secondaria. |
   | **Tipo di gateway** | Selezionare **VPN**. |
   | **Tipo VPN** | Selezionare **Basato su percorso** |
   | **Sku**| Lasciare il `VpnGw1`valore predefinito di . |
   | **Percorso**| Percorso in cui si trovano l'istanza gestita secondaria e la rete virtuale secondaria.   |
   | **Rete virtuale**| Selezionare la rete virtuale creata nella `vnet-sql-mi-secondary`sezione 2, ad esempio . |
   | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
   | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Impostazioni del gateway secondario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gateway per la rete virtuale dell'istanza gestita secondaria tramite PowerShell.Create the gateway for the virtual network of the secondary managed instance using PowerShell. 

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

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

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


## <a name="6---connect-the-gateways"></a>6 - Collegare i gateway
In questo passaggio creare una connessione bidirezionale tra i due gateway delle due reti virtuali. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Connettere i due gateway usando il portale di Azure.Connect the two gateways using the Azure portal. 


1. Selezionare **Crea una risorsa** dal portale di [Azure.](https://portal.azure.com)
1. Digitare `connection` nella casella di ricerca e quindi premere INVIO per eseguire la ricerca, che consente di passare alla risorsa **Connessione,** pubblicata da Microsoft.
1. Selezionare **Crea** per creare la connessione. 
1. Nella scheda **Nozioni di base** selezionare i valori seguenti e quindi **scegliere OK**. 
    1. Selezionare `VNet-to-VNet` per il **tipo di connessione**. 
    1. Selezionare la sottoscrizione dall'elenco a discesa. 
    1. Selezionare il gruppo di risorse per l'istanza gestita nell'elenco a discesa. 
    1. Selezionare la posizione dell'istanza gestita primaria dall'elenco a discesa 
1. Nella scheda **Impostazioni** selezionare o immettere i valori seguenti e quindi scegliere **OK:**
    1. Scegliere il gateway di rete primario per `Primary-Gateway`il primo gateway di rete **virtuale**, ad esempio .  
    1. Scegliere il gateway di rete secondario per `Secondary-Gateway`il secondo gateway di rete **virtuale**, ad esempio . 
    1. Selezionare la casella di controllo accanto a **Stabilisci connettività bidirezionale**. 
    1. Lasciare il nome della connessione primaria predefinito o rinominarlo con un valore di propria scelta. 
    1. Fornire una **chiave condivisa (PSK)** per `mi1m2psk`la connessione, ad esempio . 

   ![Creare una connessione gatewayCreate gateway connection](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Nella scheda **Riepilogo** esaminare le impostazioni per la connessione bidirezionale e quindi selezionare **OK** per creare la connessione. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Connettere i due gateway tramite PowerShell.Connect the two gateways using PowerShell. 

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

In questa parte dell'esercitazione viene utilizzato il cmdlet PowerShell seguente:

| Comando | Note |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |

---


## <a name="7---create-a-failover-group"></a>7 - Creare un gruppo di failover
In questo passaggio verrà creato il gruppo di failover e vi verranno aggiunte entrambe le istanze gestite. 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover usando il portale di Azure.Create the failover group using the Azure portal. 


1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare l'istanza gestita primaria creata `sql-mi-primary`nella prima sezione, ad esempio . 
1. In **Impostazioni**passare a Gruppi di **failover istanze** e quindi scegliere **Aggiungi gruppo** per aprire la pagina Gruppo di **failover istanza.** 

   ![Aggiungere un gruppo di failoverAdd a failover group](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Nella pagina Gruppo di **failover istanza** digitare il `failovergrouptutorial` nome del gruppo di failover, `sql-mi-secondary` ad esempio e quindi scegliere l'istanza gestita secondaria, ad esempio dall'elenco a discesa. Selezionare **Crea** per creare il gruppo di failover. 

   ![Create failover group](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Al termine della distribuzione del gruppo di failover, verrà riportata alla pagina Gruppo di **failover.** 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Creare il gruppo di failover usando PowerShell.Create the failover group using PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

In questa parte dell'esercitazione viene utilizzato il cmdlet PowerShell seguente:

| Comando | Note |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita del database SQL di Azure.  |


---


## <a name="8---test-failover"></a>8 - Failover di test
In questo passaggio verrà eseguito il failover del gruppo di failover nel server secondario e quindi verrà eseguito il failover tramite il portale di Azure.In this step, you will fail your failover group over to the secondary server, and then failback using the Azure portal. 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Testare il failover usando il portale di Azure.Test failover using the Azure portal. 


1. Passare all'istanza gestita secondaria all'interno del portale di Azure e selezionare Gruppi di **failover dell'istanza** in Impostazioni.Navigate to your _secondary_ managed instance within the [Azure portal](https://portal.azure.com) and select Instance Failover Groups under settings. 
1. Esaminare quale istanza gestita è la primaria e l'istanza gestita come secondaria. 
1. Selezionare **Failover** e quindi **selezionare Sì** nell'avviso relativo alla disconnessione delle sessioni TDS. 

   ![Eseguire il failover del gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Esaminare quale istanza manging è il primario e quale istanza è quella secondaria. Se il failover ha esito positivo, le due istanze devono avere cambiato ruolo. 

   ![Le istanze gestite hanno cambiato ruolo dopo il failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Passare alla nuova istanza gestita _secondaria_ e selezionare di nuovo **Failover** per eseguire nuovamente l'esito negativo dell'istanza primaria al ruolo primario. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Testare il failover tramite PowerShell.Test failover using PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
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
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita. | 

---



## <a name="clean-up-resources"></a>Pulire le risorse
Pulire le risorse eliminando prima l'istanza gestita, quindi il cluster virtuale, le risorse rimanenti e infine il gruppo di risorse. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
1. Selezionare l'istanza o le istanze gestite, quindi selezionare **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. Questo processo può richiedere del tempo per completare in background e fino a quando non viene fatto, non sarà possibile eliminare il *cluster virtuale* o altre risorse dipendenti. Monitorare l'eliminazione nella scheda Attività per verificare che l'istanza gestita sia stata eliminata. 
1. Una volta eliminata l'istanza gestita, eliminare il *cluster virtuale* selezionandolo nel gruppo di risorse e quindi scegliendo **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare eventuali risorse rimanenti. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare il gruppo di risorse selezionando **Elimina gruppo di risorse**, digitando il relativo nome, `myResourceGroup`, quindi selezionando **Elimina**. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Sarà necessario rimuovere due volte il gruppo di risorse. Quando si rimuove il gruppo di risorse la prima volta, verranno rimossi l'istanza gestita e i cluster virtuali, ma l'operazione non verrà completata e verrà visualizzato il messaggio di errore `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Eseguire una seconda volta il comando Remove-AzResourceGroup per rimuovere tutte le risorse residue e il gruppo di risorse.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

In questa parte dell'esercitazione viene utilizzato il cmdlet PowerShell seguente:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. |

---

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita di database SQL di Azure.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Restituisce informazioni sul database dell'istanza gestita del database SQL di Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configurazione IP per un gateway di rete virtuale. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea un gateway di rete virtuale. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita del database SQL di Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. | 

# <a name="portal"></a>[Portale](#tab/azure-portal) 

Non sono disponibili script per il portale di Azure.There are no scripts available for the Azure portal.

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un gruppo di failover tra due istanze gestite. Si è appreso come:

> [!div class="checklist"]
> - Creare un'istanza gestita primariaCreate a primary managed instance
> - Creare un'istanza gestita secondaria come parte di un gruppo di [failover.](sql-database-auto-failover-group.md) 
> - Failover di test

Passare alla guida introduttiva successiva su come connettersi all'istanza gestita e come ripristinare un database nell'istanza gestita:Advance to the next quickstart on how to connect to your managed instance, and how to restore a database to your managed instance: 

> [!div class="nextstepaction"]
> [Connettersi all'istanza gestita](sql-database-managed-instance-configure-vm.md)
> [Ripristinare un database in un'istanza gestitaConnect](sql-database-managed-instance-get-started-restore.md) to your managed instance Restore a database to a managed instance


