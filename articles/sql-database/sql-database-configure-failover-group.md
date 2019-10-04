---
title: Configurare un gruppo di failover per il database SQL di Azure
description: Informazioni su come configurare un gruppo di failover automatico per un database singolo di database SQL di Azure, un pool elastico e un'istanza gestita usando il portale di Azure, AZ CLI e PowerShell.
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
ms.openlocfilehash: 9206fd264854cd9e5d8e46473dd60b05a3362fdd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329357"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configurare un gruppo di failover per il database SQL di Azure

Questo argomento illustra come configurare un gruppo di [failover automatico](sql-database-auto-failover-group.md) per un database singolo di database SQL di Azure, un pool elastico e un'istanza gestita usando il portale di Azure o PowerShell. 

## <a name="single-database"></a>Database singolo
Creare il gruppo di failover e aggiungervi un singolo database usando il portale di Azure o PowerShell.

### <a name="prerequisites"></a>Prerequisiti

Considerare i prerequisiti seguenti:

- L'account di accesso del server e le impostazioni del firewall per il server secondario devono corrispondere a quello del server primario. 

### <a name="create-failover-group"></a>Crea gruppo di failover

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover e aggiungervi il database singolo usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il database singolo che si desidera aggiungere al gruppo di failover. 
1. Selezionare il nome del server in **nome server** per aprire le impostazioni per il server.

   ![Apri server per database singolo](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungi nuovo gruppo di failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori richiesti, quindi selezionare **Crea**.

   - **Database all'interno del gruppo**: Scegliere il database che si desidera aggiungere al gruppo di failover. L'aggiunta del database al gruppo di failover avvierà automaticamente il processo di replica geografica. 
        
    ![Aggiungi database SQL al gruppo di failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Creare il gruppo di failover e aggiungervi il database singolo usando PowerShell. 

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

Testare il failover del gruppo di failover usando il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover usando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il database singolo che si desidera aggiungere al gruppo di failover. 

   ![Apri server per database singolo](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover appena creato. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario. 
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover contenente il database singolo. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario e il server secondario. Se il failover ha avuto esito positivo, i due server dovrebbero avere ruoli scambiati. 
1. Selezionare di nuovo **failover** per riportare i server ai ruoli originariamente. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Failover di test del gruppo di failover usando PowerShell.  

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
Eseguire il failover sul server secondario: 

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

## <a name="elastic-pool"></a>Pool elastico
Creare il gruppo di failover e aggiungervi un pool elastico usando il portale di Azure o PowerShell.  

### <a name="prerequisites"></a>Prerequisiti

Considerare i prerequisiti seguenti:

- L'account di accesso del server e le impostazioni del firewall per il server secondario devono corrispondere a quello del server primario. 

### <a name="create-the-failover-group"></a>Creare il gruppo di failover 

Creare il gruppo di failover per il pool elastico usando il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover e aggiungervi il pool elastico usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico che si desidera aggiungere al gruppo di failover. 
1. Nel riquadro **Panoramica** selezionare il nome del server in **nome server** per aprire le impostazioni per il server.
  
    ![Apri server per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungi nuovo gruppo di failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori richiesti, quindi selezionare **Crea**. Creare un nuovo server secondario oppure selezionare un server secondario esistente. 

1. Selezionare i **database all'interno del gruppo** e quindi scegliere il pool elastico che si desidera aggiungere al gruppo di failover. Se un pool elastico non esiste già nel server secondario, viene visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi fare clic su **OK** per creare il pool elastico nel server secondario. 
        
    ![Aggiungere un pool elastico al gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover e quindi selezionare **Crea** per creare il gruppo di failover. L'aggiunta del pool elastico al gruppo di failover avvierà automaticamente il processo di replica geografica. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover e aggiungervi il pool elastico usando PowerShell. 

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

Failover di test del pool elastico usando il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Eseguire il failover del gruppo di failover sul server secondario e quindi eseguire il failback utilizzando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico che si desidera aggiungere al gruppo di failover. 
1. Nel riquadro **Panoramica** selezionare il nome del server in **nome server** per aprire le impostazioni per il server.
  
    ![Apri server per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover creato nella sezione 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario. 
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover che contiene il pool elastico. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario, il server secondario. Se il failover ha avuto esito positivo, i due server dovrebbero avere ruoli scambiati. 
1. Selezionare di nuovo **failover** per non riuscire a ripristinare le impostazioni originali del gruppo di failover. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Failover di test del gruppo di failover usando PowerShell.

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

Eseguire il failover sul server secondario: 

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

## <a name="managed-instance"></a>Istanza gestita

Creare un gruppo di failover tra due istanze gestite usando il portale di Azure o PowerShell. 

Sarà necessario creare un gateway per la rete virtuale di ogni istanza gestita, connettere i due gateway e quindi creare il gruppo di failover.

### <a name="prerequisites"></a>Prerequisiti
Considerare i prerequisiti seguenti:

- L'istanza gestita secondaria deve essere vuota.
- L'intervallo di subnet per la rete virtuale secondaria non deve sovrapporsi all'intervallo di subnet della rete virtuale primaria. 
- Le regole di confronto e il fuso orario dell'istanza secondaria devono corrispondere a quelli dell'istanza primaria. 
- Quando si connettono i due gateway, la **chiave condivisa** deve essere la stessa per entrambe le connessioni. 

### <a name="create-primary-virtual-network-gateway"></a>Creare un gateway di rete virtuale primario 

Creare il gateway di rete virtuale primario con il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Creare il gateway di rete virtuale primario usando il portale di Azure. 

1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la risorsa di **rete virtuale** per l'istanza gestita primaria. 
1. Selezionare **subnet** in **Impostazioni** e quindi selezionare per aggiungere una nuova **subnet del gateway**. Lasciare i valori predefiniti. 

   ![Aggiungi gateway per istanza gestita primaria](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Dopo aver creato il gateway della subnet, selezionare **Crea una risorsa** nel riquadro di spostamento a sinistra e `Virtual network gateway` quindi digitare nella casella di ricerca. Selezionare la risorsa **gateway di rete virtuale** pubblicata da **Microsoft**. 

   ![Creare un nuovo gateway di rete virtuale](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita primaria. 

   La tabella seguente mostra i valori necessari per il gateway per l'istanza gestita primaria:
 
    | **Campo** | Value |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Name** | Nome del gateway di rete virtuale. | 
    | **Region** | Area in cui si trova l'istanza gestita secondaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo di VPN** | Seleziona **basato su Route** |
    | **SKU**| Lasciare il valore `VpnGw1`predefinito di. |
    | **Location**| Il percorso in cui si trova l'istanza gestita secondaria e la rete virtuale secondaria.   |
    | **Rete virtuale**| Selezionare la rete virtuale per l'istanza gestita secondaria. |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome dell'indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP. |
    | &nbsp; | &nbsp; |

1. Lasciare gli altri valori predefiniti e quindi selezionare **Verifica + crea** per esaminare le impostazioni per il gateway di rete virtuale.

   ![Impostazioni del gateway primario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selezionare **Crea** per creare il nuovo gateway di rete virtuale. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gateway di rete virtuale primario usando PowerShell. 

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

### <a name="create-secondary-virtual-network-gateway"></a>Creare un gateway di rete virtuale secondario

Creare il gateway di rete virtuale secondario usando il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Ripetere i passaggi nella sezione precedente per creare la subnet della rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria. 

   La tabella seguente mostra i valori necessari per il gateway per l'istanza gestita secondaria:

   | **Campo** | Value |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita secondaria. |
   | **Name** | Nome del gateway di rete virtuale, ad esempio `secondary-mi-gateway`. | 
   | **Region** | Area in cui si trova l'istanza gestita secondaria. |
   | **Tipo di gateway** | Selezionare **VPN**. |
   | **Tipo di VPN** | Seleziona **basato su Route** |
   | **SKU**| Lasciare il valore `VpnGw1`predefinito di. |
   | **Location**| Il percorso in cui si trova l'istanza gestita secondaria e la rete virtuale secondaria.   |
   | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-secondary`. |
   | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
   | **Nome dell'indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Impostazioni del gateway secondario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gateway di rete virtuale secondario usando PowerShell. 

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
Creare connessioni tra i due gateway usando il portale di Azure o PowerShell. 

È necessario creare due connessioni: la connessione dal gateway primario al gateway secondario e quindi la connessione dal gateway secondario al gateway primario. 

La chiave condivisa utilizzata per entrambe le connessioni deve essere la stessa per ogni connessione. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Creare connessioni tra i due gateway usando il portale di Azure. 

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com) e selezionare il gateway primario creato nel passaggio 4. 
1. Selezionare **connessioni** in **Impostazioni** e quindi selezionare **Aggiungi** per creare una nuova connessione. 

   ![Aggiungere la connessione al gateway primario](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Immettere un nome per la connessione e digitare un valore per la **chiave condivisa**. 
1. Selezionare il **secondo gateway di rete virtuale** e quindi selezionare il gateway per l'istanza gestita secondaria. 

   ![Crea connessione primaria a secondaria](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Selezionare **OK** per aggiungere la nuova connessione gateway primario a secondario.
1. Ripetere questi passaggi per creare una connessione dal gateway dell'istanza gestita secondaria al gateway dell'istanza gestita primaria. 

   ![Creare una connessione secondaria alla connessione primaria](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare connessioni tra i due gateway usando PowerShell. 

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

### <a name="create-the-failover-group"></a>Creare il gruppo di failover 
Creare il gruppo di failover per le istanze gestite usando il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover per le istanze gestite utilizzando portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare l'istanza gestita primaria che si desidera aggiungere al gruppo di failover.  
1. In **Impostazioni**passare a **gruppi di failover istanza** , quindi scegliere **Aggiungi gruppo** per aprire la pagina **gruppo di failover dell'istanza** . 

   ![Aggiungere un gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Nella pagina **gruppo di failover dell'istanza** Digitare il nome del gruppo di failover, quindi scegliere l'istanza gestita secondaria dall'elenco a discesa. Selezionare **Crea** per creare il gruppo di failover. 

   ![Crea gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Una volta completata la distribuzione del gruppo di failover, si tornerà alla pagina del **gruppo di failover** . 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover per le istanze gestite usando PowerShell. 

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

Testare il failover del gruppo di failover usando il portale di Azure o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover usando il portale di Azure. 

1. Passare all'istanza gestita all'interno del [portale di Azure](https://portal.azure.com) e selezionare **gruppi di failover dell'istanza** in impostazioni. 
1. Verificare quale istanza gestita è la primaria e quale istanza gestita è la replica secondaria. 
1. Selezionare **failover** e quindi fare clic su **Sì** nell'avviso relativo alle sessioni TDS disconnesse. 

   ![Eseguire il failover del gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Verificare quale istanza di gestione è la principale e l'istanza secondaria. Se il failover ha avuto esito positivo, le due istanze dovrebbero avere ruoli passati. 

   ![Istanze gestite con ruoli passati dopo il failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Selezionare di nuovo **failover** per eseguire il failback dell'istanza primaria al ruolo primario. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Failover di test del gruppo di failover usando PowerShell. 

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

Una volta configurato il gruppo di failover, aggiornare la stringa di connessione per l'applicazione all'endpoint del listener. In questo modo l'applicazione verrà connessa al listener del gruppo di failover anziché al database primario, al pool elastico o all'istanza gestita. In questo modo, non è necessario aggiornare manualmente la stringa di connessione ogni volta che si verifica il failover dell'entità del database SQL di Azure e il traffico viene indirizzato a qualsiasi entità attualmente primaria. 

Il formato dell'endpoint del listener è `fog-name.database.windows.net` ed è visibile nell'portale di Azure, quando si Visualizza il gruppo di failover:

![Stringa di connessione del gruppo di failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Note

- La rimozione di un gruppo di failover per un database singolo o in pool non interrompe la replica e non elimina il database replicato. È necessario arrestare manualmente la replica geografica ed eliminare il database dal server secondario se si desidera aggiungere di nuovo un database singolo o in pool a un gruppo di failover dopo che è stato rimosso. Se non si riesce a eseguire alcuna operazione, è possibile che venga generato un errore simile a `The operation cannot be performed due to multiple errors` quando si tenta di aggiungere il database al gruppo di failover. 


## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata di configurazione di un gruppo di failover, vedere le esercitazioni seguenti:
- [Aggiungere un singolo database a un gruppo di failover](sql-database-single-database-failover-group-tutorial.md)
- [Aggiungere un pool elastico a un gruppo di failover](sql-database-elastic-pool-failover-group-tutorial.md)
- [Aggiungere istanze gestite a un gruppo di failover](sql-database-managed-instance-failover-group-tutorial.md)
 
Per una panoramica delle opzioni di disponibilità elevata del database SQL di Azure, vedere la [replica geografica](sql-database-active-geo-replication.md) e i [gruppi di failover automatico](sql-database-auto-failover-group.md). 
