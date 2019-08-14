---
title: 'Esercitazione: Aggiungere un database singolo di database SQL di Azure a un gruppo di failover | Microsoft Docs'
description: Aggiungere un database singolo di database SQL di Azure a un gruppo di failover usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: 6cf688750ac73763c7f0da4eea152cf6bf0c8285
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935009"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Esercitazione: Aggiungere un database singolo di database SQL di Azure a un gruppo di failover

Configurare un gruppo di failover per un database singolo di database SQL di Azure e un failover di test usando l'interfaccia della riga di comando di portale di Azure, PowerShell o Azure.  In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare un database singolo del database SQL di Azure.
> - Creazione di un [gruppo di failover](sql-database-auto-failover-group.md) per un singolo database tra due server SQL logici.
> - Failover di test.

## <a name="prerequisites"></a>Prerequisiti

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Per completare l'esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per completare l'esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. [Creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno.
- Versione più recente dell' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest)della riga di comando di Azure. 

---

## <a name="1---create-a-single-database"></a>1-creare un database singolo 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-creare il gruppo di failover 
In questo passaggio verrà creato un gruppo di [failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il database di esempio al gruppo di failover. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Creare il gruppo di failover e aggiungervi il database singolo usando il portale di Azure. 


1. Selezionare **tutti i servizi** nell'angolo superiore sinistro della [portale di Azure](https://portal.azure.com). 
1. Digitare `sql servers` nella casella di ricerca. 
1. Opzionale Selezionare l'icona a stella accanto a SQL Server per aggiungere i **server SQL** ai Preferiti e aggiungerla al riquadro di spostamento a sinistra. 
    
    ![Individuare i server SQL](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Selezionare **SQL Server** e scegliere il server creato nella sezione 1, ad esempio `mysqlserver`.
1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover. 

    ![Aggiungi nuovo gruppo di failover](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Nella pagina **gruppo di failover** immettere o selezionare i valori seguenti e quindi selezionare **Crea**:
    - **Nome del gruppo di failover**: Digitare un nome di gruppo di failover univoco, ad `failovergrouptutorial`esempio. 
    - **Server secondario**: Selezionare l'opzione per *configurare le impostazioni necessarie* e quindi scegliere di **creare un nuovo server**. In alternativa, è possibile scegliere un server già esistente come server secondario. Dopo aver immesso i valori seguenti, selezionare **Seleziona**. 
        - **Nome server**: Digitare un nome univoco per il server secondario, ad esempio `mysqlsecondary`. 
        - **Account di accesso amministratore server**: Tipo`azureuser`
        - **Password**: Digitare una password complessa che soddisfi i corrispondenti requisiti.
        - **Località**: Scegliere un percorso dall'elenco a discesa, ad esempio Stati Uniti orientali 2. Il percorso non può essere uguale a quello del server primario.

    > [!NOTE]
    > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario. 
    
      ![Creazione di un server secondario per il gruppo di failover](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **Database all'interno del gruppo**: Una volta selezionato un server secondario, questa opzione diventa sbloccata. Selezionarlo per **selezionare i database da aggiungere** , quindi scegliere il database creato nella sezione 1. L'aggiunta del database al gruppo di failover avvierà automaticamente il processo di replica geografica. 
        
    ![Aggiungi database SQL al gruppo di failover](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Creare il gruppo di failover e aggiungervi il database singolo usando PowerShell. 

   > [!NOTE]
   > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

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

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Creare il gruppo di failover e aggiungervi il singolo database usando AZ CLI. 

   > [!NOTE]
   > L'account di accesso del server e le impostazioni del firewall devono corrispondere a quelle del server primario. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3-failover di test 
In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback utilizzando il portale di Azure. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Failover di test con il portale di Azure. 

1. Passare al server **SQL Server** all'interno del [portale di Azure](https://portal.azure.com). 
1. Selezionare **gruppi di failover** nel riquadro **Impostazioni** , quindi scegliere il gruppo di failover creato nella sezione 2. 
  
   ![Selezionare il gruppo di failover dal portale](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Esaminare il server primario e il server secondario. 
1. Selezionare **failover** dal riquadro attività per eseguire il failover del gruppo di failover contenente il database singolo di esempio. 
1. Selezionare **Sì** nell'avviso che informa che le sessioni TDS verranno disconnesse. 

   ![Eseguire il failover del gruppo di failover contenente il database SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Esaminare il server primario e il server secondario. Se il failover ha esito positivo, i due server dovrebbero avere ruoli scambiati. 
1. Selezionare di nuovo **failover** per riportare i server ai ruoli originariamente. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Failover di test con l'interfaccia della riga di comando AZ. 


Verificare il server secondario:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Eseguire il failover sul server secondario: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Ripristinare il gruppo di failover nel server primario:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>Pulire le risorse 
Pulire le risorse eliminando il gruppo di risorse. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Eliminare il gruppo di risorse usando il portale di Azure. 


1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Elimina gruppo di risorse** per eliminare tutte le risorse nel gruppo, nonché il gruppo di risorse stesso. 
1. Digitare il nome del gruppo di risorse, `myResourceGroup`, nella casella di testo e quindi selezionare **Elimina** per eliminare il gruppo di risorse.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Eliminare il gruppo di risorse usando PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Eliminare il gruppo di risorse usando AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>Script completi

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Non sono disponibili script per la portale di Azure.
 
---

Qui è possibile trovare altri script del database SQL di Azure: [Azure PowerShell](sql-database-powershell-samples.md) e l'interfaccia della riga di comando di [Azure](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un database singolo di database SQL di Azure a un gruppo di failover e testato il failover. Si è appreso come:

> [!div class="checklist"]
> - Creare un database singolo del database SQL di Azure. 
> - Creazione di un [gruppo di failover](sql-database-auto-failover-group.md) per un singolo database tra due server SQL logici.
> - Failover di test.

Passare all'esercitazione successiva su come aggiungere il pool elastico a un gruppo di failover. 

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un pool elastico del database SQL di Azure a un gruppo di failover](sql-database-elastic-pool-failover-group-tutorial.md)
