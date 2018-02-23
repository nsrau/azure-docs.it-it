---
title: Effettuare il provisioning di un runtime di integrazione SSIS con Azure Data Factory | Microsoft Docs
description: Questo articolo spiega come creare un runtime di integrazione SSIS di Azure tramite Azure Data Factory.
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
ms.date: 01/29/2018
ms.author: spelluru
ms.openlocfilehash: 9e0b0efdf28e6d8b99d1cdf702dd0698ad87da7b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-azure-data-factory-ui"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure tramite l'interfaccia utente di Azure Data Factory
Questa esercitazione illustra la procedura per effettuare il provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory tramite il portale di Azure. È quindi possibile usare SQL Server Data Tools o SQL Server Management Studio per distribuire pacchetti SQL Server Integration Services (SSIS) in questo runtime in Azure. Per informazioni concettuali in proposito, vedere la [panoramica del runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

In questa esercitazione si completa la procedura seguente:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare e avviare un runtime di integrazione SSIS di Azure.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione di Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>prerequisiti
- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 
- **Server di database SQL di Azure**. Se non si ha già un server di database, crearne uno nel portale di Azure prima di iniziare. Azure Data Factory crea il catalogo SSIS (database SSISDB) in questo server di database. È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione nel database SSISDB senza attraversare aree di Azure. 
   - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Per abilitare questa impostazione usando PowerShell, vedere [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
   - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](../sql-database/sql-database-firewall-configure.md).
   - Verificare che il server di database SQL di Azure non abbia un catalogo SSIS (database SSISDB). Il provisioning del runtime di integrazione SSIS di Azure non supporta l'uso di un catalogo SSIS esistente.
 
## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Accedere al [portale di Azure](https://portal.azure.com/).    
2. Scegliere **Nuovo** dal menu a sinistra, selezionare **Dati e analisi** e quindi selezionare **Data Factory**. 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Nella pagina **Nuova data factory** immettere **MyAzureSsisDataFactory** in **Nome**. 
      
   ![Pagina "Nuova data factory"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio **&lt;nomeutente&gt;MyAzureSsisDataFactory**, e riprovare. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
5. In **Gruppo di risorse** eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
   Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
6. Per **Versione** selezionare **V2 (anteprima)**.
7. Per **Località** selezionare la località per la data factory. L'elenco mostra solo località supportate per la creazione di data factory.
8. Selezionare **Aggiungi al dashboard**.     
9. Selezionare **Create**.
10. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory): 

   ![Riquadro "Deploying data factory" (Distribuzione della data factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Al termine della creazione verrà visualizzata la pagina **Data factory**.
   
   ![Home page per la data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Selezionare **Monitoraggio e gestione** per aprire un'interfaccia utente di Data Factory in una scheda separata. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

1. Nella pagina **Attività iniziali** selezionare il riquadro **Configure SSIS Integration Runtime** (Configura SSIS Integration Runtime). 

   ![Riquadro "Configure SSIS Integration Runtime" (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Nella pagina **Impostazioni generali** di **Integration Runtime Setup** (Installazione di Integration Runtime) completare questa procedura: 

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Per **Nome** specificare un nome per il runtime di integrazione.

   b. Per **Località** selezionare la località per il runtime di integrazione. Vengono visualizzate solo le località supportate.

   c. Per **Dimensioni del nodo** selezionare le dimensioni del nodo da configurare con il runtime SSIS.

   d. Per **Numero nodo** specificare il numero di nodi nel cluster.
   
   e. Selezionare **Avanti**. 
3. Nella pagina **Impostazioni SQL** completare la procedura seguente: 

   ![Impostazioni SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Per **Sottoscrizione** specificare la sottoscrizione di Azure contenente il server di database di Azure.

   b. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo) selezionare il server di database di Azure.

   c. Per **Nome utente amministratore** immettere il nome utente dell'amministratore.

   d. Per **Password amministratore** immettere la password per l'amministratore.

   e. Per **Catalog Database Server Tier** (Livello del server di database del catalogo) selezionare il livello di servizio per il database SSISDB. Il valore predefinito è **Basic**.

   f. Selezionare **Avanti**. 
4. Nella pagina **Impostazioni avanzate** selezionare un valore per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo).   

   ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Questo passaggio è *facoltativo*. Se è presente una rete virtuale, creata tramite il modello di distribuzione classica oppure tramite Azure Resource Manager, a cui si vuole aggiungere il runtime di integrazione, selezionare la casella di controllo **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Selezionare una rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure e consentire ai servizi di Azure di configurare le autorizzazioni/impostazioni della rete virtuale). Completare quindi i passaggi seguenti: 

   ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. Per **Sottoscrizione** specificare la sottoscrizione contenente la rete virtuale.

   b. Per **Nome della rete virtuale** selezionare il nome della rete virtuale.

   c. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. 
6. Selezionare **Fine** per avviare la creazione del runtime di integrazione SSIS di Azure. 

   > [!IMPORTANT]
   > Il completamento di questo processo richiede circa 20 minuti.
   >
   > Il servizio Data Factory si connette al database SQL di Azure per preparare il database del catalogo SSIS (database SSISDB). Lo script configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificato. Aggiunge quindi la nuova istanza del runtime di integrazione SSIS di Azure alla rete virtuale.
   > 
   > Quando si effettua il provisioning di un'istanza del runtime di integrazione SSIS di Azure, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati supportate dai componenti predefiniti. Non è possibile installare componenti di terze parti per SSIS in questo momento. Questa restrizione è applicabile a componenti di terze parti da Microsoft, ad esempio i componenti Oracle e Teradata di Attunity e i componenti SAP BI.

7. Nella scheda **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Selezionare **Aggiorna** per aggiornare lo stato. 

   ![Stato di creazione con pulsante "Aggiorna"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Usare i collegamenti nella colonna **Azioni** per arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato. 

   ![Collegamenti nella colonna "Azioni"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure

1. Nell'interfaccia utente di Azure Data Factory, passare alla scheda **Modifica**, selezionare **Connessioni** e quindi selezionare la scheda **Integration Runtimes** (Runtime di integrazione) per visualizzare i runtime di integrazione esistenti nella data factory. 
   ![Selezioni per la visualizzazione di runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Selezionare **Nuovo** per creare un runtime di integrazione SSIS di Azure. 

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. Nella finestra **Integration Runtime Setup** (Installazione di Integration Runtime) selezionare **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi selezionare **Avanti**.

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS
Usare ora SQL Server Data Tools o SQL Server Management Studio per distribuire i pacchetti SSIS in Azure. Connettersi al server di database di Azure che ospita il catalogo SSIS (database SSISDB). Il nome del server di database di Azure ha il formato `<servername>.database.windows.net` (per il database SQL di Azure). 

Vedere gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire, eseguire e monitorare un pacchetto SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pianificare l'esecuzione di pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare e avviare un runtime di integrazione SSIS di Azure.

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
> [Copiare i dati nel cloud](tutorial-copy-data-portal.md)
