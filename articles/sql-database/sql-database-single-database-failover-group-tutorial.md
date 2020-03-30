---
title: 'Esercitazione: Aggiungere un singolo database a un gruppo di failoverTutorial: Add a single database to a failover group'
description: Aggiungere un singolo database del database SQL di Azure a un gruppo di failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.Add an Azure SQL Database single database to a failover group using the Azure portal, PowerShell, or Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061700"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Esercitazione: Aggiungere un singolo database del database SQL di Azure a un gruppo di failoverTutorial: Add an Azure SQL Database single database to a failover group

Un gruppo di [failover](sql-database-auto-failover-group.md) è un livello di astrazione dichiarativa che consente di raggruppare più database con replica geografica. Informazioni su come configurare un gruppo di failover per un singolo database del database SQL di Azure e testare il failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.Learn to configure a failover group for an Azure SQL Database single database and test failover using either the Azure portal, PowerShell, or Azure CLI.  In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Creare un singolo database del database SQL di Azure.Create an Azure SQL Database single database.
> - Creare un gruppo di failover per un singolo database tra due server SQL logici.
> - Testare il failover.

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Crea un account gratuito](https://azure.microsoft.com/free/) se non ne hai già uno.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Per completare l'esercitazione, assicurarsi di disporre dei seguenti elementi:

- Una sottoscrizione di Azure. [Crea un account gratuito](https://azure.microsoft.com/free/) se non ne hai già uno.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)
Per completare l'esercitazione, assicurarsi di disporre dei seguenti elementi:

- Una sottoscrizione di Azure. [Crea un account gratuito](https://azure.microsoft.com/free/) se non ne hai già uno.
- La versione più recente [dell'interfaccia della riga](/cli/azure/install-azure-cli?view=azure-cli-latest)di comando di Azure. 

---

## <a name="1---create-a-single-database"></a>1 - Creare un singolo database 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Creare il gruppo di failover 
In questo passaggio verrà creato un gruppo di [failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il database di esempio al gruppo di failover. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover e aggiungervi il singolo database usando il portale di Azure.Create your failover group and add your single database to it using the Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il singolo database creato `mySampleDatabase`nella sezione 1, ad esempio . 
1. I gruppi di failover possono essere configurabili a livello di server. Selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.

   ![Server aperto per singolo database](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selezionare **Gruppi** di failover nel riquadro **Impostazioni** e quindi **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungere un nuovo gruppo di failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **Gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea:**
    - **Nome gruppo di**failover: digitare un `failovergrouptutorial`nome univoco per il gruppo di failover, ad esempio . 
    - **Server secondario**: Selezionare l'opzione per *configurare le impostazioni necessarie,* quindi scegliere **Crea un nuovo server.** In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i seguenti valori, selezionare **Seleziona**. 
        - **Nome server**: digitare un nome univoco `mysqlsecondary`per il server secondario, ad esempio . 
        - **Accesso amministratore server**: Tipo`azureuser`
        - **Password**: Digitare una password complessa che soddisfi i requisiti della password.
        - **Posizione**: Scegliere una posizione dal menu `East US`a discesa, ad esempio . Questa posizione non può essere la stessa posizione del server primario.

    > [!NOTE]
    > Le impostazioni di accesso al server e del firewall devono corrispondere a quelle del server primario. 
    
      ![Creare un server secondario per il gruppo di failoverCreate a secondary server for the failover group](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Database all'interno del gruppo:** una volta selezionato un server secondario, questa opzione viene sbloccata. Selezionarla in **Selezionare** i database da aggiungere e quindi scegliere il database creato nella sezione 1. L'aggiunta del database al gruppo di failover avvierà automaticamente il processo di replica geografica. 
        
    ![Aggiungere il database SQL al gruppo di failoverAdd SQL DB to failover group](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Creare il gruppo di failover e aggiungervi il singolo database tramite PowerShell.Create your failover group and add your single database to it using PowerShell. 

   > [!NOTE]
   > Le impostazioni di accesso al server e del firewall devono corrispondere a quelle del server primario. 

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
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
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

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Nuovo-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server logico. | 
| [Nuovo-AzSqlDatabaseNew-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database singolo di Database SQL di Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)
Creare il gruppo di failover e aggiungervi il singolo database usando l'interfaccia della riga di comando. 

   > [!NOTE]
   > Le impostazioni di accesso al server e del firewall devono corrispondere a quelle del server primario. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet dell'interfaccia della riga di comando Az seguenti:

| Comando | Note |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crea le regole del firewall di un server. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Crea un gruppo di failover. | 

---

## <a name="3---test-failover"></a>3 - Failover di test 
In questo passaggio verrà eseguito il failover del gruppo di failover nel server secondario e quindi verrà eseguito il failover tramite il portale di Azure.In this step, you will fail your failover group over to the secondary server, and then failback using the Azure portal. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Testare il failover usando il portale di Azure.Test failover using the Azure portal. 

1. Selezionare **SQL di Azure** nel menu a sinistra del portale di [Azure.](https://portal.azure.com) Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare il singolo database creato nella `mySampleDatbase`sezione 2, ad esempio . 
1. Selezionare il nome del server in **Nome server** per aprire le impostazioni per il server.

   ![Server aperto per singolo database](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selezionare Gruppi di failover nel riquadro Impostazioni e quindi scegliere il gruppo di failover creato nella sezione 2.Select **Failover groups** under the **Settings** pane and then choose the failover group you created in section 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Esaminare quale server è primario e quale server secondario. 
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover contenente il singolo database di esempio. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Esaminare quale server è ora primario e quale server è secondario. Se il failover ha esito positivo, i due server devono avere scambiato i ruoli. 
1. Selezionare di nuovo **Failover** per eseguire il failover dei server ai ruoli originari. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Testare il failover tramite PowerShell.Test failover using PowerShell. 


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

Eseguire il failover al server secondario: 

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

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |



# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)
Testare il failover utilizzando l'interfaccia della riga di comando. 

Verificare quale server è secondario:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Eseguire il failover al server secondario: 

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

In questa parte dell'esercitazione vengono utilizzati i cmdlet dell'interfaccia della riga di comando Az seguenti:

| Comando | Note |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Elenca i gruppi di failover in un server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Imposta il server primario del gruppo di failover eseguendo il failover di tutti i database dal server primario corrente. | 

---

## <a name="clean-up-resources"></a>Pulire le risorse 
Pulire le risorse eliminando il gruppo di risorse. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Eliminare il gruppo di risorse tramite il portale di Azure.Delete the resource group using the Azure portal. 

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo** di risorse per eliminare tutte le risorse del gruppo, nonché il gruppo di risorse stesso. 
1. Digitare il nome del `myResourceGroup`gruppo di risorse, , nella casella di testo e quindi selezionare **Elimina** per eliminare il gruppo di risorse.  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Eliminare il gruppo di risorse tramite PowerShell.Delete the resource group using PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet di PowerShell seguenti:

| Comando | Note |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse | 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Eliminare il gruppo di risorse tramite l'interfaccia della riga di comando. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

In questa parte dell'esercitazione vengono utilizzati i cmdlet dell'interfaccia della riga di comando Az seguenti:

| Comando | Note |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

---


> [!IMPORTANT]
> Se si vuole mantenere il gruppo di risorse ma eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima che venga rimossa dal gruppo di failover può causare un comportamento imprevedibile. 


## <a name="full-scripts"></a>Script completi

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Nuovo-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall per un server logico. | 
| [Nuovo-AzSqlDatabaseNew-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database singolo di Database SQL di Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database SQL di Azure a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database SQL di Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database SQL di Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse | 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Imposta una sottoscrizione come sottoscrizione attiva corrente. | 
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crea le regole del firewall di un server. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Crea un database. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Crea un gruppo di failover. | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Elenca i gruppi di failover in un server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Imposta il server primario del gruppo di failover eseguendo il failover di tutti i database dal server primario corrente. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

# <a name="portal"></a>[Portale](#tab/azure-portal)
Non sono disponibili script per il portale di Azure.There are no scripts available for the Azure portal. 
 
---

È possibile trovare altri script di database SQL di Azure qui: Azure PowerShell e [l'interfaccia della](sql-database-cli-samples.md)riga di comando di Azure.You can find other Azure SQL Database scripts here: [Azure PowerShell](sql-database-powershell-samples.md) and Azure CLI . 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un singolo database del database SQL di Azure a un gruppo di failover e è stato testato il failover. Si è appreso come: 

> [!div class="checklist"]
> - Creare un singolo database del database SQL di Azure.Create an Azure SQL Database single database. 
> - Creare un gruppo di [failover](sql-database-auto-failover-group.md) per un singolo database tra due server SQL logici.
> - Testare il failover.

Passare all'esercitazione successiva su come aggiungere il pool elastico a un gruppo di failover. 

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failoverTutorial: Add an Azure SQL Database elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
