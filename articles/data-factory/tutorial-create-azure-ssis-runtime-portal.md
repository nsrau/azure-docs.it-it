---
title: Effettuare il provisioning di un runtime di integrazione SSIS di Azure tramite il portale | Microsoft Docs
description: Questo articolo spiega come creare un runtime di integrazione SSIS di Azure tramite il portale di Azure.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure tramite l'interfaccia utente di Azure Data Factory
Questa esercitazione illustra la procedura per effettuare il provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory tramite il portale di Azure. È quindi possibile usare SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire pacchetti SQL Server Integration Services (SSIS) in questo runtime in Azure. Per informazioni concettuali in proposito, vedere la [panoramica del runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare e avviare un runtime di integrazione SSIS di Azure

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>prerequisiti
- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. 
- **Server di database SQL di Azure**. Se non si ha già un server di database, crearne uno nel portale di Azure prima di iniziare. Azure Data Factory crea il database del catalogo SSIS (SSISDB) in questo server di database. È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure. 
    - Verificare che l'opzione "**Consenti l'accesso a Servizi di Azure**" sia abilitata per il server di database. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Per abilitare questa impostazione usando PowerShell, vedere [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](../sql-database/sql-database-firewall-configure.md).
    - Verificare che il server di database SQL di Azure non abbia un catalogo SSIS (database SSISDB). Il provisioning del runtime di integrazione SSIS di Azure non supporta l'uso di un catalogo SSIS esistente.
 
## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Accedere al [Portale di Azure](https://portal.azure.com/).    
2. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
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

## <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

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
5. Questo passaggio è **facoltativo**. Se è presente una rete virtuale classica a cui si vuole aggiungere il runtime di integrazione, selezionare l'opzione **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Selezionare una rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure e consentire ai servizi di Azure di configurare le autorizzazioni/impostazioni della rete virtuale) e seguire questa procedura: 

    ![Impostazioni avanzate per la rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Specificare la **sottoscrizione** contenente la rete virtuale classica. 
    2. Selezionare la **Rete virtuale**. <br/>
    4. Selezionare la **Subnet**.<br/> 
1. Fare clic su **Fine** per avviare la creazione del runtime di integrazione SSIS di Azure. 

    > [!IMPORTANT]
    > - Il completamento di questo processo richiede circa 20 minuti
    > - Il servizio Data Factory si connette al database SQL di Azure per preparare il database del catalogo SSIS (SSISDB). Lo script configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificata, e aggiunge la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.
    > - Quando si effettua il provisioning di un'istanza del database SQL per ospitare SSISDB, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati supportate dai componenti predefiniti. Non è attualmente possibile installare componenti di terze parti per SSIS (inclusi i componenti di terze parti di Microsoft, ad esempio i componenti Oracle e Teradata di Attunity e i componenti SAP BI).

7. Nella finestra **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Fare clic su **Aggiorna** per aggiornare lo stato. 

    ![Stato creazione](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Usare i collegamenti nella colonna **Azioni** per monitorare, arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato. 

    ![Azure SSIS Integration Runtime - Azioni](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Fare clic sul collegamento **Monitoraggio** in **Azioni**.  

    ![Azure SSIS Integration Runtime - Dettagli](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Se si è verificato un **errore associato** ad Azure SSIS Integration Runtime, in questa pagina verrà visualizzato il numero di errori con il collegamento per visualizzare i dettagli sull'errore. Ad esempio, se il catalogo SSIS esiste già nel server di database, verrà visualizzato un errore che indica che il database SSISDB esiste già.  
11. Fare clic su **Integration Runtimes** (Runtime di integrazione) nella parte superiore per tornare alla pagina precedente e visualizzare tutti i runtime di integrazione associati alla data factory.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime di integrazione SSIS di Azure nel portale

1. Nell'interfaccia utente di Azure Data Factory, passare alla scheda **Modifica**, fare clic su **Connessioni** e quindi selezionare la scheda **Integration Runtimes** (Runtime di integrazione) per visualizzare i runtime di integrazione esistenti nella data factory. 
    ![Visualizzare i runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Fare clic su **Nuovo** per creare un nuovo runtime di integrazione SSIS di Azure. 

    ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Per creare un runtime di integrazione SSIS di Azure, fare clic su **Nuovo** come mostrato nell'immagine. 
3. Nella finestra Integration Runtime Setup (Installazione di Integration Runtime) selezionare **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi fare clic su **Avanti**.

    ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS
Usare ora SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire i pacchetti SSIS in Azure. Connettersi al server SQL di Azure che ospita il catalogo SSIS (SSISDB). Il nome del server SQL di Azure ha il formato `<servername>.database.windows.net` (per il database SQL di Azure). 

Vedere gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire, eseguire e monitorare un pacchetto SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pianificare l'esecuzione di pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare e avviare un runtime di integrazione SSIS di Azure

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati nel cloud](tutorial-copy-data-portal.md)
