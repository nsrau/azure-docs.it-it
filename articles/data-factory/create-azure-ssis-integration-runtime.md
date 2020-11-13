---
title: Creare un runtime di integrazione SSIS di Azure in Azure Data Factory
description: Informazioni su come creare un runtime di integrazione Azure-SSIS in Azure Data Factory in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: effa0d3ba9f7098b691605bfbd76bff9ea3d5e66
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593757"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione SSIS di Azure in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra la procedura per eseguire il provisioning di un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) in Azure Data Factory (ADF). Azure-SSIS IR supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto)
- Esecuzione di pacchetti distribuiti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di database SQL di Azure (modello di distribuzione del pacchetto)

Dopo aver effettuato il provisioning di un'istanza di Azure-SSIS IR, è possibile usare i consueti strumenti per distribuire ed eseguire i pacchetti in Azure. Questi strumenti, già abilitati per Azure e includono SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) e utilità da riga di comando come [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

L'esercitazione sul [provisioning Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) illustra come creare una Azure-SSIS IR tramite il portale di Azure o l'app data factory. L'esercitazione illustra anche come usare facoltativamente un server di database SQL di Azure o un'istanza gestita per ospitare SSISDB. Questo articolo espande l'esercitazione e descrive come eseguire queste attività facoltative:

- Usare un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per l'hosting di SSISDB. Come prerequisito, è necessario configurare le autorizzazioni e le impostazioni della rete virtuale per la Azure-SSIS IR per l'aggiunta a una rete virtuale.

- Usare l'autenticazione Azure Active Directory (Azure AD) con l'identità gestita per la data factory per connettersi a un server di database SQL di Azure o a un'istanza gestita. Come prerequisito, è necessario aggiungere l'identità gestita per il data factory come utente del database che può creare un'istanza di SSISDB.

- Aggiungere il Azure-SSIS IR a una rete virtuale o configurare un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR per accedere ai dati in locale.

Questo articolo illustra come effettuare il provisioning di un Azure-SSIS IR usando il portale di Azure, Azure PowerShell e un modello di Azure Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si ha già una sottoscrizione, è possibile creare un account di [valutazione gratuito](https://azure.microsoft.com/pricing/free-trial/) .

- **Server di database SQL di Azure o sql istanza gestita (facoltativo)**. Se non si dispone già di un server di database o di un'istanza gestita, crearne uno nel portale di Azure prima di iniziare. Data Factory creerà a sua volta un'istanza di SSISDB in questo server di database. 

  Si consiglia di creare il server di database o l'istanza gestita nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure.

  Tenere presenti questi punti:

  - L'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita. e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per istruzioni sulla scelta tra database SQL e SQL Istanza gestita per ospitare SSISDB, vedere la sezione [confrontare il database SQL e sql istanza gestita](#comparison-of-sql-database-and-sql-managed-instance) in questo articolo. 
  
    Se si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita di SQL con endpoint privato per ospitare SSISDB o se è necessario accedere ai dati locali senza configurare un runtime di integrazione self-hosted, è necessario aggiungere il Azure-SSIS IR a una rete virtuale. Per ulteriori informazioni, vedere [aggiungere un Azure-SSIS IR a una rete virtuale](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Questa impostazione non è applicabile quando si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita di SQL con endpoint privato per ospitare SSISDB. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Per abilitare questa impostazione con PowerShell, vedere [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di database e di server di database SQL di Azure](../azure-sql/database/firewall-configure.md).

  - È possibile connettersi al server di database usando l'autenticazione SQL con le credenziali di amministratore del server o l'autenticazione di Azure AD con l'identità gestita per la data factory. Nel secondo caso è necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per ulteriori informazioni, vedere [abilitare l'autenticazione Azure ad per un Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).

  - Verificare che nel server di database non sia già presente un'istanza di SSISDB. Il provisioning di Azure-SSIS IR non supporta l'uso di un'istanza di SSISDB esistente.

- **Rete virtuale di Azure Resource Manager (facoltativa)**. È necessario avere una rete virtuale di Azure Resource Manager in presenza di almeno una delle condizioni seguenti:

  - Si sta ospitando SSISDB in un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato.

  - Si vuole connettersi agli archivi dati locali dai pacchetti SSIS in esecuzione nel Azure-SSIS IR senza configurare un runtime di integrazione self-hosted.

- **Azure PowerShell (facoltativo)**. Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps) se si vuole eseguire uno script di PowerShell per effettuare il provisioning di Azure-SSIS IR.

### <a name="regional-support"></a>Supporto locale

Per un elenco delle aree di Azure in cui sono disponibili Data Factory e un Azure-SSIS IR, vedere [Data Factory e disponibilità del runtime di integrazione SSIS per area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Confronto tra database SQL e SQL Istanza gestita

La tabella seguente mette a confronto alcune funzionalità di un server di database SQL di Azure e di SQL Istanza gestita in relazione al runtime di integrazione Azure-SSIR:

| Funzionalità | Database SQL| Istanza gestita di SQL |
|---------|--------------|------------------|
| **Pianificazione** | Il SQL Server Agent non è disponibile.<br/><br/>Vedere [pianificare l'esecuzione di un pacchetto in una pipeline Data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agente Istanza gestita disponibile. |
| **autenticazione** | È possibile creare un'istanza di SSISDB con un utente di database indipendente che rappresenta qualsiasi gruppo di Azure AD con l'identità gestita del data factory come membro del ruolo **db_owner** .<br/><br/>Vedere [abilitare l'autenticazione Azure ad per creare un SSISDB nel server di database SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | È possibile creare un'istanza di SSISDB con un utente di database indipendente che rappresenta l'identità gestita del data factory. <br/><br/>Vedere [abilitare l'autenticazione Azure ad per creare un database SSISDB in Azure SQL istanza gestita](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Livello di servizio** | Quando si crea un Azure-SSIS IR con il server di database SQL di Azure, è possibile selezionare il livello di servizio per SSISDB. Sono disponibili più livelli di servizio. | Quando si crea un Azure-SSIS IR con l'istanza gestita, non è possibile selezionare il livello di servizio per il database SSISDB. Tutti i database nell'istanza gestita condividono la stessa risorsa allocata a tale istanza. |
| **Rete virtuale** | Il Azure-SSIS IR può essere aggiunto a una rete virtuale Azure Resource Manager se si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale. | Il Azure-SSIS IR può essere aggiunto a una rete virtuale Azure Resource Manager se si usa un'istanza gestita con endpoint privato. La rete virtuale è obbligatoria quando non si Abilita un endpoint pubblico per l'istanza gestita.<br/><br/>Se si aggiunge il Azure-SSIS IR alla stessa rete virtuale dell'istanza gestita, assicurarsi che l'Azure-SSIS IR si trovi in una subnet diversa dall'istanza gestita. Se si aggiunge il Azure-SSIS IR a una rete virtuale diversa dall'istanza gestita, è consigliabile usare un peering di rete virtuale o una connessione da rete a rete. Vedere [connettere l'applicazione a un istanza gestita di database SQL di Azure](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transazioni distribuite** | Questa funzionalità è supportata tramite le transazioni elastiche. Le transazioni di Microsoft Distributed Transaction Coordinator (MSDTC) non sono supportate. Se i pacchetti SSIS usano MSDTC per coordinare le transazioni distribuite, è consigliabile eseguire la migrazione a transazioni elastiche per il database SQL di Azure. Per altre informazioni, vedere [transazioni distribuite in database cloud](../azure-sql/database/elastic-transactions-overview.md). | Non supportata. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Usare il portale di Azure per creare un runtime di integrazione

In questa sezione si usa il portale di Azure, in particolare l'interfaccia utente Data Factory o l'app, per creare un Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Creare una data factory

Per creare la data factory tramite il portale di Azure, seguire la procedura descritta in [Creare una data factory tramite l'interfaccia utente](./quickstart-create-data-factory-portal.md#create-a-data-factory). Durante questa procedura, selezionare **Aggiungi al dashboard** per un accesso rapido dopo la creazione. 

Una volta creata la data factory, aprire la relativa pagina di panoramica nel portale di Azure. Selezionare il riquadro **crea & monitoraggio** per aprire la relativa pagina **introduttiva** in una scheda separata. In questa pagina è possibile continuare a creare il Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

Nella pagina attività **iniziali** selezionare il riquadro **Configura Integration runtime SSIS** per aprire il riquadro di installazione di **Integration Runtime** .

   ![Riquadro Configure SSIS Integration Runtime (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   Il riquadro **Integration runtime setup** (Configurazione di Integration Runtime) ha tre pagine in cui è possibile configurare in successione le impostazioni generali, di distribuzione e avanzate.

#### <a name="general-settings-page"></a>Pagina Impostazioni generali

Nella pagina **Impostazioni generali** di **Integration runtime setup** (Configurazione di Integration Runtime) completare la procedura seguente.

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Per **Nome** , immettere il nome del runtime di integrazione.

   2. Per **Descrizione** , immettere la descrizione del runtime di integrazione.

   3. Per **Località** selezionare la località del runtime di integrazione. Vengono visualizzate solo le località supportate. È consigliabile selezionare la stessa località del server di database che ospiterà SSISDB.

   4. Per **dimensioni nodo** selezionare le dimensioni del nodo nel cluster di Integration Runtime. Vengono visualizzate solo le dimensioni dei nodi supportate. Selezionare una dimensione del nodo elevata (scalabilità verticale) se si prevede di eseguire numerosi pacchetti con un uso intensivo di calcoli o di memoria.
   > [!NOTE]
   > Se è necessario l' [isolamento del calcolo](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), selezionare le dimensioni del nodo **Standard_E64i_v3** . Questa dimensione del nodo rappresenta le macchine virtuali isolate che utilizzano l'intero host fisico e forniscono il livello di isolamento necessario richiesto da determinati carichi di lavoro, ad esempio i carichi di lavoro IL5 (Department Level 5) degli Stati Uniti.
   
   5. Per **Numero nodo** , selezionare il numero di nodi nel cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei nodi supportati. Selezionare un cluster di grandi dimensioni con molti nodi (aumento del numero di istanze) se si prevede di eseguire numerosi pacchetti in parallelo.

   6. Per **Edition/License** (Edizione/licenza), selezionare l'edizione di SQL Server per il runtime di integrazione: Standard o Enterprise. Selezionare Enterprise se si prevede di usare funzionalità avanzate nel runtime di integrazione.

   7. Per **Risparmio sui costi** , selezionare l'opzione Vantaggio Azure Hybrid per il runtime di integrazione: **Sì** o **No**. Selezionare **Sì** se si vuole usare la propria licenza di SQL Server con Software Assurance per trarre vantaggio dai risparmi sui costi con Hybrid Use.

   8. Selezionare **Avanti**.

#### <a name="deployment-settings-page"></a>Pagina Impostazioni di distribuzione

Nella pagina **Impostazioni di distribuzione** del riquadro **Installazione di Integration Runtime** sono disponibili le opzioni per la creazione di archivi di pacchetti di database SSISDB e/o di Azure-SSIS IR.

##### <a name="creating-ssisdb"></a>Creazione del database SSISDB

Nella pagina **Impostazioni di distribuzione** del riquadro **Installazione di Integration Runtime** selezionare la casella di controllo **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs** (Crea catalogo SSIS (SSISDB) ospitato da Istanza gestita/dal server di database SQL di Azure per archiviare progetti/pacchetti/ambienti/log di esecuzione) per indicare di distribuire i pacchetti in SSISDB (modello di distribuzione del progetto). In alternativa, non è necessario creare il catalogo SSISDB o selezionare la casella di controllo se si vogliono distribuire i pacchetti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di SQL di Azure (modello di distribuzione del pacchetto).

Indipendentemente dal modello di distribuzione, selezionare questa casella di controllo per indicare che si vuole usare SQL Server Agent ospitato da Istanza gestita di SQL di Azure per orchestrare/pianificare le esecuzioni dei pacchetti, perché la distribuzione è abilitata da SSISDB. Per altre informazioni, vedere [Pianificare esecuzioni di pacchetti SSIS tramite l'agente di Istanza gestita del database SQL di Azure](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Se si seleziona la casella di controllo, sarà necessario completare la procedura seguente per usare il proprio server di database per ospitare l'istanza di SSISDB che verrà creata e gestita per conto dell'utente.

   ![Impostazioni di distribuzione per SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure contenente il server di database che ospiterà SSISDB. 

   1. Per **Località** , selezionare la stessa località del server di database che ospiterà SSISDB. È consigliabile selezionare la stessa località del runtime di integrazione.

   1. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. 
   
      In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per materiale sussidiario sulla scelta del tipo di server di database in cui ospitare SSISDB, vedere [Confrontare il database SQL e Istanza gestita di database SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Se per ospitare SSISDB si seleziona il server di database SQL di Azure con regole del firewall per gli indirizzi IP/endpoint servizio di rete virtuale o un'istanza gestita con un endpoint privato oppure se è richiesto l'accesso ai dati in locale senza configurare il runtime di integrazione self-hosted, è necessario aggiungere Azure-SSIS IR a una rete virtuale. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR in una rete virtuale]().

   1. Selezionare la casella di controllo **Use AAD authentication with the managed identity for your ADF** (Usa l'autenticazione di Azure AD con l'identità gestita per Azure Data Factory) per scegliere il metodo di autenticazione per il server di database in cui ospitare SSISDB. Scegliere l'autenticazione SQL o l'autenticazione di AAD con l'identità gestita per la data factory.

      Se si seleziona la casella di controllo, sarà necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR con l'autenticazione di Azure AD]().
   
   1. Per **Nome utente amministratore** , immettere il nome utente di autenticazione SQL per il server di database in cui ospitare SSISDB. 

   1. Per **Password amministratore** , immettere la password di autenticazione SQL per il server di database in cui ospitare SSISDB. 

   1. Per **Catalog Database Service Tier** (Livello di servizio del database di catalogo) selezionare il livello di servizio per il server di database in cui ospitare SSISDB. Selezionare il livello Basic, Standard o Premium oppure il nome di un pool elastico.

Selezionare **Verifica connessione** se applicabile e, in caso di esito positivo, selezionare **Avanti**.

> [!NOTE]
   > Se si usa il server di database SQL di Azure per ospitare SSISDB, i dati verranno archiviati nell'archiviazione con ridondanza geografica per i backup per impostazione predefinita. Se non si vuole che i dati vengano replicati in altre aree, seguire le istruzioni per [configurare la ridondanza dell'archiviazione di backup usando PowerShell](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Creazione di archivi pacchetti Azure-SSIS IR

Nella pagina **Impostazioni di distribuzione** del riquadro **Installazione di Integration Runtime** selezionare la casella di controllo **Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance** (Crea archivi pacchetti per gestire i pacchetti distribuiti nel file system/in File di Azure/nel database SQL Server (MSDB) ospitato da Istanza gestita di SQL di Azure) per scegliere se gestire i pacchetti distribuiti nel database MSDB, nel file system o in File di Azure (modello di distribuzione del pacchetto) con archivi pacchetti Azure-SSIS IR.
   
Gli archivi pacchetti Azure-SSIS IR consentono di importare/esportare/eliminare/eseguire pacchetti e di monitorare/arrestare l'esecuzione di pacchetti tramite SSMS in modo analogo all'[archivio pacchetti SSIS legacy](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). Per altre informazioni, vedere [Gestire i pacchetti SSIS con archivi pacchetti Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).
   
Se si seleziona questa casella di controllo, è possibile aggiungere più archivi pacchetti ad Azure-SSIS IR selezionando **Nuovo**. Viceversa, è possibile condividere un archivio pacchetti tra più istanze di Azure-SSIS IR.

![Impostazioni di distribuzione per MSDB/file system/File di Azure](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

Nel riquadro **Add package store** (Aggiungi archivio pacchetti) completare la procedura seguente.
   
   1. Per **Package store name** (Nome archivio pacchetti) immettere il nome dell'archivio pacchetti. 

   1. Per **Package store linked service** (Servizio collegato archivio pacchetti) selezionare il servizio collegato esistente che archivia le informazioni di accesso per il file system/File di Azure/Istanza gestita di SQL di Azure in cui i pacchetti sono distribuiti o crearne uno nuovo selezionando **Nuovo**. Nel riquadro **New linked service** (Nuovo servizio collegato) completare la procedura seguente.
   
      > [!NOTE]
      > È possibile usare i servizi collegati **Archiviazione file di Azure** o **File system** per accedere a File di Azure. Se si usa il servizio collegato **Archiviazione file di Azure** , l'archivio pacchetti Azure-SSIS IR supporta attualmente solo il metodo di autenticazione **di base** (non **basata sulla chiave dell'account** né **basata sull'URI della firma di accesso condiviso** ). Per usare l'autenticazione **di base** nel servizio collegato **Archiviazione file di Azure** , è possibile accodare `?feature.upgradeAzureFileStorage=false` all'URL del portale ADF nel browser. In alternativa, è possibile usare il servizio collegato **File system** per accedere a File di Azure. 

      ![Impostazioni di distribuzione per i servizi collegati](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Per **Nome** immettere il nome del servizio collegato. 
         
      1. Per **Descrizione** immettere la descrizione del servizio collegato. 
         
      1. Per **Tipo** selezionare **Archiviazione file di Azure** , **Istanza gestita di SQL di Azure** o **File system**.

      1. È possibile ignorare **Connect via integration runtime** (Connetti tramite runtime di integrazione), dal momento che per recuperare le informazioni di accesso per gli archivi pacchetti si usa sempre Azure-SSIS IR.

      1. Se si seleziona **Archiviazione file di Azure** , completare la procedura seguente. 

         1. Per **Account selection method** (Metodo di selezione account) selezionare **From Azure subscription** (Dalla sottoscrizione di Azure) o **Immetti manualmente**.
         
         1. Se si seleziona **From Azure subscription** (Dalla sottoscrizione di Azure), selezionare la **Sottoscrizione di Azure** , il **Nome dell'account di archiviazione** e la **Condivisione file** pertinenti.
            
         1. Se si seleziona **Immetti manualmente** , immettere `\\<storage account name>.file.core.windows.net\<file share name>` per **Host** , `Azure\<storage account name>` per **Nome utente** e `<storage account key>` per **Password** o selezionare l' **Azure Key Vault** in cui queste informazioni sono archiviate come segreto.

      1. Se si seleziona **Istanza gestita di SQL di Azure** , completare la procedura seguente. 

         1. Selezionare **Stringa di connessione** per immetterla manualmente oppure l' **Azure Key Vault** in cui è archiviata come segreto.
         
         1. Se si seleziona **Stringa di connessione** , completare la procedura seguente. 

            1. Per **Nome di dominio completo** immettere rispettivamente `<server name>.<dns prefix>.database.windows.net` o `<server name>.public.<dns prefix>.database.windows.net,3342` come endpoint privato o pubblico, rispettivamente, dell'istanza gestita di SQL di Azure. Se si immette l'endpoint privato, **Verifica connessione** non è applicabile, perché l'interfaccia utente di Azure Data Factory non è in grado di raggiungerlo.

            1. Per **Nome database** immettere `msdb`.
               
            1. Per **Tipo di autenticazione** selezionare **Autenticazione SQL** , **identità gestita** o **Entità servizio**.

            1. Se si seleziona **Autenticazione SQL** immettere il **Nome utente** e la **Password** pertinenti o selezionare l' **Azure Key Vault** in cui tali informazioni sono archiviate come segreto.

            1. Se si seleziona **Identità gestita** , concedere all'identità gestita di Azure Data Factory l'accesso all'Istanza gestita di SQL di Azure.

            1. Se si seleziona **Entità servizio** , immettere **ID entità servizio** e **Chiave dell'entità servizio** pertinenti o selezionare l' **Azure Key Vault** in cui queste informazioni sono archiviate come segreto.

      1. Se si seleziona **File system** , immettere il percorso UNC della cartella in cui sono distribuiti i pacchetti per **Host** e il **Nome utente** e la **Password** pertinenti o selezionare l' **Azure Key Vault** in cui tali informazioni sono archiviate come segreto.

      1. Selezionare **Verifica connessione** se applicabile e, in caso di esito positivo, selezionare **Crea**.

   1. Gli archivi pacchetti aggiunti verranno visualizzati nella pagina **Impostazioni di distribuzione**. Per rimuoverli, selezionare le caselle di controllo corrispondenti e quindi selezionare **Elimina**.

Selezionare **Verifica connessione** se applicabile e, in caso di esito positivo, selezionare **Avanti**.

#### <a name="advanced-settings-page"></a>Pagina di impostazioni avanzate

Nella pagina **Impostazioni avanzate** di **Integration runtime setup** (Configurazione di Integration Runtime) completare la procedura seguente.

   ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vogliono usare più core per eseguire un singolo pacchetto di grandi dimensioni a uso intensivo di calcoli o memoria. Selezionare un numero alto se si vogliono eseguire uno o più pacchetti di piccole dimensioni in un singolo core.

   1. Selezionare la casella di controllo **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Personalizzare Azure-SSIS Integration Runtime con configurazioni di sistema/installazioni di componenti aggiuntive) per aggiungere le impostazioni personalizzate standard o Express in Azure-SSIS IR. Per altre informazioni, vedere [Configurazione personalizzata di Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate per le installazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Per l'URI di firma di accesso condiviso del **contenitore di installazione personalizzata** , immettere l'URI SAS del contenitore in cui vengono archiviati gli script e i file associati per le configurazioni personalizzate standard.

      1. Per l' **installazione personalizzata rapida** , **selezionare nuovo** per aprire il pannello **Aggiungi installazione personalizzata rapida** , quindi selezionare tutti i tipi nel menu a discesa **tipo di installazione personalizzata Express** , ad esempio eseguire il **comando cmdkey** , **aggiungere la variabile di ambiente** , **installare il componente concesso in licenza** e così via.

         Se si seleziona installa il tipo di **componente con licenza** , è possibile selezionare tutti i componenti integrati dai partner ISV nel menu a discesa **nome componente** e, se necessario, immettere il codice Product Key License/caricare il file di licenza del prodotto acquistato da essi nella casella file di licenza del **codice** di licenza / **License file** .
  
         Le impostazioni di aggiunta personalizzate Express verranno visualizzate nella pagina **Impostazioni avanzate** . Per rimuoverli, è possibile selezionare le caselle di controllo e quindi selezionare **Elimina**.

   1. Selezionare la casella di controllo **selezionare un VNet per il Azure-SSIS Integration Runtime da unire, consentire ad ADF di creare determinate risorse di rete e, facoltativamente, inserire gli indirizzi IP pubblici statici** per scegliere se si vuole aggiungere il runtime di integrazione a una rete virtuale. 

      Selezionarla se si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB o se è necessario l'accesso ai dati locali, ovvero se si dispone di origini dati locali o destinazioni nei pacchetti SSIS, senza configurare un runtime di integrazione self-hosted. Per altre informazioni, vedere [aggiungere Azure-SSIS IR a una rete virtuale](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale.

      1. Per **Località** viene selezionata la stessa località del runtime di integrazione.

      1. Per **tipo** selezionare il tipo di rete virtuale: classico o Azure Resource Manager. Si consiglia di selezionare un Azure Resource Manager rete virtuale, perché le reti virtuali classiche saranno presto deprecate.

      1. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Deve corrispondere a quello usato per il server di database SQL di Azure con endpoint di servizio della rete virtuale o istanza gestita con endpoint privato per ospitare SSISDB. Oppure deve essere lo stesso connesso alla rete locale. In caso contrario, può trattarsi di qualsiasi rete virtuale per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

      1. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve corrispondere a quello usato per il server di database SQL di Azure con gli endpoint del servizio rete virtuale per ospitare SSISDB. Oppure deve essere una subnet diversa da quella usata per l'istanza gestita con endpoint privato per l'hosting di SSISDB. In caso contrario, può essere una qualsiasi subnet per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

      1. Selezionare la casella di controllo **Bring static IP Public Addresss for your Azure-SSIS Integration Runtime** per scegliere se si desidera portare gli indirizzi IP pubblici statici per Azure-SSIS IR, in modo da poterli consentire nel firewall per le origini dati.

         Se si seleziona la casella di controllo, completare i passaggi seguenti.

         1. Per il **primo indirizzo IP pubblico statico** selezionare il primo indirizzo IP pubblico statico che soddisfi i requisiti per la Azure-SSIS IR. Se non è disponibile, fare clic su **Crea nuovo** collegamento per creare indirizzi IP pubblici statici in portale di Azure, quindi fare clic sul pulsante Aggiorna qui, in modo da poterli selezionare.
      
         1. Per il **secondo indirizzo IP pubblico statico** selezionare il secondo indirizzo IP pubblico statico che soddisfa i requisiti per la Azure-SSIS IR. Se non è disponibile, fare clic su **Crea nuovo** collegamento per creare indirizzi IP pubblici statici in portale di Azure, quindi fare clic sul pulsante Aggiorna qui, in modo da poterli selezionare.

   1. Selezionare la casella di controllo **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Configurare un runtime di integrazione self-hosted come proxy per Azure-SSIS Integration Runtime) per scegliere se configurare un runtime di integrazione self-hosted come proxy per Azure-SSIS IR. Per altre informazioni, vedere [Configurare un runtime di integrazione self-hosted come proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con un runtime di integrazione self-hosted](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Per **Integration runtime indipendenti** , selezionare il runtime di integrazione self-hosted esistente come proxy per Azure-SSIS IR.

      1. Per **servizio collegato archiviazione di staging** selezionare il servizio collegato di archiviazione BLOB di Azure esistente o crearne uno nuovo per la gestione temporanea.

      1. Per **percorso di gestione temporanea** specificare un contenitore BLOB nell'account di archiviazione BLOB di Azure selezionato o lasciarlo vuoto per usare uno predefinito per la gestione temporanea.

   1. Selezionare **convalida VNet**  >  **continua**. 

Nella sezione **Riepilogo** esaminare tutte le impostazioni di provisioning, aggiungere un segnalibro per i collegamenti consigliati alla documentazione e fare clic su **Fine** per avviare la creazione del runtime di integrazione.

   > [!NOTE]
   > Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, potrebbero essere necessari 20-30 minuti prima che il Azure-SSIS IR si unisca a una rete virtuale.
   >
   > Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il Azure-SSIS IR alla rete virtuale.
   > 
   > Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per altre informazioni sui componenti predefiniti/preinstallati, vedere [Componenti predefiniti e preinstallati in Azure-SSIS Integration Runtime](./built-in-preinstalled-components-ssis-integration-runtime.md). Per altre informazioni sui componenti aggiuntivi che è possibile installare, vedere [Configurazioni personalizzate per Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Riquadro Connessioni

Nel riquadro **Connessioni** dell'hub **Gestione** , passare alla pagina **Integration runtimes** (Runtime di integrazione) e selezionare **Aggiorna**. 

   ![Riquadro Connessioni](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   È possibile modificare o riconfigurare l'istanza di Azure-SSIS IR selezionando il nome corrispondente. È anche possibile selezionare i pulsanti pertinenti per monitorare/avviare/arrestare/eliminare l'istanza di Azure-SSIS IR, generare automaticamente una pipeline di Azure Data Factory con l'attività Esegui pacchetto SSIS per l'esecuzione nell'istanza di Azure-SSIS IR e visualizzare il codice o il payload JSON di tale istanza.  È possibile modificare o eliminare l'istanza di Azure-SSIS IR solo quando è arrestata.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime di integrazione SSIS di Azure nel portale

1. Nell'interfaccia utente di Azure Data Factory passare alla scheda **Modifica** e selezionare **Connessioni**. Quindi passare alla scheda **Runtime di integrazione** per visualizzare i runtime di integrazione attualmente presenti nella data factory.

   ![Visualizzare i runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selezionare **nuovo** per creare una nuova Azure-SSIS IR e aprire il riquadro di **installazione di Integration Runtime** .

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Nel riquadro **Integration runtime setup** (Configurazione di Integration Runtime) selezionare il riquadro **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi selezionare **Avanti**.

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Per i passaggi rimanenti per configurare una Azure-SSIS IR, vedere la sezione effettuare il [provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Usare Azure PowerShell per creare un runtime di integrazione

In questa sezione si userà Azure PowerShell per creare una Azure-SSIS IR.

### <a name="create-variables"></a>Creare le variabili

Copiare e incollare lo script seguente. Specificare valori per le variabili. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Accedere e selezionare una sottoscrizione

Aggiungere lo script seguente per accedere e selezionare la sottoscrizione di Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Convalidare la connessione al server di database

Aggiungere lo script seguente per convalidare il server di database SQL di Azure o l'istanza gestita.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
}
```

### <a name="configure-the-virtual-network"></a>Configurare la rete virtuale

Aggiungere lo script seguente per configurare automaticamente le autorizzazioni e le impostazioni della rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure.

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

Creare un [Gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Se il gruppo di risorse esiste già, non copiare questo codice nello script. 

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

Se non si utilizza SSISDB, è possibile omettere `CatalogServerEndpoint` i `CatalogPricingTier` parametri, e `CatalogAdminCredential` .

Se non si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB o se è necessario l'accesso ai dati locali, è possibile omettere i `VNetId` `Subnet` parametri e o passare i valori vuoti. È anche possibile ometterli se si configura un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR per accedere ai dati in locale. In caso contrario, non sarà possibile ometterli e passare i valori validi dalla configurazione della rete virtuale. Per ulteriori informazioni, vedere [aggiungere un Azure-SSIS IR a una rete virtuale](./join-azure-ssis-integration-runtime-virtual-network.md).

Se si utilizza un'istanza gestita per ospitare SSISDB, è possibile omettere il `CatalogPricingTier` parametro o passarvi un valore vuoto. In caso contrario, non è possibile ometterlo e passare un valore valido dall'elenco dei piani tariffari supportati per il database SQL di Azure. Per altre informazioni, vedere [limiti delle risorse del database SQL](../azure-sql/database/resource-limits-logical-server.md).

Se si utilizza Azure AD autenticazione con l'identità gestita per la data factory per la connessione al server di database, è possibile omettere il `CatalogAdminCredential` parametro. È tuttavia necessario aggiungere l'identità gestita per la data factory in un gruppo Azure AD con le autorizzazioni di accesso al server di database. Per ulteriori informazioni, vedere [abilitare l'autenticazione Azure ad per un Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md). In caso contrario, non è possibile ometterlo e passare un oggetto valido formato dal nome utente dell'amministratore del server e dalla password per l'autenticazione SQL.

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
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Avviare Integration Runtime

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
> Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, potrebbero essere necessari 20-30 minuti prima che il Azure-SSIS IR si unisca a una rete virtuale.
>
> Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il Azure-SSIS IR alla rete virtuale.
> 
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per altre informazioni sui componenti predefiniti/preinstallati, vedere [Componenti predefiniti e preinstallati in Azure-SSIS Integration Runtime](./built-in-preinstalled-components-ssis-integration-runtime.md). Per altre informazioni sui componenti aggiuntivi che è possibile installare, vedere [Configurazioni personalizzate per Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Di seguito è riportato lo script completo che consente di creare un runtime di integrazione SSIS di Azure.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
}

### Configure a virtual network
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
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Usare un modello di Azure Resource Manager per creare un runtime di integrazione

In questa sezione si userà un modello di Azure Resource Manager per creare il runtime di integrazione Azure-SSIS. Ecco una procedura dettagliata di esempio:

1. Creare un file JSON con il modello di Resource Manager di Azure seguente. Sostituire i valori nelle parentesi acute (segnaposto) con valori personalizzati.

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

2. Per distribuire il modello di Azure Resource Manager, eseguire il `New-AzResourceGroupDeployment` comando come illustrato nell'esempio seguente. Nell'esempio `ADFTutorialResourceGroup` è il nome del gruppo di risorse. `ADFTutorialARM.json` è il file che contiene la definizione JSON per il data factory e il Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Questo comando crea il data factory e Azure-SSIS IR, ma non avvia il runtime di integrazione.

3. Per avviare la Azure-SSIS IR, eseguire il `Start-AzDataFactoryV2IntegrationRuntime` comando:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, potrebbero essere necessari 20-30 minuti prima che il Azure-SSIS IR si unisca a una rete virtuale.
>
> Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il Azure-SSIS IR alla rete virtuale.
> 
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per altre informazioni sui componenti predefiniti/preinstallati, vedere [Componenti predefiniti e preinstallati in Azure-SSIS Integration Runtime](./built-in-preinstalled-components-ssis-integration-runtime.md). Per altre informazioni sui componenti aggiuntivi che è possibile installare, vedere [Configurazioni personalizzate per Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS

Se si usa SSISDB, è possibile distribuirvi i pacchetti ed eseguirli in Azure-SSIS IR tramite gli strumenti SSDT o SSMS abilitati per Azure. Questi strumenti si connettono al server di database tramite l'endpoint del server: 

- Per un server di database SQL di Azure, il formato dell'endpoint è `<server name>.database.windows.net`.
- Per un'istanza gestita con un endpoint privato, il formato dell'endpoint è `<server name>.<dns prefix>.database.windows.net`.
- Per un'istanza gestita con un endpoint pubblico, il formato dell'endpoint è `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se non si usa SSISDB, è possibile distribuire i pacchetti nel file system, in File di Azure o nel database MSDB ospitato nell'Istanza gestita di SQL di Azure ed eseguirli in Azure-SSIS IR usando le utilità da riga di comando [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Per altre informazioni, vedere [Distribuire progetti/pacchetti SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-ver15).

In entrambi i casi, è anche possibile eseguire i pacchetti distribuiti in Azure-SSIS IR tramite l'attività di esecuzione di pacchetti SSIS nelle pipeline di Data Factory. Per altre informazioni, vedere [Richiamare l'esecuzione di pacchetti SSIS come attività di Data Factory di prima classe](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere altri Azure-SSIS IR argomenti in questa documentazione:

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo fornisce informazioni sui runtime di integrazione in generale, tra cui Azure-SSIS IR.
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come recuperare e comprendere le informazioni sul Azure-SSIS IR.
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare i Azure-SSIS IR. Viene inoltre illustrato come scalare il Azure-SSIS IR aggiungendo altri nodi.
- [Distribuire ed eseguire un pacchetto di SQL Server Integration Services (SSIS) in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Pianificare l'esecuzione dei pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
