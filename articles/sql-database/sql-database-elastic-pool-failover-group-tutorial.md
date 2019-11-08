---
title: 'Esercitazione: aggiungere un pool elastico a un gruppo di failover'
description: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: e2ae9afaf7c1dcc1794b90d4851fdd60298b5ad6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823883"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Esercitazione: aggiungere un pool elastico del database SQL di Azure a un gruppo di failover

Configurare un gruppo di failover per un pool elastico del database SQL di Azure e un failover di test usando il portale di Azure.  In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare un database singolo del database SQL di Azure.
> - Aggiungere il database singolo in un pool elastico. 
> - Creare un [gruppo di failover](sql-database-auto-failover-group.md) per due pool elastici tra due server SQL logici.
> - Failover di test.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.


## <a name="1---create-a-single-database"></a>1-creare un database singolo 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-aggiungere un singolo database al pool elastico
In questo passaggio si creerà un pool elastico e si aggiungerà il singolo database. 


# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Creare il pool elastico usando il portale di Azure. 


1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando Mostra dettagli nel riquadro database.
1. Selezionare **pool elastico** dall'elenco a discesa **tipo di risorsa** nel riquadro **database SQL** . Selezionare **Crea** per creare il pool elastico. 

    ![Seleziona pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configurare il pool elastico con i valori seguenti:
   - **Nome**: specificare un nome univoco per il pool elastico, ad esempio `myElasticPool`. 
   - **Sottoscrizione**: selezionare la sottoscrizione dall'elenco a discesa.
   - **ResourceGroup**: selezionare `myResourceGroup` dall'elenco a discesa, il gruppo di risorse creato nella sezione 1. 
   - **Server**: selezionare il server creato nella sezione 1 dall'elenco a discesa.  

       ![Crea nuovo server per il pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Calcolo e archiviazione**: selezionare **Configura pool elastico** per configurare il calcolo, l'archiviazione e aggiungere il database singolo al pool elastico. Nella scheda **Impostazioni pool** lasciare l'impostazione predefinita quinta generazione, con 2 Vcore e 32 GB. 

1. Nella pagina **Configura** selezionare la scheda **database** , quindi scegliere **Aggiungi database**. Scegliere il database creato nella sezione 1 e quindi fare clic su **applica** per aggiungerlo al pool elastico. Selezionare di nuovo **applica** per applicare le impostazioni del pool elastico e chiudere la pagina **Configura** . 

    ![Aggiungere un database SQL al pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selezionare **Verifica + crea** per esaminare le impostazioni del pool elastico e quindi selezionare **Crea** per creare il pool elastico. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Creare i pool elastici e il server secondario usando PowerShell. 

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

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Consente di creare un pool di database elastici per un database SQL di Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. | 

---

## <a name="3---create-the-failover-group"></a>3-creare il gruppo di failover 
In questo passaggio verrà creato un gruppo di [failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il pool elastico al gruppo di failover. 


# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover usando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico creato nella sezione precedente, ad esempio `myElasticPool`. 
1. Nel riquadro **Panoramica** selezionare il nome del server in **nome server** per aprire le impostazioni per il server.
  
    ![Apri server per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungi nuovo gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea**:
    - **Nome del gruppo di failover**: digitare un nome di gruppo di failover univoco, ad esempio `failovergrouptutorial`. 
    - **Server secondario**: selezionare l'opzione per *configurare le impostazioni necessarie* e quindi scegliere di **creare un nuovo server**. In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i valori seguenti per il nuovo server secondario, selezionare **Seleziona**. 
        - **Nome server**: digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary`. 
        - **Account di accesso amministratore server**: digitare `azureuser`
        - **Password**: digitare una password complessa che soddisfi i requisiti delle password.
        - **Località**: scegliere un percorso dall'elenco a discesa, ad esempio `East US`. Il percorso non può essere uguale a quello del server primario.

       > [!NOTE]
       > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario. 
    
       ![Creazione di un server secondario per il gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selezionare i **database all'interno del gruppo** e quindi selezionare il pool elastico creato nella sezione 2. Verrà visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi fare clic su **OK** per creare il pool elastico nel server secondario. 
        
    ![Aggiungere un pool elastico al gruppo di failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover e quindi selezionare **Crea** per creare il gruppo di failover. L'aggiunta del pool elastico al gruppo di failover avvierà automaticamente il processo di replica geografica.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover usando PowerShell. 

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

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server logico. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Consente di creare un pool di database elastici per un database SQL di Azure.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |

---


## <a name="4---test-failover"></a>4-failover di test 
In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback utilizzando il portale di Azure. 


# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover usando il portale di Azure. 

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **SQL di Azure** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il pool elastico creato nella sezione precedente, ad esempio `myElasticPool`. 
1. Selezionare il nome del server in **nome server** per aprire le impostazioni per il server.

    ![Apri server per pool elastico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover creato nella sezione 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario. 
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover che contiene il pool elastico. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario, il server secondario. Se il failover ha avuto esito positivo, i due server dovrebbero avere ruoli scambiati. 
1. Selezionare di nuovo **failover** per non riuscire a ripristinare le impostazioni originali del gruppo di failover. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Failover di test del gruppo di failover usando PowerShell. 

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

Interrompere il gruppo di failover nel server secondario e quindi eseguire il failback usando PowerShell. 

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

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |


---

## <a name="clean-up-resources"></a>Pulire le risorse 

Pulire le risorse eliminando il gruppo di risorse. 


# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)


1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo di risorse** per eliminare tutte le risorse nel gruppo, nonché il gruppo di risorse stesso. 
1. Digitare il nome del gruppo di risorse, `myResourceGroup`, nella casella di testo e quindi selezionare **Elimina** per eliminare il gruppo di risorse. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pulire le risorse usando PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```
---

Questa parte dell'esercitazione usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse | 

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

## <a name="full-script"></a>Script completo

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server logico. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database singolo di Database SQL di Azure. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Consente di creare un pool di database elastici per un database SQL di Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse | 


# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Non sono disponibili script per la portale di Azure.

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un pool elastico del database SQL di Azure a un gruppo di failover e testato il failover. Si è appreso come:

> [!div class="checklist"]
> - Creare un database singolo del database SQL di Azure.
> - Aggiungere il database singolo in un pool elastico. 
> - Creare un [gruppo di failover](sql-database-auto-failover-group.md) per due pool elastici tra due server SQL logici.
> - Failover di test.

Passare all'esercitazione successiva per informazioni su come eseguire la migrazione con Servizio Migrazione del database.

> [!div class="nextstepaction"]
> [Esercitazione: eseguire la migrazione di SQL Server a un database in pool con DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
