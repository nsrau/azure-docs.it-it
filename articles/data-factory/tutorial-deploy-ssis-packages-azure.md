---
title: Effettuare il provisioning di Azure-SSIS Integration Runtime
description: Informazioni su come eseguire il provisioning del runtime di integrazione Azure-SSIS in Azure Data Factory in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 24ae71206188dc6d60f6a37629ad55ae4d4c1567
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015363"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Effettuare il provisioning di Azure-SSIS Integration Runtime in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questa esercitazione illustra la procedura per l'uso del portale di Azure per il provisioning di Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory. Azure-SSIS IR supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto)
- Esecuzione di pacchetti distribuiti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di database SQL di Azure (modello di distribuzione del pacchetto)

Dopo aver effettuato il provisioning di un'istanza di Azure-SSIS IR, è possibile usare i consueti strumenti per distribuire ed eseguire i pacchetti in Azure. Questi strumenti, già abilitati per Azure e includono SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) e utilità da riga di comando come [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) e [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec).

Per informazioni concettuali in proposito, vedere la [panoramica del runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

In questa esercitazione si completa la procedura seguente:

> [!div class="checklist"]
> * Creare una data factory.
> * Effettuare il provisioning di un runtime di integrazione SSIS di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- **Server di database SQL di Azure (facoltativo)** . Se non si ha ancora un server di database, crearne uno nel portale di Azure prima di iniziare. Data Factory creerà a sua volta un'istanza di SSISDB in questo server di database. 

  È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure.

  Tenere presenti questi punti:

  - In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per materiale sussidiario sulla scelta del tipo di server di database in cui ospitare SSISDB, vedere [Confrontare il database SQL e Istanza gestita di database SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Se per ospitare SSISDB si usa il server di database SQL di Azure con regole del firewall per gli indirizzi IP/endpoint servizio di rete virtuale o un'istanza gestita con un endpoint privato oppure se è richiesto l'accesso ai dati in locale senza configurare il runtime di integrazione self-hosted, è necessario aggiungere Azure-SSIS IR a una rete virtuale. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR in una rete virtuale](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Questa impostazione non è applicabile se per ospitare SSISDB si usa un server di database SQL con regole del firewall per gli indirizzi IP/endpoint del servizio di rete virtuale o un'istanza gestita con un endpoint privato. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Per abilitare questa impostazione con PowerShell, vedere [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di database e di server di database SQL di Azure](../sql-database/sql-database-firewall-configure.md).

  - È possibile connettersi al server di database usando l'autenticazione SQL con le credenziali di amministratore del server o l'autenticazione di Azure AD con l'identità gestita per la data factory. Nel secondo caso è necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR con l'autenticazione di Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Verificare che nel server di database non sia già presente un'istanza di SSISDB. Il provisioning di Azure-SSIS IR non supporta l'uso di un'istanza di SSISDB esistente.

> [!NOTE]
> Per un elenco delle aree di Azure in cui sono attualmente disponibili Data Factory e Azure-SSIS IR, vedere [Disponibilità di Data Factory e SSIS IR in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Creare una data factory

Per creare la data factory tramite il portale di Azure, seguire la procedura descritta in [Creare una data factory tramite l'interfaccia utente](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Durante questa procedura, selezionare **Aggiungi al dashboard** per un accesso rapido dopo la creazione. 

Una volta creata la data factory, aprire la relativa pagina di panoramica nel portale di Azure. Selezionare il riquadro **Crea e monitora** per aprire la pagina **Attività iniziali** in una nuova scheda. Qui è possibile continuare a creare l'istanza di Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure

### <a name="from-the-data-factory-overview"></a>Dalla panoramica di Data Factory

1. Nella pagina **Attività iniziali** selezionare il riquadro **Configure SSIS Integration Runtime** (Configura SSIS Integration Runtime). 

   ![Riquadro "Configure SSIS Integration Runtime" (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Nell'interfaccia utente di creazione

1. Nell'interfaccia utente di Azure Data Factory passare alla scheda **Modifica** e selezionare **Connessioni**. Quindi passare alla scheda **Runtime di integrazione** per visualizzare i runtime di integrazione attualmente presenti nella data factory. 

   ![Selezioni per la visualizzazione di runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selezionare **Nuovo** per creare un Azure-SSIS IR e aprire il riquadro **Integration Runtime Setup** (Configurazione di Integration Runtime). 

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Nel riquadro **Integration runtime setup** (Configurazione di Integration Runtime) selezionare il riquadro **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi selezionare **Avanti**.

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

Il riquadro **Integration runtime setup** (Configurazione di Integration Runtime) ha tre pagine in cui è possibile configurare in successione le impostazioni generali, di distribuzione e avanzate.

### <a name="general-settings-page"></a>Pagina Impostazioni generali

Nella pagina **Impostazioni generali** di **Integration runtime setup** (Configurazione di Integration Runtime) completare la procedura seguente. 

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Per **Nome**, immettere il nome del runtime di integrazione. 

   1. Per **Descrizione**, immettere la descrizione del runtime di integrazione. 

   1. Per **Località** selezionare la località del runtime di integrazione. Vengono visualizzate solo le località supportate. È consigliabile selezionare la stessa località del server di database che ospiterà SSISDB. 

   1. Per **Dimensioni del nodo**, selezionare la dimensione del nodo nel cluster del runtime di integrazione. Vengono visualizzate solo le dimensioni dei nodi supportate. Selezionare una dimensione del nodo elevata (scalabilità verticale) se si prevede di eseguire numerosi pacchetti con un uso intensivo di calcoli o di memoria. 

   1. Per **Numero nodo**, selezionare il numero di nodi nel cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei nodi supportati. Selezionare un cluster di grandi dimensioni con molti nodi (aumento del numero di istanze) se si prevede di eseguire numerosi pacchetti in parallelo. 

   1. Per **Edition/License** (Edizione/licenza), selezionare l'edizione di SQL Server per il runtime di integrazione: Standard o Enterprise. Selezionare Enterprise se si prevede di usare funzionalità avanzate nel runtime di integrazione. 

   1. Per **Risparmio sui costi**, selezionare l'opzione Vantaggio Azure Hybrid per il runtime di integrazione: **Sì** o **No**. Selezionare **Sì** se si vuole usare la propria licenza di SQL Server con Software Assurance per trarre vantaggio dai risparmi sui costi con Hybrid Use. 

   1. Selezionare **Avanti**. 

### <a name="deployment-settings-page"></a>Pagina Impostazioni di distribuzione

Nella pagina **Impostazioni di distribuzione** del riquadro **Installazione di Integration Runtime** sono disponibili le opzioni per la creazione di archivi di pacchetti di database SSISDB e/o di Azure-SSIS IR.

#### <a name="creating-ssisdb"></a>Creazione del database SSISDB

Nella pagina **Impostazioni di distribuzione** del riquadro **Installazione di Integration Runtime** selezionare la casella di controllo **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs** (Crea catalogo SSIS (SSISDB) ospitato da Istanza gestita/dal server di database SQL di Azure per archiviare progetti/pacchetti/ambienti/log di esecuzione) per indicare di distribuire i pacchetti in SSISDB (modello di distribuzione del progetto). In alternativa, non è necessario creare il catalogo SSISDB o selezionare la casella di controllo se si vogliono distribuire i pacchetti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di SQL di Azure (modello di distribuzione del pacchetto).

Indipendentemente dal modello di distribuzione, selezionare questa casella di controllo per indicare che si vuole usare SQL Server Agent ospitato da Istanza gestita di SQL di Azure per orchestrare/pianificare le esecuzioni dei pacchetti, perché la distribuzione è abilitata da SSISDB. Per altre informazioni, vedere [Pianificare esecuzioni di pacchetti SSIS tramite l'agente di Istanza gestita del database SQL di Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent).
   
Se si seleziona la casella di controllo, sarà necessario completare la procedura seguente per usare il proprio server di database per ospitare l'istanza di SSISDB che verrà creata e gestita per conto dell'utente.

   ![Impostazioni di distribuzione per SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure contenente il server di database che ospiterà SSISDB. 

   1. Per **Località**, selezionare la stessa località del server di database che ospiterà SSISDB. È consigliabile selezionare la stessa località del runtime di integrazione.

   1. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. 
   
      In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per materiale sussidiario sulla scelta del tipo di server di database in cui ospitare SSISDB, vedere [Confrontare il database SQL e Istanza gestita di database SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Se per ospitare SSISDB si seleziona il server di database SQL di Azure con regole del firewall per gli indirizzi IP/endpoint servizio di rete virtuale o un'istanza gestita con un endpoint privato oppure se è richiesto l'accesso ai dati in locale senza configurare il runtime di integrazione self-hosted, è necessario aggiungere Azure-SSIS IR a una rete virtuale. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR in una rete virtuale](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   1. Selezionare la casella di controllo **Use AAD authentication with the managed identity for your ADF** (Usa l'autenticazione di Azure AD con l'identità gestita per Azure Data Factory) per scegliere il metodo di autenticazione per il server di database in cui ospitare SSISDB. Scegliere l'autenticazione SQL o l'autenticazione di AAD con l'identità gestita per la data factory.

      Se si seleziona la casella di controllo, sarà necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR con l'autenticazione di Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   1. Per **Nome utente amministratore**, immettere il nome utente di autenticazione SQL per il server di database in cui ospitare SSISDB. 

   1. Per **Password amministratore**, immettere la password di autenticazione SQL per il server di database in cui ospitare SSISDB. 

   1. Per **Catalog Database Service Tier** (Livello di servizio del database di catalogo) selezionare il livello di servizio per il server di database in cui ospitare SSISDB. Selezionare il livello Basic, Standard o Premium oppure il nome di un pool elastico.

Selezionare **Verifica connessione** se applicabile e, in caso di esito positivo, selezionare **Avanti**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Creazione di archivi pacchetti Azure-SSIS IR

Nella pagina **Impostazioni di distribuzione** del riquadro **Installazione di Integration Runtime** selezionare la casella di controllo **Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance** (Crea archivi pacchetti per gestire i pacchetti distribuiti nel file system/in File di Azure/nel database SQL Server (MSDB) ospitato da Istanza gestita di SQL di Azure) per scegliere se gestire i pacchetti distribuiti nel database MSDB, nel file system o in File di Azure (modello di distribuzione del pacchetto) con archivi pacchetti Azure-SSIS IR.
   
Gli archivi pacchetti Azure-SSIS IR consentono di importare/esportare/eliminare/eseguire pacchetti e di monitorare/arrestare l'esecuzione di pacchetti tramite SSMS in modo analogo all'[archivio pacchetti SSIS legacy](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). Per altre informazioni, vedere [Gestire i pacchetti SSIS con archivi pacchetti Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store).
   
Se si seleziona questa casella di controllo, è possibile aggiungere più archivi pacchetti ad Azure-SSIS IR selezionando **Nuovo**. Viceversa, è possibile condividere un archivio pacchetti tra più istanze di Azure-SSIS IR.

![Impostazioni di distribuzione per MSDB/file system/File di Azure](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

Nel riquadro **Add package store** (Aggiungi archivio pacchetti) completare la procedura seguente.
   
   1. Per **Package store name** (Nome archivio pacchetti) immettere il nome dell'archivio pacchetti. 

   1. Per **Package store linked service** (Servizio collegato archivio pacchetti) selezionare il servizio collegato esistente che archivia le informazioni di accesso per il file system/File di Azure/Istanza gestita di SQL di Azure in cui i pacchetti sono distribuiti o crearne uno nuovo selezionando **Nuovo**. Nel riquadro **New linked service** (Nuovo servizio collegato) completare la procedura seguente. 

      > [!NOTE]
      > È possibile usare i servizi collegati **Archiviazione file di Azure** o **File system** per accedere a File di Azure. Se si usa il servizio collegato **Archiviazione file di Azure**, l'archivio pacchetti Azure-SSIS IR supporta attualmente solo il metodo di autenticazione **di base** (non **basata sulla chiave dell'account** né **basata sull'URI della firma di accesso condiviso**). Per usare l'autenticazione **di base** nel servizio collegato **Archiviazione file di Azure**, è possibile accodare `?feature.upgradeAzureFileStorage=false` all'URL del portale ADF nel browser. In alternativa, è possibile usare il servizio collegato **File system** per accedere a File di Azure. 

      ![Impostazioni di distribuzione per i servizi collegati](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Per **Nome** immettere il nome del servizio collegato. 
         
      1. Per **Descrizione** immettere la descrizione del servizio collegato. 
         
      1. Per **Tipo** selezionare **Archiviazione file di Azure**, **Istanza gestita di SQL di Azure** o **File system**.

      1. È possibile ignorare **Connect via integration runtime** (Connetti tramite runtime di integrazione), dal momento che per recuperare le informazioni di accesso per gli archivi pacchetti si usa sempre Azure-SSIS IR.

      1. Se si seleziona **Archiviazione file di Azure**, completare la procedura seguente. 

         1. Per **Account selection method**(Metodo di selezione account) selezionare **From Azure subscription** (Dalla sottoscrizione di Azure) o **Immetti manualmente**.
         
         1. Se si seleziona **From Azure subscription**(Dalla sottoscrizione di Azure), selezionare la **Sottoscrizione di Azure**, il **Nome dell'account di archiviazione** e la **Condivisione file** pertinenti.
            
         1. Se si seleziona **Immetti manualmente**, immettere `\\<storage account name>.file.core.windows.net\<file share name>` per **Host**, `Azure\<storage account name>` per **Nome utente** e `<storage account key>` per **Password** o selezionare l'**Azure Key Vault** in cui queste informazioni sono archiviate come segreto.

      1. Se si seleziona **Istanza gestita di SQL di Azure**, completare la procedura seguente. 

         1. Selezionare **Stringa di connessione** per immetterla manualmente oppure l'**Azure Key Vault** in cui è archiviata come segreto.
         
         1. Se si seleziona **Stringa di connessione**, completare la procedura seguente. 

            1. Per **Nome di dominio completo** immettere rispettivamente `<server name>.<dns prefix>.database.windows.net` o `<server name>.public.<dns prefix>.database.windows.net,3342` come endpoint privato o pubblico, rispettivamente, dell'istanza gestita di SQL di Azure. Se si immette l'endpoint privato, **Verifica connessione** non è applicabile, perché l'interfaccia utente di Azure Data Factory non è in grado di raggiungerlo.

            1. Per **Nome database** immettere `msdb`.
               
            1. Per **Tipo di autenticazione** selezionare **Autenticazione SQL**, **identità gestita** o **Entità servizio**.

            1. Se si seleziona **Autenticazione SQL** immettere il **Nome utente** e la **Password** pertinenti o selezionare l'**Azure Key Vault** in cui tali informazioni sono archiviate come segreto.

            1. Se si seleziona **Identità gestita**, concedere all'identità gestita di Azure Data Factory l'accesso all'Istanza gestita di SQL di Azure.

            1. Se si seleziona **Entità servizio**, immettere **ID entità servizio** e **Chiave dell'entità servizio** pertinenti o selezionare l'**Azure Key Vault** in cui queste informazioni sono archiviate come segreto.

      1. Se si seleziona **File system**, immettere il percorso UNC della cartella in cui sono distribuiti i pacchetti per **Host** e il **Nome utente** e la **Password** pertinenti o selezionare l'**Azure Key Vault** in cui tali informazioni sono archiviate come segreto.

      1. Selezionare **Verifica connessione** se applicabile e, in caso di esito positivo, selezionare **Crea**.

   1. Gli archivi pacchetti aggiunti verranno visualizzati nella pagina **Impostazioni di distribuzione**. Per rimuoverli, selezionare le caselle di controllo corrispondenti e quindi selezionare **Elimina**.

Selezionare **Verifica connessione** se applicabile e, in caso di esito positivo, selezionare **Avanti**.

### <a name="advanced-settings-page"></a>Pagina di impostazioni avanzate

Nella pagina **Impostazioni avanzate** di **Integration runtime setup** (Configurazione di Integration Runtime) completare la procedura seguente. 

   ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vogliono usare più core per eseguire un singolo pacchetto di grandi dimensioni a uso intensivo di calcoli o memoria. Selezionare un numero alto se si vogliono eseguire uno o più pacchetti di piccole dimensioni in un singolo core. 

   1. Selezionare la casella di controllo **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Personalizzare Azure-SSIS Integration Runtime con configurazioni di sistema/installazioni di componenti aggiuntive) per aggiungere le impostazioni personalizzate standard o Express in Azure-SSIS IR. Per altre informazioni, vedere [Configurazione personalizzata di Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Selezionare la casella di controllo **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (Selezionare una rete virtuale a cui aggiungere Azure-SSIS Integration Runtime, consentire a Azure Data Factory di creare determinate risorse di rete e facoltativamente usare indirizzi IP pubblici statici personalizzati) per scegliere se si vuole aggiungere Azure-SSIS IR a una rete virtuale.

      Selezionarla se per ospitare SSISDB si usa il server di database SQL di Azure con regole del firewall per gli indirizzi IP/endpoint servizio di rete virtuale o un'istanza gestita con un endpoint privato oppure se è richiesto l'accesso ai dati in locale senza configurare il runtime di integrazione self-hosted. Per altre informazioni, vedere [Creare un'istanza di Azure-SSIS IR in una rete virtuale](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Selezionare la casella di controllo **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Configurare un runtime di integrazione self-hosted come proxy per Azure-SSIS Integration Runtime) per scegliere se configurare un runtime di integrazione self-hosted come proxy per Azure-SSIS IR. Per altre informazioni, vedere [Configurare un runtime di integrazione self-hosted come proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Selezionare **Continua**. 

Nella pagina **Riepilogo** del riquadro **Integration runtime setup** (Configurazione di Integration Runtime) esaminare tutte le impostazioni di provisioning, aggiungere un segnalibro per i collegamenti consigliati alla documentazione e fare clic su **Fine** per avviare la creazione del runtime di integrazione. 

   > [!NOTE]
   > Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti.
   >
   > Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. 
   > 
   > Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per altre informazioni sui componenti predefiniti/preinstallati, vedere [Componenti predefiniti e preinstallati in Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime). Per altre informazioni sui componenti aggiuntivi che è possibile installare, vedere [Configurazioni personalizzate per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

### <a name="connections-pane"></a>Riquadro Connessioni

Nel riquadro **Connessioni** dell'hub **Gestione**, passare alla pagina **Integration runtimes** (Runtime di integrazione) e selezionare **Aggiorna**. 

   ![Riquadro Connessioni](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   È possibile modificare o riconfigurare l'istanza di Azure-SSIS IR selezionando il nome corrispondente. È anche possibile selezionare i pulsanti pertinenti per monitorare/avviare/arrestare/eliminare l'istanza di Azure-SSIS IR, generare automaticamente una pipeline di Azure Data Factory con l'attività Esegui pacchetto SSIS per l'esecuzione nell'istanza di Azure-SSIS IR e visualizzare il codice o il payload JSON di tale istanza.  È possibile modificare o eliminare l'istanza di Azure-SSIS IR solo quando è arrestata.

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS

Se si usa SSISDB, è possibile distribuirvi i pacchetti ed eseguirli in Azure-SSIS IR tramite gli strumenti SSDT o SSMS abilitati per Azure. Questi strumenti si connettono al server di database tramite l'endpoint del server: 

- Per un server di database SQL di Azure, il formato dell'endpoint è `<server name>.database.windows.net`.
- Per un'istanza gestita con un endpoint privato, il formato dell'endpoint è `<server name>.<dns prefix>.database.windows.net`.
- Per un'istanza gestita con un endpoint pubblico, il formato dell'endpoint è `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se non si usa SSISDB, è possibile distribuire i pacchetti nel file system, in File di Azure o nel database MSDB ospitato nell'Istanza gestita di SQL di Azure ed eseguirli in Azure-SSIS IR usando le utilità da riga di comando [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) e [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec). 

Per altre informazioni, vedere [Distribuire progetti/pacchetti SSIS](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-ver15).

In entrambi i casi, è anche possibile eseguire i pacchetti distribuiti in Azure-SSIS IR tramite l'attività di esecuzione di pacchetti SSIS nelle pipeline di Data Factory. Per altre informazioni, vedere [Richiamare l'esecuzione di pacchetti SSIS come attività di Data Factory di prima classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Vedere anche la documentazione di SSIS seguente: 

- [Distribuire ed eseguire un pacchetto di SQL Server Integration Services (SSIS) in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Connettersi al catalogo SSIS (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pianificare l'esecuzione dei pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla personalizzazione di Azure-SSIS Integration Runtime, vedere l'articolo seguente: 

> [!div class="nextstepaction"]
> [Personalizzare Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)