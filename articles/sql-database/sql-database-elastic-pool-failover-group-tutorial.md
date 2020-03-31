---
title: 'Esercitazione: Aggiungere un pool elastico a un gruppo di failoverTutorial: Add an elastic pool to a failover group'
description: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.Add an Azure SQL Database elastic pool to a failover group using the Azure portal, PowerShell, or Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268977"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Esercitazione: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failoverTutorial: Add an Azure SQL Database elastic pool to a failover group

Configurare un gruppo di failover per un pool elastico del database SQL di Azure e testare il failover tramite il portale di Azure.Configure a failover group for an Azure SQL Database elastic pool and test failover using the Azure portal.  In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Creare un singolo database del database SQL di Azure.Create an Azure SQL Database single database.
> - Aggiungere il singolo database in un pool elastico. 
> - Creare un gruppo di [failover](sql-database-auto-failover-group.md) per due pool elastici tra due server SQL logici.
> - Testare il failover.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Crea un account gratuito](https://azure.microsoft.com/free/) se non ne hai già uno.


## <a name="1---create-a-single-database"></a>1 - Creare un singolo database 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Aggiungere un singolo database al pool elastico
In questo passaggio verrà creato un pool elastico a cui verrà aggiunto il singolo database. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il pool elastico usando il portale di Azure.Create your elastic pool using the Azure portal. 


1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando Mostra dettagli nel riquadro Database.
1. Selezionare **Pool elastico** dall'elenco a discesa **Tipo di risorsa** nel riquadro Database **SQL.** Selezionare **Crea** per creare il pool elastico. 

    ![Seleziona pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configurare il pool elastico con i valori seguenti:Configure your elastic pool with the following values:
   - **Nome**: Specificare un nome univoco `myElasticPool`per il pool elastico, ad esempio . 
   - **Abbonamento**: Selezionare l'abbonamento dall'elenco a discesa.
   - **ResourceGroup:** `myResourceGroup` selezionare dall'elenco a discesa il gruppo di risorse creato nella sezione 1.ResourceGroup : Select from the drop-down, the resource group you created in section 1. 
   - **Server**: Selezionare il server creato nella sezione 1 dall'elenco a discesa.  

       ![Creare un nuovo server per il pool elasticoCreate new server for elastic pool](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Calcolo e archiviazione:** selezionare **Configura pool elastico** per configurare il calcolo, l'archiviazione e aggiungere il singolo database al pool elastico. Nella scheda **Impostazioni pool** lasciare il valore predefinito Gen5, con 2 vCore e 32 gb. 

1. Nella pagina **Configura** selezionare la scheda **Database** e quindi scegliere **Aggiungi database**. Scegliere il database creato nella sezione 1 e quindi selezionare **Applica** per aggiungerlo al pool elastico. Selezionare di nuovo **Applica** per applicare le impostazioni del pool elastico e chiudere la pagina **Configura.** 

    ![Aggiungere il database SQL al pool elasticoAdd SQL DB to elastic pool](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selezionare **Revisione : crea** per rivedere le impostazioni del pool elastico e quindi selezionare **Crea** per creare il pool elastico. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Creare i pool elastici e il server secondario usando PowerShell.Create your elastic pools and secondary server using PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool di database elastico per un database SQL di Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. | 

---

## <a name="3---create-the-failover-group"></a>3 - Creare il gruppo di failover 
In questo passaggio verrà creato un gruppo di [failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il pool elastico al gruppo di failover. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover usando il portale di Azure.Create your failover group using the Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico creato `myElasticPool`nella sezione precedente, ad esempio . 
1. Nel riquadro **Panoramica** selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.
  
    ![Server aperto per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **Gruppi** di failover nel riquadro **Impostazioni** e quindi **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungere un nuovo gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Nella pagina **Gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea:**
    - **Nome gruppo di**failover: digitare un `failovergrouptutorial`nome univoco per il gruppo di failover, ad esempio . 
    - **Server secondario**: Selezionare l'opzione per *configurare le impostazioni necessarie,* quindi scegliere **Crea un nuovo server.** In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i seguenti valori per il nuovo server secondario, selezionare **Seleziona**. 
        - **Nome server**: digitare un nome univoco `mysqlsecondary`per il server secondario, ad esempio . 
        - **Accesso amministratore server**: Tipo`azureuser`
        - **Password**: Digitare una password complessa che soddisfi i requisiti della password.
        - **Posizione**: Scegliere una posizione dal menu `East US`a discesa, ad esempio . Questa posizione non può essere la stessa posizione del server primario.

       > [!NOTE]
       > Le impostazioni di accesso al server e del firewall devono corrispondere a quelle del server primario. 
    
       ![Creare un server secondario per il gruppo di failoverCreate a secondary server for the failover group](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selezionare **Database all'interno del gruppo,** quindi selezionare il pool elastico creato nella sezione 2. Dovrebbe essere visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi **scegliere OK** per creare il pool elastico nel server secondario. 
        
    ![Aggiungere un pool elastico al gruppo di failoverAdd elastic pool to failover group](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover e quindi selezionare **Crea** per creare il gruppo di failover. L'aggiunta del pool elastico al gruppo di failover avvierà automaticamente il processo di replica geografica.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Creare il gruppo di failover usando PowerShell.Create your failover group using PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
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
   $failoverGroup
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Nuovo-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server logico. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool di database elastico per un database SQL di Azure.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |

---


## <a name="4---test-failover"></a>4 - Failover di test 
In questo passaggio verrà eseguito il failover del gruppo di failover nel server secondario e quindi verrà eseguito il failover tramite il portale di Azure.In this step, you will fail your failover group over to the secondary server, and then failback using the Azure portal. 


# <a name="portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover usando il portale di Azure.Test failover of your failover group using the Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico creato `myElasticPool`nella sezione precedente, ad esempio . 
1. Selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.

    ![Server aperto per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare Gruppi di failover nel riquadro Impostazioni e quindi scegliere il gruppo di failover creato nella sezione 2.Select **Failover groups** under the **Settings** pane and then choose the failover group you created in section 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Esaminare quale server è primario e quale server secondario. 
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover contenente il pool elastico. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario, il server secondario. Se il failover ha esito positivo, i due server devono avere scambiato i ruoli. 
1. Selezionare di nuovo **Failover** per non eseguire nuovamente le impostazioni originali del gruppo di failover. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testare il failover del gruppo di failover tramite PowerShell.Test failover of your failover group using PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Fail your failover group over to the secondary server, and then fail back using the PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |


---

## <a name="clean-up-resources"></a>Pulire le risorse 

Pulire le risorse eliminando il gruppo di risorse. 


# <a name="portal"></a>[Portale](#tab/azure-portal)


1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo** di risorse per eliminare tutte le risorse del gruppo, nonché il gruppo di risorse stesso. 
1. Digitare il nome del `myResourceGroup`gruppo di risorse, , nella casella di testo e quindi selezionare **Elimina** per eliminare il gruppo di risorse. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Pulire le risorse tramite PowerShell.Clean up your resources using PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In questa parte dell'esercitazione viene utilizzato il cmdlet PowerShell seguente:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse | 

---

> [!IMPORTANT]
> Se si vuole mantenere il gruppo di risorse ma eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima che venga rimossa dal gruppo di failover può causare un comportamento imprevedibile. 

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Nuovo-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server logico. | 
| [Nuovo-AzSqlDatabaseNew-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database singolo di Database SQL di Azure. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool di database elastico per un database SQL di Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse | 


# <a name="portal"></a>[Portale](#tab/azure-portal)
Non sono disponibili script per il portale di Azure.There are no scripts available for the Azure portal.

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un pool elastico del database SQL di Azure a un gruppo di failover e è stato testato il failover. Si è appreso come:

> [!div class="checklist"]
> - Creare un singolo database del database SQL di Azure.Create an Azure SQL Database single database.
> - Aggiungere il singolo database in un pool elastico. 
> - Creare un gruppo di [failover](sql-database-auto-failover-group.md) per due pool elastici tra due server SQL logici.
> - Testare il failover.

Passare all'esercitazione successiva per informazioni su come eseguire la migrazione con Servizio Migrazione del database.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la migrazione di SQL Server in un database in pool tramite DMSTutorial: Migrate SQL Server to a pooled database using DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
