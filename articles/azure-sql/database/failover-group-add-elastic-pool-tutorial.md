---
title: 'Esercitazione: Aggiungere un pool elastico a un gruppo di failover'
description: Aggiungere un pool elastico di database SQL di Azure a un gruppo di failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 08/27/2019
ms.openlocfilehash: a9f5bac475fd019b294f79abf0acdfaff198f52b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442757"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Esercitazione: Aggiungere un pool elastico di database SQL di Azure a un gruppo di failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configurare un gruppo di failover per un pool elastico di database SQL di Azure e testare il failover usando il portale di Azure.  In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un database singolo.
> - Aggiungere il database a un pool elastico.
> - Creare un [gruppo di failover](auto-failover-group-overview.md) per due pool elastici tra due server.
> - Testare il failover.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/), se non ne è già disponibile uno.

## <a name="1---create-a-single-database"></a>1 - Creare un database singolo

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 - Aggiungere il database a un pool elastico

In questo passaggio si creerà un pool elastico, a cui si aggiungerà il database.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il pool elastico tramite il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare "Azure SQL" nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui diversi database selezionando Mostra dettagli nel riquadro Database.
1. Selezionare **Pool elastico** dall'elenco a discesa **Tipo di risorsa** nel riquadro **Database SQL**. Selezionare **Crea** per creare il pool elastico.

    ![Selezionare il pool elastico](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Configurare il pool elastico con i valori seguenti:
   - **Nome**: specificare un nome univoco per il pool elastico, ad esempio `myElasticPool`.
   - **Sottoscrizione** Selezionare la sottoscrizione dall'elenco a discesa.
   - **Gruppo di risorse**: nell'elenco a discesa selezionare `myResourceGroup`, il gruppo di risorse creato nella sezione 1.
   - **Server**: nell'elenco a discesa selezionare il server creato nella sezione 1.  

       ![Creare un nuovo server per il pool elastico](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Calcolo e archiviazione**: selezionare **Configura pool elastico** per configurare le risorse di calcolo e archiviazione e aggiungere il database singolo al pool elastico. Nella scheda **Impostazioni pool** lasciare il valore predefinito Gen5, con 2 vCore e 32 GB.

1. Nella pagina **Configura** selezionare la scheda **Database** e quindi scegliere **Aggiungi database**. Selezionare il database creato nella sezione 1 e quindi selezionare **Applica** per aggiungerlo al pool elastico. Selezionare di nuovo **Applica** per applicare le impostazioni del pool elastico e chiudere la pagina **Configura**.

    ![Aggiungere il database al pool elastico](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Selezionare **Rivedi e crea** per verificare le impostazioni del pool elastico e quindi selezionare **Crea** per creare il pool elastico.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il pool elastico e il server secondario usando PowerShell.

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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool di database elastico per un database SQL di Azure.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. |

---

## <a name="3---create-the-failover-group"></a>3 - Creare il gruppo di failover

In questo passaggio si creerà un [gruppo di failover](auto-failover-group-overview.md) tra un server esistente e un nuovo server in un'altra area. Aggiungere quindi il pool elastico al gruppo di failover.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover con il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il pool elastico creato nella sezione precedente, ossia `myElasticPool`.
1. Nel riquadro **Panoramica** selezionare il nome del server in **Nome del server** per aprire le impostazioni del server.
  
    ![Aprire il server per il pool elastico](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover.

    ![Aggiungere un nuovo gruppo di failover](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. Nella pagina **Gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea**:
    - **Nome del gruppo di failover**: digitare un nome univoco per il gruppo di failover, ad esempio `failovergrouptutorial`.
    - **Server secondario**: selezionare *Configura le impostazioni obbligatorie* e quindi scegliere **Crea un nuovo server**. In alternativa, è possibile selezionare un server già esistente come server secondario. Dopo aver immesso i valori seguenti per il nuovo server secondario, selezionare **Seleziona**.
        - **Nome server**: digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary`.
        - **Account di accesso amministratore server**: Digitare `azureuser`.
        - **Password**: Digitare una password complessa che soddisfi i corrispondenti requisiti.
        - **Località**: scegliere una località dall'elenco a discesa, ad esempio `East US`. Questa località non deve corrispondere a quella del server primario.

       > [!NOTE]
       > Le impostazioni di accesso e del firewall del server devono corrispondere a quelle del server primario.

       ![Creare un server secondario per il gruppo di failover](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Selezionare **Database nel gruppo** quindi selezionare il pool elastico creato nella sezione 2. Dovrebbe essere visualizzato un avviso in cui viene chiesto di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi scegliere **OK** per creare il pool elastico nel server secondario. 

   ![Aggiungere il pool elastico al gruppo di failover](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover, quindi selezionare **Crea** per creare il gruppo di failover. Aggiungendo il pool elastico al gruppo di failover viene avviato automaticamente il processo di replica geografica.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover con PowerShell.

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
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server che ospita database e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool elastico per un database SQL di Azure.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |

---

## <a name="4---test-failover"></a>4 - Testare il failover

In questo passaggio verrà eseguito il failover del gruppo di failover nel server secondario e quindi il failback usando il portale di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover con il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il pool elastico creato nella sezione precedente, ossia `myElasticPool`.
1. In **Nome del server** selezionare il nome del server per aprire le impostazioni del server.

    ![Aprire il server per il pool elastico](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare il gruppo di failover creato nella sezione 2.
  
   ![Selezionare il gruppo di failover nel portale](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Verificare qual è il server primario e quale quello secondario.
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover che contiene il pool elastico.
1. Selezionare **Sì** nella finestra dell'avviso che informa che le sessioni TDS verranno disconnesse.

   ![Failover del gruppo di failover contenente il database](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Verificare qual è il server primario e quale quello secondario. Se il failover è riuscito, i due server dovrebbero essersi scambiati i ruoli.
1. Selezionare di nuovo **Failover** per ripristinare le impostazioni originali del gruppo di failover.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testare il failover del gruppo di failover con PowerShell.

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

Si eseguirà il failover del gruppo di failover nel server secondario e quindi il failback usando PowerShell.

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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |

---

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse, eliminare il gruppo di risorse.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo di risorse** per eliminare tutte le risorse nel gruppo, compreso il gruppo di risorse stesso.
1. Nella casella di testo digitare il nome del gruppo di risorse, `myResourceGroup`, quindi selezionare **Elimina** per eliminare il gruppo di risorse.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pulire le risorse mediante PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In questa parte dell'esercitazione si usa il cmdlet di PowerShell seguente:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse |

---

> [!IMPORTANT]
> Se si vuole mantenere il gruppo di risorse ma eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima della rimozione dal gruppo di failover può causare comportamenti imprevisti.

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server che ospita database e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un database. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool di database elastico per un database SQL di Azure.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database nel database SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse |

# <a name="portal"></a>[Portale](#tab/azure-portal)

Non sono disponibili script per il portale di Azure.

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un pool elastico di database SQL di Azure a un gruppo di failover ed è stato testato il failover. Si è appreso come:

> [!div class="checklist"]
>
> - Creare un database singolo.
> - Aggiungere il database in un pool elastico.
> - Creare un [gruppo di failover](auto-failover-group-overview.md) per due pool elastici tra due server.
> - Testare il failover.

Passare all'esercitazione successiva per informazioni su come eseguire la migrazione con Servizio Migrazione del database.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la migrazione di SQL Server in un database in pool mediante il servizio Migrazione del database](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
