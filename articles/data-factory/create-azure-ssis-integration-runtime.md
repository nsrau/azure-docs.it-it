---
title: Creare il runtime di integrazione Azure-SSIS in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare un runtime di integrazione Azure-SSIS in Azure Data Factory in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f75d08074fc6020541226318d6422da373136a2d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267903"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Creare il runtime di integrazione SSIS di Azure in Azure Data Factory
Questo articolo illustra la procedura di provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory. È quindi possibile usare SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire ed eseguire pacchetti SQL Server Integration Services (SSIS) in questo runtime in Azure.

L'esercitazione [Distribuire pacchetti SQL Server Integration Services in Azure](tutorial-create-azure-ssis-runtime-portal.md) illustra come creare un runtime di integrazione SSIS di Azure usando il database SQL di Azure per ospitare il catalogo SSIS. Questo articolo amplia l'esercitazione e descrive come eseguire queste operazioni: 

- Facoltativamente usare Database SQL di Azure con gli endpoint servizio rete virtuale/Istanza gestita (anteprima) come server di database per ospitare il catalogo SSIS (database SSISDB). Come prerequisito, sarà necessario aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale e configurare le autorizzazioni della rete virtuale e le impostazioni come necessarie, vedere [Aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  
- Facoltativamente, usare l'autenticazione di Azure Active Directory (AAD) con l'identità del servizio gestita di Azure Data Factory (ADF) per collegare il runtime di integrazione SSIS di Azure al server di database. Come prerequisito, sarà necessario aggiungere l'identità del servizio gestita di Data Factory in un gruppo AAD con autorizzazioni di accesso al server di database, vedere [Abilitare l'autenticazione AAD per il runtime di integrazione SSIS di Azure](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Panoramica
Questo articolo illustra vari metodi per effettuare il provisioning di un runtime di integrazione Azure-SSIS:

- [Portale di Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modello di Azure Resource Manager](#azure-resource-manager-template)

Quando si crea un runtime di integrazione Azure-SSIS, Data Factory si connette al database SQL di Azure per preparare il database del catalogo SSIS (SSISDB). Lo script configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificata, e aggiunge la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.

Quando si effettua il provisioning di un'istanza del runtime di integrazione SSIS di Azure, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati supportate dai componenti predefiniti. È anche possibile installare componenti aggiuntivi. Per altre informazioni, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>prerequisiti

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile creare un account di [valutazione gratuito](http://azure.microsoft.com/pricing/free-trial/).
- **Server di database SQL di Azure/Istanza gestita (anteprima)**. Se non si ha già un server di database, crearne uno nel portale di Azure prima di iniziare. Questo server ospita il database del catalogo SSIS (SSISDB). È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure. In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come un database autonomo, parte di un pool elastico o in un'istanza gestita (anteprima) e reso accessibile in una rete pubblica o mediante l'aggiunta a una rete virtuale. Per un elenco di piani tariffari supportati per il database SQL di Azure, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

    Verificare che il server di database SQL di Azure/Istanza gestita (anteprima) non abbia un catalogo SSIS (database SSISDB). Il provisioning del runtime di integrazione Azure-SSIS non supporta l'uso di un catalogo SSIS esistente.
- **Rete virtuale classica/di Azure Resource Manager (facoltativa)**. È necessaria una rete virtuale di Azure in presenza di almeno una delle condizioni seguenti:
    - Si ospita il database del catalogo SSIS nel database SQL di Azure con endpoint del servizio di rete virtuale/Istanza gestita (anteprima) che si trova all'interno di una rete virtuale.
    - Si intende connettersi ad archivi dati locali da pacchetti SSIS eseguiti in un runtime di integrazione SSIS di Azure.
- **Azure PowerShell**. Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps), se si usa PowerShell per eseguire uno script per effettuare il provisioning del runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS nel cloud. 

> [!NOTE]
> - È possibile creare una data factory della versione 2 nelle aree Stati Uniti orientali, Stati Uniti orientali 2, Asia sud-orientale ed Europa occidentale. 
> - È possibile creare un runtime di integrazione SSIS di Azure nelle aree Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale, Regno Unito meridionale e Australia orientale.

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
2. Nella pagina **Impostazioni generali** di **Integration Runtime Setup** (Installazione di Integration Runtime) completare questa procedura: 

    ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Per **Nome**, immettere il nome del runtime di integrazione.
    
    b. Per **Descrizione**, immettere la descrizione del runtime di integrazione.
    
    c. Per **Località** selezionare la località del runtime di integrazione. Vengono visualizzate solo le località supportate. È consigliabile selezionare la stessa località del server di database che ospiterà SSISDB.
    
    d. Per **Dimensioni del nodo**, selezionare la dimensione del nodo nel cluster del runtime di integrazione. Vengono visualizzate solo le dimensioni dei nodi supportate. Selezionare una dimensione del nodo elevata (scalabilità verticale), se si prevede di eseguire numerosi pacchetti con un uso intensivo della capacità di calcolo o della memoria.
    
    e. Per **Numero nodo**, selezionare il numero di nodi nel cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei nodi supportati. Selezionare un cluster di grandi dimensioni con molti nodi (scalabilità orizzontale), se si prevede di eseguire numerosi pacchetti in parallelo.
     
    f. Per **Edition/License** (Edizione/licenza), selezionare l'edizione/licenza di SQL Server per il runtime di integrazione: Standard o Enterprise. Selezionare Enterprise se si prevede di usare funzionalità avanzate/premium nel runtime di integrazione. 
    
    g. Per **Risparmio sui costi**, selezionare l'opzione Vantaggio Azure Hybrid per il runtime di integrazione: Sì o No. Selezionare Sì se si vuole usare la propria licenza di SQL Server con Software Assurance per trarre vantaggio dai risparmi sui costi con Hybrid Use.
    
    h. Fare clic su **Avanti**. 
3. Nella pagina **Impostazioni SQL** completare la procedura seguente: 

    ![Impostazioni SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Per **Sottoscrizione** selezionare la sottoscrizione di Azure contenente il server di database che ospiterà SSISDB. 
    
    b. Per **Località**, selezionare la stessa località del server di database che ospiterà SSISDB. È consigliabile selezionare la stessa località del runtime di integrazione.
    
    c. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come un database autonomo, parte di un pool elastico o in un'istanza gestita (anteprima) e reso accessibile in una rete pubblica o mediante l'aggiunta a una rete virtuale.
    
    d. Per la casella di controllo **Use AAD authentication...** (Usa autenticazione AAD...), selezionare il metodo di autenticazione per il server di database che ospiterà SSISDB: SQL o Azure Active Directory (AAD) con l'identità del servizio gestita di Azure Data Factory. Se si seleziona la casella di controllo, è necessario aggiungere l'identità del servizio gestita di Data Factory in un gruppo AAD con autorizzazioni di accesso al server di database, vedere [Abilitare l'autenticazione AAD per il runtime di integrazione SSIS di Azure](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    
    e. Per **Nome utente amministratore**, immettere il nome utente di autenticazione SQL per il server di database che ospiterà SSISDB.
    
    f. Per **Password amministratore**, immettere la password di autenticazione SQL per il server di database che ospiterà SSISDB.  
    
    g. Per **Catalog Database Service Tier** (Livello di servizio database di catalogo), selezionare il livello di servizio per il server di database che ospiterà SSISDB: livello Basic/Standard/Premium o il nome del pool elastico.
    
    h. Fare clic su **Test connessione** e, in caso di esito positivo, fare clic su **Avanti**. 
4.  Nella pagina **Impostazioni avanzate** completare la procedura seguente:   

    ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
    
    a. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vuole usare più di un core per eseguire un singolo pacchetto pesante/di grandi dimensioni con un uso intensivo della capacità di calcolo o della memoria. Selezionare un numero elevato se si vuole eseguire uno o più pacchetti leggeri/di piccole dimensioni un singolo core. 
    
    b. Per **Custom Setup Container SAS URI** (URI di firma di accesso condiviso del contenitore di installazione personalizzata), immettere facoltativamente l'URI (Uniform Resource Identifier) di firma di accesso condiviso del contenitore BLOB di archiviazione di Azure in cui sono archiviati gli script di installazione e i relativi file associati. Vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 
5. Per la casella di controllo**Select a VNet...** (Selezionare una rete virtuale...), selezionare se aggiungere il runtime di integrazione a una rete virtuale. Verificare se si usa il database SQL di Azure con endpoint del servizio Rete virtuale/Istanza gestita (anteprima) per ospitare il database SSISDB o occorre accedere a dati in locale; ovvero, si dispone di origini/destinazioni di dati in locale nei pacchetti SSIS, vedere [Aggiungere un runtime di integrazione SSIS di Azure in una rete virtuale](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se si seleziona la casella di controllo, completare la procedura seguente:


    ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    a. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale. 
    
    b. Per **Località** viene selezionata la stessa località del runtime di integrazione.
    
    b. Per **Tipo**, selezionare il tipo di rete virtuale: classica o Azure Resource Manager. È consigliabile selezionare la rete virtuale di Azure Resource Manager, poiché la rete virtuale classica verrà deprecata a breve.
    
    c. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Questa rete virtuale deve essere la stessa rete virtuale usata per il Database SQL di Azure con gli endpoint servizio rete virtuale/Istanza gestita (anteprima) per ospitare SSISDB e quella connesso alla rete in locale.

    d. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Questa rete virtuale deve essere una subnet diversa rispetto a quella usata per istanza gestita (anteprima) per ospitare SSISDB.
6. Fare clic su **Convalida della rete virtuale** e in caso di esito positivo, fare clic su **Fine** per avviare la creazione del runtime di integrazione di Azure SSIS. 

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
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>Eseguire l'accesso e selezionare la sottoscrizione
Aggiungere il codice seguente allo script per eseguire l'accesso e selezionare la sottoscrizione di Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Convalidare la connessione al database
Aggiungere lo script seguente per convalidare l'endpoint del server di database SQL di Azure. 

```powershell
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
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
Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
Eseguire questo comando per creare una data factory.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Creare un runtime di integrazione
Eseguire questo comando per creare un runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS in Azure. 

Se non si usa il Database SQL di Azure con gli endpoint servizio rete virtuale/Istanza gestita (anteprima) per ospitare SSISDB e non si richiede l'accesso ai dati in locale, è possibile omettere i parametri VNetId e Subnet o passare al loro posto dei valori vuoti. In caso contrario, non è possibile ometterli ed è necessario passare i valori validi dalla configurazione della rete virtuale, vedere [Aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se si usa l'istanza gestita (anteprima) per ospitare SSISDB, è possibile omettere il parametro CatalogPricingTier o passare al suo posto un valore vuoto. In caso contrario, non è possibile ometterlo ed è necessario passare un valore valido dall'elenco dei livelli di prezzo supportati per il Database SQL di Azure, vedere [Limiti delle risorse del Database SQL](../sql-database/sql-database-resource-limits.md). 

Se si usa l'autenticazione di Azure Active Directory (AAD) con identità del servizio gestita di Azure Data Factory per connettersi al server del database, è possibile omettere il parametro CatalogAdminCredential, ma è necessario aggiungere il Data factory MSI in un gruppo AAD con autorizzazioni di accesso al server del database, vedere [Abilitare l'autenticazione AAD per il runtime di integrazione SSIS di Azure](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

È necessario passare valori per i parametri VnetId e Subnet con Istanza gestita di SQL di Azure (anteprima) aggiunta a una rete virtuale. Il parametro CatalogPricingTier non è applicabile per Istanza gestita di SQL di Azure (anteprima). 

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

Di seguito è riportato lo script completo che consente di creare un runtime di integrazione SSIS di Azure. 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
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

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
In questa sezione si userà il modello di Azure Resource Manager per creare un runtime di integrazione Azure-SSIS. Di seguito è riportata una procedura dettagliata di esempio: 

1. Creare un file JSON con il modello di Resource Manager di Azure seguente. Sostituire i valori nelle parentesi angolari (segnaposto) con valori personalizzati. 

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
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
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
    
2. Per distribuire il modello di Azure Resource Manager, eseguire il comando New-AzureRmResourceGroupDeployment come illustrato nell'esempio seguente, dove ADFTutorialResourceGroup è il nome del gruppo di risorse e ADFTutorialARM.json è il file che contiene la definizione JSON per la data factory e il runtime di integrazione SSIS di Azure. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Questo comando crea la data factory e, al suo interno, un runtime di integrazione Azure-SSIS, senza avviarlo. 
3. Per avviare il runtime di integrazione Azure-SSIS, eseguire il comando Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS
Usare ora SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire i pacchetti SSIS in Azure. Connettersi al server del database che ospita il catalogo SSIS (SSISDB). Il nome del server di database è nel formato: &lt;Nome del server di Database SQL di Azure&gt;. database.windows.net oppure &lt;Istanza gestita (anteprima) name.DSN prefisso&gt;.database.windows.net. Per istruzioni, vedere l'articolo su come [distribuire i pacchetti](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri argomenti relativi al runtime di integrazione SSIS di Azure in questa documentazione:

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta del runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale. 

