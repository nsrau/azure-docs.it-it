---
title: Configurare un gruppo di failover
description: Informazioni su come configurare un gruppo di failover automatico per un singolo database del database SQL di Azure, un pool elastico e un'istanza gestita tramite il portale di Azure, l'interfaccia della riga di comando Az e PowerShell.Learn how to configure an Auto-failover group for an Azure SQL Database single database, elastic pool, and managed instance using the Azure portal, the Az CLI, and PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461808"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configurare un gruppo di failover per il database SQL di AzureConfigure a failover group for Azure SQL Database

Questo argomento illustra come configurare un gruppo di failover automatico per un singolo database del database SQL di Azure, un pool elastico e un'istanza gestita tramite il portale di Azure o PowerShell.This topic teaches you how to configure an [auto-failover group](sql-database-auto-failover-group.md) for an Azure SQL Database single database, elastic pool, and managed instance using the Azure portal, or PowerShell. 

## <a name="single-database"></a>Database singolo
Creare il gruppo di failover e aggiungervi un singolo database usando il portale di Azure o PowerShell.Create the failover group and add a single database to it using the Azure portal, or PowerShell.

### <a name="prerequisites"></a>Prerequisiti

Considerare i prerequisiti seguenti:Consider the following prerequisites:

- Le impostazioni di accesso al server e del firewall per il server secondario devono corrispondere a quelle del server primario. 

### <a name="create-failover-group"></a>Create failover group

# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover e aggiungervi il singolo database usando il portale di Azure.Create your failover group and add your single database to it using the Azure portal.


1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il singolo database che si desidera aggiungere al gruppo di failover. 
1. Selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.

   ![Server aperto per singolo database](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selezionare **Gruppi** di failover nel riquadro **Impostazioni** e quindi **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungere un nuovo gruppo di failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **Gruppo di failover** immettere o selezionare i valori richiesti e quindi selezionare **Crea**.

   - **Database all'interno del gruppo:** scegliere il database da aggiungere al gruppo di failover. L'aggiunta del database al gruppo di failover avvierà automaticamente il processo di replica geografica. 
        
    ![Aggiungere il database SQL al gruppo di failoverAdd SQL DB to failover group](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Creare il gruppo di failover e aggiungervi il singolo database tramite PowerShell.Create your failover group and add your single database to it using PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Failover di test 

Testare il failover del gruppo di failover usando il portale di Azure o PowerShell.Test failover of your failover group using the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover usando il portale di Azure.Test failover of your failover group using the Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il singolo database che si desidera aggiungere al gruppo di failover. 

   ![Server aperto per singolo database](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selezionare **Gruppi di** failover nel riquadro **Impostazioni** e quindi scegliere il gruppo di failover appena creato. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Esaminare quale server è primario e quale server secondario. 
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover contenente il singolo database. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Esaminare quale server è ora primario e quale server è secondario. Se il failover ha esito positivo, i due server devono avere scambiato i ruoli. 
1. Selezionare di nuovo **Failover** per eseguire il failover dei server ai ruoli originari. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testare il failover del gruppo di failover tramite PowerShell.Test failover of your failover group using PowerShell.  

Controllare il ruolo della replica secondaria: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Eseguire il failover al server secondario: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Ripristinare il gruppo di failover nel server primario:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Se è necessario eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima che venga rimossa dal gruppo di failover può causare un comportamento imprevedibile. 

## <a name="elastic-pool"></a>Pool elastico
Creare il gruppo di failover e aggiungervi un pool elastico usando il portale di Azure o PowerShell.Create the failover group and add an elastic pool to it using the Azure portal, or PowerShell.  

### <a name="prerequisites"></a>Prerequisiti

Considerare i prerequisiti seguenti:Consider the following prerequisites:

- Le impostazioni di accesso al server e del firewall per il server secondario devono corrispondere a quelle del server primario. 

### <a name="create-the-failover-group"></a>Creare il gruppo di failoverCreate the failover group 

Creare il gruppo di failover per il pool elastico usando il portale di Azure o PowerShell.Create the failover group for your elastic pool using the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover e aggiungervi il pool elastico tramite il portale di Azure.Create your failover group and add your elastic pool to it using the Azure portal.

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico che si desidera aggiungere al gruppo di failover. 
1. Nel riquadro **Panoramica** selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.
  
    ![Server aperto per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **Gruppi** di failover nel riquadro **Impostazioni** e quindi **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungere un nuovo gruppo di failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **Gruppo di failover** immettere o selezionare i valori richiesti e quindi selezionare **Crea**. Creare un nuovo server secondario o selezionare un server secondario esistente. 

1. Selezionare **Database all'interno del gruppo,** quindi scegliere il pool elastico da aggiungere al gruppo di failover. Se nel server secondario non esiste già un pool elastico, viene visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi **scegliere OK** per creare il pool elastico nel server secondario. 
        
    ![Aggiungere un pool elastico al gruppo di failoverAdd elastic pool to failover group](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover e quindi selezionare **Crea** per creare il gruppo di failover. L'aggiunta del pool elastico al gruppo di failover avvierà automaticamente il processo di replica geografica. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gruppo di failover e aggiungervi il pool elastico tramite PowerShell.Create your failover group and add your elastic pool to it using PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Failover di test

Testare il failover del pool elastico usando il portale di Azure o PowerShell.Test failover of your elastic pool using the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Eseguire il failover del gruppo di failover nel server secondario e quindi eseguire il failback tramite il portale di Azure.Fail your failover group over to the secondary server, and then fail back using the Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico che si desidera aggiungere al gruppo di failover. 
1. Nel riquadro **Panoramica** selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.
  
    ![Server aperto per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Selezionare Gruppi di failover nel riquadro Impostazioni e quindi scegliere il gruppo di failover creato nella sezione 2.Select **Failover groups** under the **Settings** pane and then choose the failover group you created in section 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Esaminare quale server è primario e quale server secondario. 
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover contenente il pool elastico. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario, il server secondario. Se il failover ha esito positivo, i due server devono avere scambiato i ruoli. 
1. Selezionare di nuovo **Failover** per non eseguire nuovamente le impostazioni originali del gruppo di failover. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testare il failover del gruppo di failover tramite PowerShell.Test failover of your failover group using PowerShell.

Controllare il ruolo della replica secondaria: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Eseguire il failover al server secondario: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Se è necessario eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima che venga rimossa dal gruppo di failover può causare un comportamento imprevedibile. 

## <a name="managed-instance"></a>Istanza gestita

Creare un gruppo di failover tra due istanze gestite usando il portale di Azure o PowerShell.Create a failover group between two managed instances using the Azure portal, or PowerShell. 

Sarà necessario configurare [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) o creare un gateway per la rete virtuale di ogni istanza gestita, connettere i due gateway e quindi creare il gruppo di failover. 

### <a name="prerequisites"></a>Prerequisiti
Considerare i prerequisiti seguenti:Consider the following prerequisites:

- L'istanza gestita secondaria deve essere vuota.
- L'intervallo di subnet per la rete virtuale secondaria non deve sovrapporsi all'intervallo di subnet della rete virtuale primaria. 
- Le regole di confronto e il fuso orario dell'istanza secondaria devono corrispondere a quelli dell'istanza primaria. 
- Quando si connettono i due gateway, la **chiave condivisa** deve essere la stessa per entrambe le connessioni. 

### <a name="create-primary-virtual-network-gateway"></a>Creare un gateway di rete virtuale primarioCreate primary virtual network gateway 

Se ExpressRoute non è stato configurato, è possibile creare il gateway di rete virtuale primario con il portale di Azure o PowerShell.If you have not configured [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md), you can create the primary virtual network gateway with the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gateway di rete virtuale principale usando il portale di Azure.Create the primary virtual network gateway using the Azure portal. 

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
    | **Nome** | Nome del gateway di rete virtuale. | 
    | **Regione** | Area in cui si trova l'istanza gestita secondaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo VPN** | Selezionare **Basato su percorso** |
    | **Sku**| Lasciare il `VpnGw1`valore predefinito di . |
    | **Percorso**| Percorso in cui si trovano l'istanza gestita secondaria e la rete virtuale secondaria.   |
    | **Rete virtuale**| Selezionare la rete virtuale per l'istanza gestita secondaria. |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP. |
    | &nbsp; | &nbsp; |

1. Lasciare gli altri valori come predefiniti e quindi selezionare **Revisione : crea** per esaminare le impostazioni per il gateway di rete virtuale.

   ![Impostazioni del gateway primario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selezionare **Crea** per creare il nuovo gateway di rete virtuale. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gateway di rete virtuale primario tramite PowerShell.Create the primary virtual network gateway using PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Creare un gateway di rete virtuale secondarioCreate secondary virtual network gateway

Creare il gateway di rete virtuale secondario usando il portale di Azure o PowerShell.Create the secondary virtual network gateway using the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Ripetere i passaggi nella sezione precedente per creare la subnet di rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria. 

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

Creare il gateway di rete virtuale secondario tramite PowerShell.Create the secondary virtual network gateway using PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Connettere i gateway 
Creare connessioni tra i due gateway usando il portale di Azure o PowerShell.Create connections between the two gateways using the Azure portal, or PowerShell. 

È necessario creare due connessioni: la connessione dal gateway primario al gateway secondario e quindi la connessione dal gateway secondario al gateway primario. 

La chiave condivisa utilizzata per entrambe le connessioni deve essere la stessa per ogni connessione. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare connessioni tra i due gateway usando il portale di Azure.Create connections between the two gateways using the Azure portal. 

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

Creare connessioni tra i due gateway usando PowerShell.Create connections between the two gateways using PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Creare il gruppo di failoverCreate the failover group 
Creare il gruppo di failover per le istanze gestite usando il portale di Azure o PowerShell.Create the failover group for your managed instances using the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover per le istanze gestite usando il portale di Azure.Create the failover group for your managed instances using Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare l'istanza gestita primaria che si desidera aggiungere al gruppo di failover.  
1. In **Impostazioni**passare a Gruppi di **failover istanze** e quindi scegliere **Aggiungi gruppo** per aprire la pagina Gruppo di **failover istanza.** 

   ![Aggiungere un gruppo di failoverAdd a failover group](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Nella pagina Gruppo di **failover istanza** digitare il nome del gruppo di failover e quindi scegliere l'istanza gestita secondaria dall'elenco a discesa. Selezionare **Crea** per creare il gruppo di failover. 

   ![Create failover group](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Al termine della distribuzione del gruppo di failover, verrà riportata alla pagina Gruppo di **failover.** 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gruppo di failover per le istanze gestite usando PowerShell.Create the failover group for your managed instances using PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Failover di test

Testare il failover del gruppo di failover usando il portale di Azure o PowerShell.Test failover of your failover group using the Azure portal, or PowerShell. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover usando il portale di Azure.Test failover of your failover group using the Azure portal. 

1. Passare all'istanza gestita secondaria all'interno del portale di Azure e selezionare Gruppi di **failover dell'istanza** in Impostazioni.Navigate to your _secondary_ managed instance within the [Azure portal](https://portal.azure.com) and select Instance Failover Groups under settings. 
1. Esaminare quale istanza gestita è la primaria e l'istanza gestita come secondaria. 
1. Selezionare **Failover** e quindi **selezionare Sì** nell'avviso relativo alla disconnessione delle sessioni TDS. 

   ![Eseguire il failover del gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Esaminare quale istanza manging è il primario e quale istanza è quella secondaria. Se il failover ha avuto esito positivo, le due istanze devono avere cambiato ruolo. 

   ![Le istanze gestite hanno cambiato ruolo dopo il failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Passare alla nuova istanza gestita _secondaria_ e selezionare di nuovo **Failover** per eseguire nuovamente l'esito negativo dell'istanza primaria al ruolo primario. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testare il failover del gruppo di failover tramite PowerShell.Test failover of your failover group using PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Individuare l'endpoint del listener

Dopo aver configurato il gruppo di failover, aggiornare la stringa di connessione per l'applicazione all'endpoint del listener. In questo modo l'applicazione verrà mantenuta connessa al listener del gruppo di failover, anziché al database primario, al pool elastico o all'istanza gestita. In questo modo, non è necessario aggiornare manualmente la stringa di connessione ogni volta che si esegue il failover dell'entità del database SQL di Azure e il traffico viene instradato a qualsiasi entità sia attualmente primaria. 

L'endpoint del listener `fog-name.database.windows.net`è nel formato , ed è visibile nel portale di Azure quando si visualizza il gruppo di failover:

![Stringa di connessione del gruppo di failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Osservazioni

- La rimozione di un gruppo di failover per un database singolo o in pool non interrompe la replica e non elimina il database replicato. Sarà necessario arrestare manualmente la replica geografica ed eliminare il database dal server secondario se si desidera aggiungere di nuovo un database singolo o in pool a un gruppo di failover dopo che è stato rimosso. La mancata esecuzione di una delle `The operation cannot be performed due to multiple errors` due operazione può causare un errore simile a quando si tenta di aggiungere il database al gruppo di failover. 


## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata di configurazione di un gruppo di failover, vedere le esercitazioni seguenti:For detailed steps configuring a failover group, see the following tutorials:
- [Aggiungere un singolo database a un gruppo di failoverAdd a single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
- [Aggiungere un pool elastico a un gruppo di failover](sql-database-elastic-pool-failover-group-tutorial.md)
- [Aggiungere istanze gestite a un gruppo di failoverAdd managed instances to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
 
Per una panoramica delle opzioni di disponibilità elevata del database SQL di Azure, vedere gruppi di [replica geografica](sql-database-active-geo-replication.md) e [failover automatico.](sql-database-auto-failover-group.md) 
