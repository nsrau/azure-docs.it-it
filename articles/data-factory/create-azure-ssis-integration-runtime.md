---
title: Creare Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare Azure-SSIS Integration Runtime in Azure Data Factory in modo da rendere possibile la distribuzione e l'esecuzione dei pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d30ec0765627ec173f0027e49f44cb77f6b26ac6
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361480"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Creare Azure-SSIS Integration Runtime in Azure Data Factory

Questo articolo illustra la procedura per il provisioning di Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF). È quindi possibile usare SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire ed eseguire pacchetti SQL Server Integration Services (SSIS) in questo runtime di integrazione in Azure.

L'[esercitazione Distribuire pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md) illustra come creare Azure-SSIS IR usando il server di database SQL di Azure per ospitare il database di catalogo SSIS (SSISDB). Questo articolo amplia l'esercitazione e descrive come eseguire queste operazioni:

- Per ospitare il database SSISDB è possibile usare il server di database SQL di Azure con endpoint di servizio di rete virtuale o l'istanza gestita. Per indicazioni sulla scelta del tipo di server di database per ospitare SSISDB, vedere [Confrontare database singolo/pool elastico e Istanza gestita di database SQL di Azure](create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Come prerequisito, è necessario aggiungere Azure-SSIS IR a una rete virtuale e configurare le autorizzazioni/impostazioni della rete virtuale in base alle esigenze. Vedere [Aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

- Facoltativamente, usare l'autenticazione di Azure Active Directory (AAD) con l'identità gestita di ADF per connettersi al server di database. Come prerequisito, è necessario aggiungere l'identità gestita per ADF come utente di database indipendente in grado di creare SSISDB nel server di database SQL di Azure o nell'istanza gestita. Per informazioni, vedere [Abilitare l'autenticazione di AAD per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

## <a name="overview"></a>Panoramica

Questo articolo illustra vari metodi per effettuare il provisioning di Azure-SSIS IR:

- [Portale di Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modello di Azure Resource Manager](#azure-resource-manager-template)

Quando si crea Azure-SSIS IR, il servizio ADF si connette al server di database SQL di Azure o all'istanza gestita per preparare il database SSISDB. Configura inoltre le autorizzazioni/impostazioni per la rete virtuale, se specificata, e aggiunge Azure-SSIS IR alla rete virtuale.

Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati supportate dai componenti predefiniti. È anche possibile installare componenti aggiuntivi. Per altre informazioni, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si ha ancora una sottoscrizione, è possibile creare un account di [valutazione gratuito](https://azure.microsoft.com/pricing/free-trial/).

- **Server di database SQL di Azure o Istanza gestita**. Se non si ha ancora un server di database, è possibile crearne uno nel portale di Azure prima di iniziare. Questo server ospiterà il database SSISDB. È consigliabile creare il server di database nella stessa area di Azure in cui si trova il runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure. In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come database singolo, come parte di un pool elastico o nell'istanza gestita e può essere reso accessibile in una rete pubblica o tramite l'aggiunta a una rete virtuale. Per un elenco di piani tariffari supportati per il database SQL di Azure, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

    Verificare che il server di database SQL di Azure o l'istanza gestita non abbia già un database SSISDB. Il provisioning di Azure-SSIS IR non supporta l'uso di un database SSISDB esistente.

- **Rete virtuale di Azure Resource Manager (facoltativa)**. È necessario avere una rete virtuale di Azure Resource Manager in presenza di almeno una delle condizioni seguenti:

  - Si ospita il database SSISDB nel server di database SQL di Azure con endpoint di servizio di rete virtuale o in Istanza gestita all'interno di una rete virtuale.
  - Si vuole connettersi ad archivi dati locali da pacchetti SSIS in esecuzione su Azure-SSIS IR.

- **Azure PowerShell**. Seguire le istruzioni riportate in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps) se si vuole eseguire uno script di PowerShell per effettuare il provisioning di Azure-SSIS IR.

### <a name="region-support"></a>Supporto di area

Per un elenco delle aree di Azure in cui sono attualmente disponibili ADF e Azure-SSIS IR, vedere [Disponibilità di ADF + SSIS IR per area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Confrontare database singolo/pool elastico e Istanza gestita di database SQL di Azure

La tabella seguente mette a confronto alcune funzionalità del server di database SQL di Azure e di Istanza gestita in relazione a Azure-SSIR IR:

| Funzionalità | Database singolo/pool elastico| Istanza gestita |
|---------|--------------|------------------|
| **Pianificazione** | SQL Server Agent non è disponibile.<br/><br/>Vedere [Pianificare un pacchetto nell'ambito di una pipeline di ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| L’agente di istanza gestita è disponibile. |
| **Authentication** | È possibile creare SSISDB con un utente di database indipendente che rappresenta un gruppo AAD qualsiasi con l'identità gestita di ADF come membro del ruolo **db_owner**.<br/><br/>Vedere [Abilitare l'autenticazione di Azure AD per creare SSISDB nel server di database SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | È possibile creare SSISDB con un utente di database indipendente che rappresenta l'identità gestita di ADF. <br/><br/>Vedere [Abilitare l'autenticazione di Azure AD per creare SSISDB in Istanza gestita di database SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Livello di servizio** | Quando si crea Azure-SSIS IR con il server di database SQL di Azure, è possibile selezionare il livello di servizio per SSISDB. Sono disponibili più livelli di servizio. | Quando si crea Azure-SSIS IR con la propria istanza gestita, non è possibile selezionare il livello di servizio per SSISDB. Tutti i database nell'istanza gestita condividono la stessa risorsa allocata a tale istanza. |
| **Rete virtuale** | Supporta solo le reti virtuali di Azure Resource Manager per l'aggiunta di Azure-SSIS IR se si usa il server di database SQL di Azure con endpoint del servizio di rete virtuale o si richiede l'accesso ad archivi dati in locale. | Supporta solo le reti virtuali di Azure Resource Manager per l'aggiunta di Azure-SSIS IR. La rete virtuale è sempre obbligatoria.<br/><br/>Se si aggiunge Azure-SSIS IR alla stessa rete virtuale della propria istanza gestita, assicurarsi che Azure-SSIS IR si trovi in una subnet diversa rispetto all'istanza gestita. Se si aggiunge Azure-SSIS IR a una rete virtuale diversa da quella della propria istanza gestita, è consigliabile effettuare un peering di rete virtuale o stabilire una connessione tra reti virtuali. Vedere [Connettere un'applicazione a Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transazioni distribuite** | Supportate tramite transazioni elastiche. Le transazioni di Microsoft Distributed Transaction Coordinator (MSDTC) non sono supportate. Se i pacchetti SSIS usano MSDTC per coordinare le transazioni distribuite, è consigliabile eseguire la migrazione a Transazioni elastiche per il database SQL di Azure. Per altre informazioni, vedere [Transazioni distribuite in database cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Non supportati. |
| | | |

## <a name="azure-portal"></a>Portale di Azure

In questa sezione si userà il portale di Azure, e in particolare l'interfaccia utente o l'app di ADF, per creare Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**.

   ![Nuovo->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **MyAzureSsisDataFactory** per **Nome**.

   ![Pagina Nuova data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio nomeutenteMyAzureSsisDataFactory, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. Per il **gruppo di risorse**, eseguire una di queste operazioni:

   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.

   Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).

1. Selezionare **V2** per **version**.
1. Selezionare la **località** per la data factory. Nell'elenco vengono mostrate solo le località supportate per la creazione di data factory.
1. Selezionare **Aggiungi al dashboard**.
1. Fare clic su **Create**(Crea).
1. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory).

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.

    ![Home page di Data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Fare clic su **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

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

    c. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come database singolo, parte di un pool elastico o in un'istanza gestita e reso accessibile in una rete pubblica o mediante l'aggiunta a una rete virtuale.

    d. Nella casella di controllo **Use AAD authentication** (Usa autenticazione AAD) selezionare il metodo di autenticazione per il server di database che ospiterà SSISDB: SQL o Azure Active Directory (AAD) con l'identità gestita per Azure Data Factory. Se si seleziona la casella di controllo, è necessario aggiungere l'identità gestita per ADF in un gruppo AAD con autorizzazioni di accesso al server di database. Vedere [Abilitare l'autenticazione AAD per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

    e. Per **Nome utente amministratore**, immettere il nome utente di autenticazione SQL per il server di database che ospiterà SSISDB.

    f. Per **Password amministratore**, immettere la password di autenticazione SQL per il server di database che ospiterà SSISDB.

    g. Per **Catalog Database Service Tier** (Livello di servizio del database di catalogo) selezionare il livello di servizio per il server di database che ospiterà SSISDB: livello Basic/Standard/Premium o nome del pool elastico.

    h. Fare clic su **Test connessione** e, in caso di esito positivo, fare clic su **Avanti**.

4. Nella pagina **Impostazioni avanzate** completare la procedura seguente:

    ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vuole usare più di un core per eseguire un singolo pacchetto pesante/di grandi dimensioni con un uso intensivo della capacità di calcolo o della memoria. Selezionare un numero elevato se si vuole eseguire uno o più pacchetti leggeri/di piccole dimensioni un singolo core.

    b. Per **Custom Setup Container SAS URI** (URI di firma di accesso condiviso del contenitore di installazione personalizzata), immettere facoltativamente l'URI (Uniform Resource Identifier) di firma di accesso condiviso del contenitore BLOB di archiviazione di Azure in cui sono archiviati gli script di installazione e i relativi file associati. Vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Per la casella di controllo**Select a VNet...** (Selezionare una rete virtuale...), selezionare se aggiungere il runtime di integrazione a una rete virtuale. Verificare se si usa il database SQL di Azure con endpoint di servizio di rete virtuale/Istanza gestita per ospitare il database SSISDB o se è necessario l'accesso ai dati locali, ovvero se si hanno origini/destinazioni dati locali nei pacchetti SSIS, vedere [Aggiungere Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se si seleziona la casella di controllo, completare la procedura seguente:

   ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale.

    b. Per **Località** viene selezionata la stessa località del runtime di integrazione.

    c. Per **Tipo**, selezionare il tipo di rete virtuale: classica o Azure Resource Manager. È consigliabile selezionare la rete virtuale di Azure Resource Manager, poiché la rete virtuale classica verrà deprecata a breve.

    d. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Questa rete virtuale deve essere la stessa rete virtuale usata per il database SQL di Azure con gli endpoint di servizio di rete virtuale/Istanza gestita per ospitare SSISDB o quella connessa alla rete locale.

    e. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve trattarsi di una subnet diversa rispetto a quella usata per Istanza gestita per ospitare SSISDB.

6. Fare clic su **Convalida della rete virtuale** e in caso di esito positivo, fare clic su **Fine** per avviare la creazione del runtime di integrazione di Azure SSIS.

    > [!IMPORTANT]
    > - Il completamento di questo processo richiede da 20 a 30 minuti circa
    > - Il servizio Data Factory si connette al database SQL di Azure per preparare il database del catalogo SSIS (SSISDB). Configura inoltre le autorizzazioni e le impostazioni per la rete virtuale, se specificata, e aggiunge la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.

7. Nella finestra **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Fare clic su **Aggiorna** per aggiornare lo stato.

   ![Stato creazione](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Usare i collegamenti nella colonna **Azioni** per arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato.

   ![Azure SSIS Integration Runtime - Azioni](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime di integrazione SSIS di Azure nel portale

1. Nell'interfaccia utente di Azure Data Factory, passare alla scheda **Modifica**, fare clic su **Connessioni** e quindi selezionare la scheda **Integration Runtimes** (Runtime di integrazione) per visualizzare i runtime di integrazione esistenti nella data factory.

   ![Visualizzare i runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Fare clic su **Nuovo** per creare un nuovo runtime di integrazione SSIS di Azure.

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Per creare un runtime di integrazione SSIS di Azure, fare clic su **Nuovo** come mostrato nell'immagine.
4. Nella finestra Integration Runtime Setup (Installazione di Integration Runtime) selezionare **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi fare clic su **Avanti**.

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime).

## <a name="azure-powershell"></a>Azure PowerShell

In questa sezione si userà Azure PowerShell per creare un runtime di integrazione Azure-SSIS.

### <a name="create-variables"></a>Creare le variabili

Definire le variabili da usare nello script di questa esercitazione:

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Accedi e selezionare la sottoscrizione

Aggiungere il codice seguente allo script per accedere e selezionare la sottoscrizione di Azure:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Convalidare la connessione al database

Aggiungere lo script seguente per convalidare l'endpoint del server di database SQL di Azure.

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
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
}
```

### <a name="configure-virtual-network"></a>Configurare la rete virtuale

Aggiungere lo script seguente per configurare automaticamente le autorizzazioni/impostazioni della rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Creare una data factory

Eseguire questo comando per creare una data factory.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Creare un runtime di integrazione

Eseguire questo comando per creare un runtime di integrazione SSIS di Azure che esegue i pacchetti SSIS in Azure.

Se non si usa il database SQL di Azure con gli endpoint di servizio di rete virtuale/Istanza gestita per ospitare SSISDB e non è necessario l'accesso ai dati locali, è possibile omettere i parametri VNetId e Subnet o passare al loro posto valori vuoti. In caso contrario, non è possibile ometterli ed è necessario passare i valori validi dalla configurazione della rete virtuale, vedere [Aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se si usa Istanza gestita per ospitare SSISDB, è possibile omettere il parametro CatalogPricingTier o passare al suo posto un valore vuoto. In caso contrario, non è possibile ometterlo ed è necessario passare un valore valido dall'elenco dei livelli di prezzo supportati per il Database SQL di Azure, vedere [Limiti delle risorse del Database SQL](../sql-database/sql-database-resource-limits.md).

Se si usa l'autenticazione di Azure Active Directory (AAD) con l'identità gestita per consentire ad Azure Data Factory di connettersi al server di database, è possibile omettere il parametro CatalogAdminCredential, ma è necessario aggiungere l'identità gestita di ADF in un gruppo AAD con autorizzazioni di accesso al server di database. Vedere [Abilitare l'autenticazione AAD per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). In caso contrario, non sarà possibile ometterlo e si dovrà passare un oggetto valido formato dal nome utente e dalla password dell'amministratore server per l'autenticazione SQL.

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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Avviare il runtime di integrazione

Eseguire questo comando per avviare il runtime di integrazione SSIS di Azure:

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
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
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
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

2. Per distribuire il modello di Azure Resource Manager, eseguire comando New-AzResourceGroupDeployment come illustrato nell'esempio seguente, dove ADFTutorialResourceGroup è il nome del gruppo di risorse e adftutorialarm. JSON è il file che contiene la definizione JSON per data factory e del runtime di integrazione SSIS di Azure.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Questo comando crea la data factory e, al suo interno, un runtime di integrazione Azure-SSIS, senza avviarlo.

3. Per avviare il runtime di integrazione Azure-SSIS, eseguire il comando Start-AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS

Usare ora SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire i pacchetti SSIS in Azure. Connettersi al server del database che ospita il catalogo SSIS (SSISDB). Il nome del server di database SQL di Azure ha il formato seguente: &lt;Nome server di database SQL di Azure&gt;.database.windows.net oppure &lt;Nome istanza gestita.prefisso DNS&gt;.database.windows.net. Per istruzioni, vedere l'articolo su come [distribuire i pacchetti](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri argomenti relativi al runtime di integrazione SSIS di Azure in questa documentazione:

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure.
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS.
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite.
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione.
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta del runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale.
