---
title: 'Esercitazione: aggiungere un database a un gruppo di failover'
description: Aggiungere un database nel database SQL di Azure a un gruppo di failover automatico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 53645b6ba9f1463eac14ea974a17c356c1791db6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255314"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Esercitazione: aggiungere un database SQL di Azure a un gruppo di failover automatico
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Un [gruppo di failover](auto-failover-group-overview.md) è un livello di astrazione dichiarativo che consente di raggruppare più database con replica geografica. Informazioni su come configurare un gruppo di failover per un database SQL di Azure e il failover di test usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.  In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Creare un database nel database SQL di Azure
> - Creare un gruppo di failover per il database tra due server.
> - Failover di test.

## <a name="prerequisites"></a>Prerequisiti

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Per completare questa esercitazione, accertarsi di avere:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per completare l'esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Per completare l'esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.
- Versione più recente dell' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest)della riga di comando di Azure.

---

## <a name="1---create-a-database"></a>1-creare un database

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-creare il gruppo di failover

In questo passaggio verrà creato un [gruppo di failover](auto-failover-group-overview.md) tra un server esistente e un nuovo server in un'altra area. Aggiungere quindi il database di esempio al gruppo di failover.

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover e aggiungervi il database usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra del [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il database creato nella sezione 1, ad esempio `mySampleDatabase` .
1. I gruppi di failover possono essere configurati a livello di server. Selezionare il nome del server in **nome server** per aprire le impostazioni per il server.

   ![Apri server per database](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover.

   ![Aggiungi nuovo gruppo di failover](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea**:

   - **Nome del gruppo di failover**: digitare un nome di gruppo di failover univoco, ad esempio `failovergrouptutorial` .
   - **Server secondario**: selezionare l'opzione per *configurare le impostazioni necessarie* e quindi scegliere di **creare un nuovo server**. In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i valori seguenti, selezionare **Seleziona**.
      - **Nome server**: digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary` .
      - **Accesso amministratore server**: tipo`azureuser`
      - **Password**: digitare una password complessa che soddisfi i requisiti delle password.
      - **Località**: scegliere un percorso dall'elenco a discesa, ad esempio `East US` . Il percorso non può essere uguale a quello del server primario.

     > [!NOTE]
     > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario.

     ![Creazione di un server secondario per il gruppo di failover](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Database all'interno del gruppo**: una volta selezionato un server secondario, questa opzione diventa sbloccata. Selezionarlo per **selezionare i database da aggiungere** , quindi scegliere il database creato nella sezione 1. L'aggiunta del database al gruppo di failover avvierà automaticamente il processo di replica geografica.

   ![Aggiungere un database SQL al gruppo di failover](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover e aggiungervi il database usando PowerShell.

   > [!NOTE]
   > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario.

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

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Consente di creare un server nel database SQL di Azure che ospita i database singoli e i pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server nel database SQL di Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Consente di creare un nuovo database singolo nel database SQL di Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Consente di creare un nuovo gruppo di failover nel database SQL di Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database nel database SQL di Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database a un gruppo di failover nel database SQL di Azure. |

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Creare il gruppo di failover e aggiungervi il database usando l'interfaccia della riga di comando di Azure.

   > [!NOTE]
   > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario.

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

Questa parte dell'esercitazione usa i cmdlet dell'interfaccia della riga di comando di Azure seguenti:

| Comando | Note |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crea un server che ospita database e pool elastici. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crea le regole del firewall di un server. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Crea un gruppo di failover. |

---

## <a name="3---test-failover"></a>3-failover di test

In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback usando il portale di Azure.

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Failover di test con il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra del [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **tutti i servizi**, quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il database creato nella sezione 2, ad esempio `mySampleDatbase` .
1. Selezionare il nome del server in **nome server** per aprire le impostazioni per il server.

   ![Apri server per database](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover creato nella sezione 2.
  
   ![Selezionare il gruppo di failover dal portale](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario.
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover che contiene il database di esempio.
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse.

   ![Eseguire il failover del gruppo di failover contenente il database](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Esaminare il server primario e il server secondario. Se il failover ha avuto esito positivo, i due server dovrebbero avere ruoli scambiati.
1. Selezionare di nuovo **failover** per ripristinare i ruoli originali dei server.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Failover di test con PowerShell.

Controllare il ruolo della replica secondaria:

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

Eseguire il failover sul server secondario:

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

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Failover di test con l'interfaccia della riga di comando di Azure

Verificare il server secondario:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Eseguire il failover sul server secondario:

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

Questa parte dell'esercitazione usa i cmdlet dell'interfaccia della riga di comando di Azure seguenti:

| Comando | Note |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Elenca i gruppi di failover in un server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Imposta il server primario del gruppo di failover eseguendo il failover di tutti i database dal server primario corrente. |

---

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse eliminando il gruppo di risorse.

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Eliminare il gruppo di risorse usando il portale di Azure.

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo di risorse** per eliminare tutte le risorse nel gruppo, nonché il gruppo di risorse stesso.
1. Digitare il nome del gruppo di risorse, `myResourceGroup` , nella casella di testo e quindi selezionare **Elimina** per eliminare il gruppo di risorse.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Eliminare il gruppo di risorse usando PowerShell.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Questa parte dell'esercitazione usa i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse |

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Questa parte dell'esercitazione usa i cmdlet dell'interfaccia della riga di comando di Azure seguenti:

| Comando | Note |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

---

> [!IMPORTANT]
> Se si desidera lasciare il gruppo di risorse, ma eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima che venga rimossa dal gruppo di failover può causare un comportamento imprevedibile.

## <a name="full-scripts"></a>Script completi

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Consente di creare un server che ospita i database singoli e i pool elastici nel database SQL di Azure. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server nel database SQL di Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Consente di creare un nuovo database nel database SQL di Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Consente di creare un nuovo gruppo di failover nel database SQL di Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database nel database SQL di Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database a un gruppo di failover nel database SQL di Azure. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover nel database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue un failover di un gruppo di failover nel database SQL di Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse nel database SQL di Azure.|

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Imposta una sottoscrizione come sottoscrizione attiva corrente. |
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Consente di creare un server che ospita i database singoli e i pool elastici nel database SQL di Azure. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crea le regole del firewall IP a livello di server nel database SQL di Azure. |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Consente di creare un database nel database SQL di Azure. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Consente di creare un gruppo di failover nel database SQL di Azure. |
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Elenca i gruppi di failover in un server nel database SQL di Azure. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Imposta il server primario del gruppo di failover eseguendo il failover di tutti i database dal server primario corrente. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

# <a name="the-portal"></a>[Portale](#tab/azure-portal)

Non sono disponibili script per la portale di Azure.

---

Qui è possibile trovare altri script del database SQL di Azure: [Azure PowerShell](powershell-script-content-guide.md) e l'interfaccia della riga di comando di [Azure](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un database nel database SQL di Azure a un gruppo di failover e testato il failover. Si è appreso come:

> [!div class="checklist"]
>
> - Creare un database nel database SQL di Azure
> - Creare un gruppo di failover per il database tra due server.
> - Failover di test.

Passare all'esercitazione successiva su come aggiungere il pool elastico a un gruppo di failover.

> [!div class="nextstepaction"]
> [Esercitazione: aggiungere un pool elastico del database SQL di Azure a un gruppo di failover](failover-group-add-elastic-pool-tutorial.md)
