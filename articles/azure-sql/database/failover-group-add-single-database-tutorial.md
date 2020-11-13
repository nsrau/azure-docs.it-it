---
title: 'Esercitazione: Aggiungere un database a un gruppo di failover'
description: Aggiungere un database SQL di Azure a un gruppo di failover automatico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/19/2019
ms.openlocfilehash: 8298c673ddc707130d0873f686e1baed3677a46f
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593966"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Esercitazione: Aggiungere un database SQL di Azure a un gruppo di failover automatico
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Un [gruppo di failover](auto-failover-group-overview.md) è un livello di astrazione dichiarativo che consente di raggruppare più database con replica geografica. Questa esercitazione illustra come configurare un gruppo di failover per un database SQL di Azure e testare il failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.  In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Creare un database nel database SQL di Azure
> - Creare un gruppo di failover per il database tra due server.
> - Testare il failover.

## <a name="prerequisites"></a>Prerequisiti

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Per completare questa esercitazione, accertarsi di avere:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/), se non ne è già disponibile uno.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per completare questa esercitazione, verificare di avere gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/), se non ne è già disponibile uno.
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Per completare questa esercitazione, verificare di avere gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/), se non ne è già disponibile uno.
- L'ultima versione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

---

## <a name="1---create-a-database"></a>1 - Creare un database

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Creare il gruppo di failover

In questo passaggio verrà creato un [gruppo di failover](auto-failover-group-overview.md) tra un server esistente e un nuovo server in un'altra area. Aggiungere quindi il database di esempio al gruppo di failover.

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover e aggiungervi il database tramite il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il database creato nella sezione 1, ossia `mySampleDatabase`.
1. I gruppi di failover possono essere configurati a livello di server. In **Nome del server** selezionare il nome del server per aprire le impostazioni del server.

   ![Aprire il server per il database](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover.

   ![Aggiungere un nuovo gruppo di failover](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **Gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea** :

   - **Nome del gruppo di failover** : digitare un nome univoco per il gruppo di failover, ad esempio `failovergrouptutorial`.
   - **Server secondario** : selezionare *Configura le impostazioni obbligatorie* e quindi scegliere **Crea un nuovo server**. In alternativa, è possibile selezionare un server già esistente come server secondario. Dopo aver immesso i valori seguenti, selezionare **Seleziona**.
      - **Nome server** : digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary`.
      - **Account di accesso amministratore server** : Digitare `azureuser`.
      - **Password** : Digitare una password complessa che soddisfi i corrispondenti requisiti.
      - **Località** : scegliere una località dall'elenco a discesa, ad esempio `East US`. Questa località non può corrispondere a quella del server primario.

     > [!NOTE]
     > Le impostazioni di accesso e del firewall del server devono corrispondere a quelle del server primario.

     ![Creare un server secondario per il gruppo di failover](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Database nel gruppo** : dopo aver selezionato un server secondario, questa opzione viene sbloccata. Selezionarla per attivare **Selezionare i database da aggiungere** e quindi scegliere il database creato nella sezione 1. Aggiungendo il database al gruppo di failover viene avviato automaticamente il processo di replica geografica.

   ![Aggiungere il database SQL al gruppo di failover](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover e aggiungervi il database tramite PowerShell.

   > [!NOTE]
   > Le impostazioni di accesso e del firewall del server devono corrispondere a quelle del server primario.

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

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

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server nel database SQL di Azure che ospita database singoli e pool di database elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server nel database SQL di Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database singolo nel database SQL di Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover nel database SQL di Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database nel database SQL di Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database a un gruppo di failover nel database SQL di Azure. |

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Creare il gruppo di failover e aggiungervi il database tramite l'interfaccia della riga di comando di Azure.

   > [!NOTE]
   > Le impostazioni di accesso e del firewall del server devono corrispondere a quelle del server primario.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

In questa parte dell'esercitazione si usano i cmdlet dell'interfaccia della riga di comando di Azure seguenti:

| Comando | Note |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crea un server che ospita database e pool elastici. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crea le regole del firewall di un server. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create&preserve-view=true) | Crea un gruppo di failover. |

---

## <a name="3---test-failover"></a>3 - Testare il failover

In questo passaggio si eseguirà il failover del gruppo di failover nel server secondario e quindi il failback usando il portale di Azure.

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Testare il failover con il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il database creato nella sezione 2, ossia `mySampleDatbase`.
1. In **Nome del server** selezionare il nome del server per aprire le impostazioni del server.

   ![Aprire il server per il database](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare il gruppo di failover creato nella sezione 2.
  
   ![Selezionare il gruppo di failover nel portale](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Verificare qual è il server primario e quale quello secondario.
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover che contiene il database di esempio.
1. Selezionare **Sì** nella finestra dell'avviso che informa che le sessioni TDS verranno disconnesse.

   ![Failover del gruppo di failover contenente il database](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Verificare quale server è ora il server primario e quale quello secondario. Se il failover è riuscito, i due server dovrebbero essersi scambiati i ruoli.
1. Selezionare di nuovo **Failover** per ripristinare i ruoli originali dei server.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testare il failover con PowerShell.

Verificare il ruolo della replica secondaria:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Effettuare il failover nel server secondario:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

Ripristinare il gruppo di failover nel server primario:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Testare il failover con l'interfaccia della riga di comando di Azure.

Verificare quale server è il server secondario:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Effettuare il failover nel server secondario:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Ripristinare il gruppo di failover nel server primario:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

In questa parte dell'esercitazione si usano i cmdlet dell'interfaccia della riga di comando di Azure seguenti:

| Comando | Note |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list&preserve-view=true) | Elenca i gruppi di failover in un server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary&preserve-view=true) | Imposta il server primario del gruppo di failover eseguendo il failover di tutti i database dal server primario corrente. |

---

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse, eliminare il gruppo di risorse.

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Eliminare il gruppo di risorse tramite il portale di Azure.

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo di risorse** per eliminare tutte le risorse nel gruppo, compreso il gruppo di risorse stesso.
1. Nella casella di testo digitare il nome del gruppo di risorse, `myResourceGroup`, quindi selezionare **Elimina** per eliminare il gruppo di risorse.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Eliminare il gruppo di risorse mediante PowerShell.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In questa parte dell'esercitazione si usano i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse |

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Eliminare il gruppo di risorse tramite l'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

In questa parte dell'esercitazione si usano i cmdlet dell'interfaccia della riga di comando di Azure seguenti:

| Comando | Note |
|---|---|
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

---

> [!IMPORTANT]
> Se si vuole mantenere il gruppo di risorse ma eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima della rimozione dal gruppo di failover può causare comportamenti imprevisti.

## <a name="full-scripts"></a>Script completi

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server che ospita database singoli e pool di database elastici nel database SQL di Azure. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server nel database SQL di Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database nel database SQL di Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover nel database SQL di Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database nel database SQL di Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database a un gruppo di failover nel database SQL di Azure. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover nel database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover nel database SQL di Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse nel database SQL di Azure.|

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true) | Imposta una sottoscrizione come sottoscrizione attiva corrente. |
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crea un server che ospita database singoli e pool di database elastici nel database SQL di Azure. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crea le regole del firewall IP a livello di server nel database SQL di Azure. |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest&preserve-view=true) | Crea un database nel database SQL di Azure. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create&preserve-view=true) | Crea un gruppo di failover nel database SQL di Azure. |
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list&preserve-view=true) | Elenca i gruppi di failover in un server nel database SQL di Azure. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary&preserve-view=true) | Imposta il server primario del gruppo di failover eseguendo il failover di tutti i database dal server primario corrente. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Non sono disponibili script per il portale di Azure.

---

È possibile trovare altri script del database SQL di Azure qui: [Azure PowerShell](powershell-script-content-guide.md) e [Interfaccia della riga di comando di Azure](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un database SQL di Azure a un gruppo di failover ed è stato testato il failover. Si è appreso come:

> [!div class="checklist"]
>
> - Creare un database nel database SQL di Azure
> - Creare un gruppo di failover per il database tra due server.
> - Testare il failover.

Passare all'esercitazione successiva che illustra come aggiungere il pool di database elastico a un gruppo di failover.

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un pool elastico di database SQL di Azure a un gruppo di failover](failover-group-add-elastic-pool-tutorial.md)