---
title: Distribuire pacchetti SSIS in Azure | Microsoft Docs
description: Questo articolo illustra come distribuire pacchetti SSIS nel runtime di integrazione SSIS di Azure disponibile in Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 10/06/2017
ms.author: spelluru
ms.openlocfilehash: 350c7784da1abb24df4ccd292cad28f73f3f8c0c
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Distribuire pacchetti SQL Server Integration Services in Azure
Questa esercitazione illustra la procedura di provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory. È quindi possibile usare SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire pacchetti SQL Server Integration Services (SSIS) in questo runtime in Azure. In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione SSIS di Azure
> * Avviare il runtime di integrazione SSIS di Azure
> * Distribuire pacchetti SSIS
> * Rivedere lo script completo

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. Per informazioni concettuali in proposito, vedere la [panoramica del runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

## <a name="prerequisites"></a>prerequisiti
- **Server di database SQL di Azure**. Se non si ha già un server di database, crearne uno nel portale di Azure prima di iniziare. Questo server ospita il database del catalogo SSIS (SSISDB). È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure. 
    - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia impostata su **SÌ** per il server di database. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Per abilitare questa impostazione usando PowerShell, vedere [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](../sql-database/sql-database-firewall-configure.md). 
- **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell). PowerShell viene usato per eseguire uno script per il provisioning di un runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS nel cloud. 

> [!NOTE]
> Per un elenco delle aree supportate da Azure Data Factory V2 e da Azure-SSIS Integration Runtime, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/). Espandere **Dati e analisi** per vedere **Data Factory V2** e **SSIS Integration Runtime**.

## <a name="launch-windows-powershell-ise"></a>Avviare Windows PowerShell ISE
Avviare **Windows PowerShell ISE** con privilegi amministrativi. 

## <a name="create-variables"></a>Creare le variabili
Copiare e incollare lo script seguente, specificando i valori per le variabili. Per un elenco di **piani tariffari** supportati per il database SQL di Azure, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>Convalidare la connessione al database
Aggiungere lo script seguente per convalidare il server di database SQL di Azure server.database.windows.net. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

Per creare un database SQL di Azure come parte dello script, vedere l'esempio seguente: 

Impostare i valori per le variabili che non sono già state definite. Ad esempio: FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>Eseguire l'accesso e selezionare la sottoscrizione
Aggiungere il codice seguente allo script per eseguire l'accesso e selezionare la sottoscrizione di Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

Se il gruppo di risorse esiste già, non copiare questo codice nello script. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
Eseguire questo comando per creare una data factory:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Creare un runtime di integrazione
Eseguire questo comando per creare un runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS in Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>Avviare il runtime di integrazione
Eseguire questo comando per avviare il runtime di integrazione SSIS di Azure: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Il completamento di questo comando richiede **da 20 a 30 minuti**. 

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS
Usare ora SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire i pacchetti SSIS in Azure. Connettersi al server SQL di Azure che ospita il catalogo SSIS (SSISDB). Il nome del server SQL di Azure ha il formato `<servername>.database.windows.net` (per il database SQL di Azure). 

Vedere gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire, eseguire e monitorare un pacchetto SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pianificare l'esecuzione di pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Script completo
In questa versione, per effettuare il provisioning di un'istanza del runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS nel cloud è necessario usare PowerShell. Non è possibile attualmente effettuare il provisioning di questo runtime usando il portale di Azure. 

Lo script di PowerShell in questa sezione configura un'istanza del runtime di integrazione SSIS di Azure nel cloud che esegue i pacchetti SSIS. Al termine dell'esecuzione di questo script, è possibile distribuire ed eseguire pacchetti SSIS nel cloud di Microsoft Azure con SSISDB ospitato nel database SQL di Azure.

1. Avviare Windows PowerShell Integrated Scripting Environment (ISE).
2. In ISE eseguire questo comando al prompt dei comandi:    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Copiare lo script di PowerShell in questa sezione e incollarlo in ISE.
4. Specificare i valori appropriati per i parametri dello script nella sezione delle specifiche di SSIS in Azure all'inizio dello script. Questi parametri sono descritti nella sezione successiva.
5. Eseguire lo script. L'esecuzione del comando `Start-AzureRmDataFactoryV2IntegrationRuntime` nella parte finale dello script richiede **20-30 minuti**.

> [!NOTE]
> Lo script si connette al database SQL di Azure per preparare il database del catalogo SSIS (SSISDB). Lo script configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificata, e aggiunge la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.

Per un elenco di **piani tariffari** supportati per il database SQL di Azure, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md). 

Per un elenco delle aree supportate da Azure Data Factory V2 e da Azure-SSIS Integration Runtime, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/). Espandere **Dati e analisi** per vedere **Data Factory V2** e **SSIS Integration Runtime**.

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
Se si usa un'istanza gestita di SQL di Azure (anteprima privata) per l'hosting del catalogo SQL Server Integration Services (SSIS) all'interno di una rete virtuale, è necessario anche aggiungere il runtime di integrazione SSIS di Azure alla stessa rete virtuale. Azure Data Factory versione 2 (anteprima) consente di aggiungere il runtime di integrazione SSIS di Azure a una VNet classica. Per altre informazioni, vedere [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

Per uno script completo per creare un runtime SSIS di Azure aggiunto a una rete virtuale, vedere [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorare e gestire un runtime di integrazione SSIS di Azure
Per informazioni dettagliate sul monitoraggio e la gestione di un runtime di integrazione SSIS di Azure, vedere gli articoli seguenti. 

- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione SSIS di Azure
> * Avviare il runtime di integrazione SSIS di Azure
> * Distribuire pacchetti SSIS
> * Rivedere lo script completo

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati nel cloud](tutorial-copy-data-dot-net.md)
