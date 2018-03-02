---
title: Creare il runtime di integrazione Azure-SSIS in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare un runtime di integrazione Azure-SSIS in modo da poter eseguire il pacchetto SSIS nel cloud di Azure.
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 879489dffbf713b5fadb72a58638e462938aaf26
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione SSIS di Azure in Azure Data Factory
Questo articolo illustra la procedura di provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory. È quindi possibile usare SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire pacchetti SQL Server Integration Services (SSIS) in questo runtime in Azure.

L'esercitazione [Distribuire pacchetti SQL Server Integration Services in Azure](tutorial-create-azure-ssis-runtime-portal.md) illustra come creare un runtime di integrazione Azure-SSIS usando il database SQL di Azure come archivio per il catalogo SSIS. Questo articolo amplia l'esercitazione e descrive come eseguire queste operazioni: 

- Usare l'istanza gestita di SQL di Azure (anteprima privata) per l'hosting di un catalogo SSIS (database SSISDB).
- Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale di Azure. Per informazioni concettuali sull'aggiunta di un runtime di integrazione SSIS di Azure a una rete virtuale e sulla configurazione di una rete virtuale nel portale di Azure, vedere [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Panoramica
Questo articolo illustra vari metodi per effettuare il provisioning di un runtime di integrazione Azure-SSIS:

- [Azure portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modello di Azure Resource Manager](#azure-resource-manager-template)

Quando si crea un runtime di integrazione Azure-SSIS, Data Factory si connette al database SQL di Azure per preparare il database del catalogo SSIS (SSISDB). Lo script configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificata, e aggiunge la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.

Quando si effettua il provisioning di un'istanza del runtime di integrazione SSIS di Azure, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati supportate dai componenti predefiniti. Non è attualmente possibile installare componenti di terze parti per SSIS (inclusi i componenti di terze parti di Microsoft, ad esempio i componenti Oracle e Teradata di Attunity e i componenti SAP BI).

## <a name="prerequisites"></a>prerequisiti

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile creare un account di [valutazione gratuito](http://azure.microsoft.com/pricing/free-trial/).
- **Server di database SQL di Azure** o **istanza gestita di SQL Server (anteprima privata) (anteprima privata estesa)**. Se non si ha già un server di database, crearne uno nel portale di Azure prima di iniziare. Questo server ospita il database del catalogo SSIS (SSISDB). È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure. Annotare il piano tariffario del server SQL di Azure. Per un elenco di piani tariffari supportati per il database SQL di Azure, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

    Verificare che il server di database SQL di Azure o l'istanza gestita di SQL Server (anteprima privata estesa) non abbia un catalogo SSIS (database SSISDB). Il provisioning del runtime di integrazione Azure-SSIS non supporta l'uso di un catalogo SSIS esistente.
- **Rete virtuale classica o rete virtuale di Azure Resource Manager (VNet) (facoltativa)**. È necessaria una rete virtuale di Azure in presenza di almeno una delle condizioni seguenti:
    - Il database del catalogo SSIS è ospitato in un'istanza gestita di SQL Server (anteprima privata) che fa parte di una rete virtuale.
    - Si intende connettersi ad archivi dati locali da pacchetti SSIS eseguiti in un runtime di integrazione SSIS di Azure.
- **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell). PowerShell viene usato per eseguire uno script per il provisioning di un runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS nel cloud. 

> [!NOTE]
> - È possibile creare una data factory della versione 2 nelle aree Stati Uniti orientali, Stati Uniti orientali 2, Asia sud-orientale ed Europa occidentale. 
> - È possibile creare un runtime di integrazione SSIS di Azure nelle aree Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale e Australia orientale.

## <a name="azure-portal"></a>Portale di Azure
In questa sezione si userà il portale di Azure, e in particolare l'interfaccia utente di Data Factory, per creare un runtime di integrazione Azure-SSIS. 

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Accedere al [Portale di Azure](https://portal.azure.com/).    
3. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Nella pagina **Nuova data factory** immettere **MyAzureSsisDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio nomeutenteMyAzureSsisDataFactory, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco vengono mostrate solo le località supportate per la creazione di data factory.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Fare clic su **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

1. Nella pagina delle attività iniziali fare clic sul riquadro **Configure SSIS Integration Runtime** (Configura SSIS Integration Runtime). 

   ![Riquadro Configure SSIS Integration Runtime (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Nella pagina **Impostazioni generali** di **Integration Runtime Setup** (Installazione di Integration Runtime) seguire questa procedura: 

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Specificare un **nome** per il runtime di integrazione.
    2. Specificare una **località** per il runtime di integrazione. Vengono visualizzate solo le località supportate.
    3. Selezionare le **dimensioni del nodo** da configurare con il runtime SSIS.
    4. Specificare il **numero di nodi** nel cluster.
    5. Fare clic su **Avanti**. 
1. In **Impostazioni SQL** seguire questa procedura: 

    ![Impostazioni SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Specificare la **sottoscrizione** contenente il server Azure SQL. 
    2. Selezionare il server Azure SQL per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo)
    3. Immettere il nome utente dell'**amministratore**
    4. Immettere la **password** per l'amministratore.  
    5. Selezionare il **livello di servizio** per il database SSISDB. Il valore predefinito è Basic.
    6. Fare clic su **Avanti**. 
1.  Nella pagina **Impostazioni avanzate** selezionare un valore per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo).   

    ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Questo passaggio è **facoltativo**. Se è disponibile una rete virtuale (classica o Azure Resource Manager) a cui si vuole aggiungere il runtime di integrazione, selezionare l'opzione **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Selezionare una rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure e consentire ai servizi di Azure di configurare le autorizzazioni/impostazioni della rete virtuale) e seguire questa procedura: 

    ![Impostazioni avanzate per la rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. In **Sottoscrizione** specificare la **sottoscrizione** contenente la rete virtuale. 
    2. Per il tipo, specificare il **tipo** di rete virtuale (rete virtuale classica o rete virtuale di Azure Resource Manager). 
    3. In **Nome della rete virtuale** selezionare il nome della **rete virtuale**. 
    4. In **Nome subnet** selezionare il nome della **subnet** nella rete virtuale.
1. Fare clic su **Fine** per avviare la creazione del runtime di integrazione SSIS di Azure. 

    > [!IMPORTANT]
    > - Il completamento di questo processo richiede circa 20 minuti
    > - Il servizio Data Factory si connette al database SQL di Azure per preparare il database del catalogo SSIS (SSISDB). Lo script configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificata, e aggiunge la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.
7. Nella finestra **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Fare clic su **Aggiorna** per aggiornare lo stato. 

    ![Stato creazione](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Usare i collegamenti nella colonna **Azioni** per arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato. 

    ![Azure SSIS Integration Runtime - Azioni](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime di integrazione SSIS di Azure nel portale

1. Nell'interfaccia utente di Azure Data Factory, passare alla scheda **Modifica**, fare clic su **Connessioni** e quindi selezionare la scheda **Integration Runtimes** (Runtime di integrazione) per visualizzare i runtime di integrazione esistenti nella data factory. 
    ![Visualizzare i runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Fare clic su **Nuovo** per creare un nuovo runtime di integrazione SSIS di Azure. 

    ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Per creare un runtime di integrazione SSIS di Azure, fare clic su **Nuovo** come mostrato nell'immagine. 
3. Nella finestra Integration Runtime Setup (Installazione di Integration Runtime) selezionare **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi fare clic su **Avanti**.

    ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime).

## <a name="azure-powershell"></a>Azure PowerShell
In questa sezione si userà Azure PowerShell per creare un runtime di integrazione Azure-SSIS.

### <a name="create-variables"></a>Creare le variabili
Definire le variabili da usare nello script di questa esercitazione:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Eseguire l'accesso e selezionare la sottoscrizione
Aggiungere il codice seguente allo script per eseguire l'accesso e selezionare la sottoscrizione di Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Convalidare la connessione al database
Aggiungere lo script seguente per convalidare il server di database SQL di Azure server.database.windows.net o l'endpoint server dell'istanza gestita di SQL di Azure (anteprima privata). 

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

### <a name="configure-virtual-network"></a>Configurare la rete virtuale
Aggiungere lo script seguente per configurare automaticamente le autorizzazioni/impostazioni della rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
Eseguire questo comando per creare una data factory:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Creare un runtime di integrazione
Eseguire il comando seguente per creare un runtime di integrazione Azure-SSIS che consenta l'esecuzione di pacchetti SSIS in Azure: usare lo script riportato nella sezione in base al tipo di database in uso (database SQL di Azure o istanza gestita di SQL di Azure - anteprima privata). 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Database SQL di Azure per l'hosting del database SSISDB (catalogo SSIS) 

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

Non è necessario passare valori per VNetId e Subnet a meno che non si debba accedere a dati locali perché nei pacchetti SSIS sono incluse origini dati/destinazioni locali. È necessario passare un valore per il parametro CatalogPricingTier. Per un elenco di piani tariffari supportati per il database SQL di Azure, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Istanza gestita di SQL di Azure (anteprima privata) per l'hosting del database SSISDB

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

È necessario passare valori per i parametri VnetId e Subnet con l'istanza gestita di SQL di Azure (anteprima privata) aggiunta a una rete virtuale. Il parametro CatalogPricingTier non si applica all'istanza gestita di SQL di Azure. 

### <a name="start-integration-runtime"></a>Avviare il runtime di integrazione
Eseguire questo comando per avviare il runtime di integrazione SSIS di Azure: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Il completamento di questo comando richiede **da 20 a 30 minuti**. 


### <a name="full-script"></a>Script completo
Di seguito è riportato lo script completo che consente di creare un runtime di integrazione Azure-SSIS e di aggiungerlo a una rete virtuale. Questo script presuppone che si stia usando l'istanza gestita di SQL di Azure per l'hosting del catalogo SSIS. 

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
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
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

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
In questa sezione si userà un modello di Azure Resource Manager per creare un runtime di integrazione Azure-SSIS. Di seguito è riportata una procedura dettagliata di esempio: 

1. Creare un file JSON con il modello di Resource Manager seguente. Sostituire i valori nelle parentesi angolari (segnaposto) con valori personalizzati. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Per distribuire il modello di Resource Manager, eseguire il comando New-AzureRmResourceGroupDeployment, come illustrato nell'esempio seguente. In questo esempio, ADFTutorialResourceGroup è il nome del gruppo di risorse, mentre ADFTutorialARM.json è il file contenente la definizione JSON per la data factory e il runtime di integrazione Azure-SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Questo comando crea la data factory e, al suo interno, crea un runtime di integrazione Azure-SSIS, senza avviarlo. 
3. Per avviare il runtime di integrazione Azure-SSIS, eseguire il comando Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS
Usare ora SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire i pacchetti SSIS in Azure. Connettersi al server SQL di Azure che ospita il catalogo SSIS (SSISDB). Il nome del server SQL di Azure ha il formato &lt;nomeserver&gt;.database.windows.net (per il database SQL di Azure). Per istruzioni, vedere l'articolo su come [distribuire i pacchetti](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri argomenti relativi al runtime di integrazione SSIS di Azure in questa documentazione:

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce informazioni sull'aggiunta di un runtime di integrazione SSIS di Azure a una rete virtuale di Azure (VNet). Indica inoltre i passaggi per usare il portale di Azure per configurare VNet in modo che il runtime di integrazione SSIS di Azure possa essere aggiunto alla rete virtuale. 