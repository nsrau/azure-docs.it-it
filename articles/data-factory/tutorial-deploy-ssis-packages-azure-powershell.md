---
title: Eseguire il provisioning del runtime di integrazione SSIS di Azure con PowerShell | Microsoft Docs
description: Informazioni su come eseguire il provisioning del runtime di integrazione Azure-SSIS in Azure Data Factory con PowerShell, in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7a4ae2198653ea8adab136caef0f812019efd998
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326114"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Eseguire il provisioning del runtime di integrazione SSIS di Azure in Azure Data Factory con PowerShell

Questa esercitazione illustra la procedura di provisioning di Azure-SSIS Integration Runtime in Azure Data Factory. Azure-SSIS IR supporta l'esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato dal server di database SQL di Azure o da Istanza gestita (modello di distribuzione del progetto) e di quelli distribuiti in file system, condivisioni file o File di Azure (modello di distribuzione del pacchetto). Dopo il provisioning di Azure-SSIS IR, è possibile usare strumenti familiari, ad esempio SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) e utilità della riga di comando, ad esempio `dtinstall`/`dtutil`/`dtexec`, per distribuire ed eseguire i pacchetti in Azure. In questa esercitazione vengono completati i passaggi seguenti:

> [!NOTE]
> Questo articolo usa Azure PowerShell per effettuare il provisioning di Azure-SSIS IR. Per usare il portale di Azure o l'app Azure Data Factory per il provisioning di Azure-SSIS IR, vedere [Esercitazione: Provisioning di Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione SSIS di Azure
> * Avviare il runtime di integrazione SSIS di Azure
> * Rivedere lo script completo
> * Distribuire pacchetti SSIS

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. Per informazioni concettuali in proposito, vedere la [panoramica del runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Server di database SQL di Azure (facoltativo)** . Se non si ha già un server di database, crearne uno nel portale di Azure prima di iniziare. Azure Data Factory creerà a sua volta il database SSISDB nel server di database. È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure. 
    - In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come database singolo, parte di un pool elastico o in un'istanza gestita e reso accessibile in una rete pubblica o mediante l'aggiunta a una rete virtuale. Per indicazioni sulla scelta del tipo di server di database in cui ospitare SSISDB, vedere [Confrontare un database singolo, un pool elastico e un'istanza gestita di Database SQL di Azure](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). Se si usa il server di database SQL di Azure con endpoint servizio di rete virtuale/Istanza gestita in una rete virtuale per ospitare il database SSISDB o occorre accedere a dati in locale, è necessario aggiungere Azure-SSIS IR a una rete virtuale. Vedere [Creare Azure-SSIS IR in una rete virtuale](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Questo non vale quando si usa il server di database SQL di Azure con endpoint servizio di rete virtuale/Istanza gestita in una rete virtuale per ospitare il database SSISDB. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Per abilitare questa impostazione con PowerShell, vedere [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Aggiungere l'indirizzo IP del computer client, o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client, all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di database e di server di database SQL di Azure](../sql-database/sql-database-firewall-configure.md).
    - È possibile connettersi al server di database usando l'autenticazione SQL con le credenziali di amministratore del server o l'autenticazione di Azure Active Directory (AAD) con l'identità gestita per Azure Data Factory.  Nel secondo caso è necessario aggiungere l'identità gestita per ADF in un gruppo AAD con autorizzazioni di accesso al server di database. Vedere [Creare il runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Verificare che nel server di database non sia già presente un database SSISDB. Il provisioning di Azure-SSIS IR non supporta l'uso di un database SSISDB esistente.
- **Azure PowerShell**. Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps) se si vuole eseguire uno script di PowerShell per effettuare il provisioning di Azure-SSIS IR.

> [!NOTE]
> - Per un elenco delle aree di Azure in cui sono attualmente disponibili Azure Data Factory e Azure-SSIS IR, vedere [Disponibilità di Azure Data Factory e SSIS IR in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Avviare Windows PowerShell ISE

Avviare **Windows PowerShell ISE** con privilegi amministrativi. 

## <a name="create-variables"></a>Creare le variabili

Copiare e incollare lo script seguente, specificando i valori per le variabili. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Eseguire l'accesso e selezionare la sottoscrizione

Aggiungere il codice seguente allo script per eseguire l'accesso e selezionare la sottoscrizione di Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Convalidare la connessione al server di database

Aggiungere lo script seguente per convalidare il server di database SQL di Azure. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Per creare un database SQL di Azure come parte dello script, vedere l'esempio seguente: 

Impostare i valori per le variabili che non sono già state definite. Ad esempio:  SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Se il gruppo di risorse esiste già, non copiare questo codice nello script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Creare una data factory

Eseguire questo comando per creare una data factory:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Creare un runtime di integrazione

Eseguire questo comando per creare un runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS in Azure.

Se non si usa il database SSISDB, è possibile omettere i parametri CatalogServerEndpoint, CatalogPricingTier e CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Avviare il runtime di integrazione

Eseguire i comandi seguenti per avviare Azure-SSIS Integration Runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Ad eccezione di eventuali tempi per l'installazione personalizzata, questo processo verrà completato entro 5 minuti.
>
> Se si usa il database SSISDB, il servizio Azure Data Factory si connetterà al server di database per la preparazione di SSISDB. 
> 
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. È anche possibile installare componenti aggiuntivi. Vedere [Installazione personalizzata per Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Script completo

Lo script di PowerShell in questa sezione configura un'istanza di Azure-SSIS IR che esegue i pacchetti SSIS. Dopo aver eseguito lo script, sarà possibile distribuire ed eseguire pacchetti SSIS in Azure.

1. Avviare Windows PowerShell Integrated Scripting Environment (ISE).
2. In ISE eseguire questo comando al prompt dei comandi.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Copiare lo script di PowerShell in questa sezione e incollarlo in ISE.
4. Specificare i valori appropriati per tutti i parametri all'inizio dello script.
5. Eseguire lo script. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorare e gestire un runtime di integrazione SSIS di Azure

Per informazioni dettagliate sul monitoraggio e la gestione di un runtime di integrazione SSIS di Azure, vedere gli articoli seguenti. 

- [Monitorare Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Gestire Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS

Se si usa il catalogo SSISDB, è possibile distribuire i pacchetti al suo interno ed eseguirli in Azure-SSIS IR usando gli strumenti SSDT e SSMS per la connessione al server di database tramite l'endpoint server. Per il server di database SQL di Azure/Istanza gestita con un endpoint pubblico, il formato dell'endpoint server è, rispettivamente, `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`. Se non si usa il catalogo SSISDB, è possibile distribuire i pacchetti nei file system, nelle condivisioni file o in File di Azure ed eseguirli in Azure-SSIS IR usando le utilità della riga di comando `dtinstall`/`dtutil`/`dtexec`. Per altre informazioni, vedere [Distribuire pacchetti SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). In entrambi i casi, è anche possibile eseguire i pacchetti distribuiti in Azure-SSIS IR usando l'attività di esecuzione del pacchetto SSIS in pipeline di Azure Data Factory. Vedere [Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Vedere anche gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire ed eseguire un pacchetto di SQL Server Integration Services (SSIS) in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pianificare l'esecuzione dei pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione SSIS di Azure
> * Avviare il runtime di integrazione SSIS di Azure
> * Rivedere lo script completo
> * Distribuire pacchetti SSIS

Per altre informazioni sulla personalizzazione di Azure-SSIS Integration Runtime, vedere l'articolo seguente:

> [!div class="nextstepaction"]
>[Personalizzare l'installazione del runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)