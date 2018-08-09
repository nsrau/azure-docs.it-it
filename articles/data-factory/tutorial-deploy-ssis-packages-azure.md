---
title: Eseguire il provisioning del runtime di integrazione SSIS di Azure | Microsoft Docs
description: Informazioni su come eseguire il provisioning del runtime di integrazione Azure-SSIS in Azure Data Factory in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: abbf64fadfdd6dd194afe0fb498303ab18a9e069
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425344"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Eseguire il provisioning del runtime di integrazione SSIS di Azure in Azure Data Factory
Questa esercitazione illustra la procedura per effettuare il provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory tramite il portale di Azure. È quindi possibile usare SQL Server Data Tools o SQL Server Management Studio per distribuire ed eseguire pacchetti SQL Server Integration Services (SSIS) in questo runtime in Azure. Per informazioni concettuali in proposito, vedere la [panoramica del runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

In questa esercitazione si completa la procedura seguente:

> [!div class="checklist"]
> * Creare una data factory.
> * Effettuare il provisioning di un runtime di integrazione SSIS di Azure.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 
- **Server di database SQL di Azure**. Se non si ha ancora un server di database, crearne uno nel portale di Azure prima di iniziare. Azure Data Factory crea il catalogo SSIS (database SSISDB) in questo server di database. È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione nel database SSISDB senza attraversare aree di Azure. 
- In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come database singolo, parte di un pool elastico o in un'istanza gestita (anteprima) e reso accessibile in una rete pubblica o mediante l'aggiunta a una rete virtuale. Se si usa il database SQL di Azure con endpoint del servizio Rete virtuale/Istanza gestita (anteprima) per ospitare il database SSISDB o occorre accedere a dati in locale, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale. Vedere [Creare un runtime di integrazione SSIS di Azure in una rete virtuale](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
- Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Questo non vale quando si usa il database SQL di Azure con endpoint del servizio Rete virtuale/Istanza gestita (anteprima) per ospitare il database SSISDB. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Per abilitare questa impostazione usando PowerShell, vedere [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
- Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](../sql-database/sql-database-firewall-configure.md). 
- È possibile connettersi al server di database tramite l'autenticazione di SQL Server con le credenziali di amministratore del server o tramite l'autenticazione di Azure Active Directory (AAD) con l'identità del servizio gestita di Azure Data Factory (ADF).  Nel secondo caso, è necessario aggiungere l'identità del servizio gestita di ADF in un gruppo AAD con autorizzazioni di accesso al server di database. Vedere [Creare un runtime di integrazione SSIS di Azure con l'autenticazione AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
- Verificare che il server di database SQL di Azure non abbia un catalogo SSIS (database SSISDB). Il provisioning del runtime di integrazione SSIS di Azure non supporta l'uso di un catalogo SSIS esistente. 

> [!NOTE]
> - Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [ Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). 
> - Per un elenco di aree di Azure in cui il runtime di integrazione SSIS di Azure è attualmente disponibile, seleziona le aree di interesse nella pagina seguente, quindi espandi **Analitics** per individuare **Runtime di integrazione SSIS**: [ Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). 

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome. 
1. Accedere al [portale di Azure](https://portal.azure.com/). 
1. Scegliere **Nuovo** dal menu a sinistra, selezionare **Dati e analisi** e quindi selezionare **Data Factory**. 

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **MyAzureSsisDataFactory** in **Nome**. 

   ![Pagina "Nuova data factory"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio **&lt;nomeutente&gt;MyAzureSsisDataFactory**, e riprovare. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
1. In **Gruppo di risorse** eseguire una di queste operazioni: 

   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 

   Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md). 
1. Per **Versione** selezionare **V2 (anteprima)**. 
1. Per **Località** selezionare la località per la data factory. L'elenco mostra solo località supportate per la creazione di data factory. 
1. Selezionare **Aggiungi al dashboard**. 
1. Selezionare **Create**. 
1. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory): 

   ![Riquadro "Deploying data factory" (Distribuzione della data factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Al termine della creazione verrà visualizzata la pagina **Data factory**. 

   ![Home page per la data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Selezionare **Crea e monitora** per aprire l'interfaccia utente di Data Factory in una scheda separata. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

1. Nella pagina **Attività iniziali** selezionare il riquadro **Configure SSIS Integration Runtime** (Configura SSIS Integration Runtime). 

   ![Riquadro "Configure SSIS Integration Runtime" (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Nella pagina **Impostazioni generali** di **Integration Runtime Setup** (Installazione di Integration Runtime) completare questa procedura: 

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Per **Nome**, immettere il nome del runtime di integrazione. 

   b. Per **Descrizione**, immettere la descrizione del runtime di integrazione. 

   c. Per **Località** selezionare la località del runtime di integrazione. Vengono visualizzate solo le località supportate. È consigliabile selezionare la stessa località del server di database che ospiterà SSISDB. 

   d. Per **Dimensioni del nodo**, selezionare la dimensione del nodo nel cluster del runtime di integrazione. Vengono visualizzate solo le dimensioni dei nodi supportate. Selezionare una dimensione del nodo elevata (scalabilità verticale), se si prevede di eseguire numerosi pacchetti con un uso intensivo della capacità di calcolo o della memoria. 

   e. Per **Numero nodo**, selezionare il numero di nodi nel cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei nodi supportati. Selezionare un cluster di grandi dimensioni con molti nodi (scalabilità orizzontale), se si prevede di eseguire numerosi pacchetti in parallelo. 

   f. Per **Edition/License** (Edizione/licenza), selezionare l'edizione/licenza di SQL Server per il runtime di integrazione: Standard o Enterprise. Selezionare Enterprise se si prevede di usare funzionalità avanzate/premium nel runtime di integrazione. 

   g. Per **Risparmio sui costi**, selezionare l'opzione Vantaggio Azure Hybrid per il runtime di integrazione: Sì o No. Selezionare Sì se si vuole usare la propria licenza di SQL Server con Software Assurance per trarre vantaggio dai risparmi sui costi con Hybrid Use. 

   h. Fare clic su **Avanti**. 

1. Nella pagina **Impostazioni SQL** completare la procedura seguente: 

   ![Impostazioni SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Per **Sottoscrizione** selezionare la sottoscrizione di Azure contenente il server di database che ospiterà SSISDB. 

   b. Per **Località**, selezionare la stessa località del server di database che ospiterà SSISDB. È consigliabile selezionare la stessa località del runtime di integrazione. 

   c. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. In base al server di database selezionato, il database SSISDB può essere creato per conto dell'utente come un database autonomo, parte di un pool elastico o in un'istanza gestita (anteprima) e reso accessibile in una rete pubblica o mediante l'aggiunta a una rete virtuale. Per materiale sussidiario sulla scelta del tipo di server di database per ospitare SSISDB, vedere [Confrontare il database SQL e l'istanza gestita (anteprima)](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). Se si seleziona il database SQL di Azure con endpoint del servizio Rete virtuale/Istanza gestita (anteprima) per ospitare il database SSISDB o occorre accedere a dati in locale, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale. Vedere [Creare il runtime di integrazione Azure-SSIS in una rete virtuale](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Per la casella di controllo **Use AAD authentication...** (Usa autenticazione AAD...), selezionare il metodo di autenticazione per il server di database che ospiterà SSISDB: SQL o Azure Active Directory (AAD) con l'identità del servizio gestita di Azure Data Factory (ADF). Se si seleziona la casella di controllo, è necessario aggiungere l'identità del servizio gestita di ADF in un gruppo AAD con autorizzazioni di accesso al server di database. Vedere [Creare un runtime di integrazione SSIS di Azure con l'autenticazione AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Per **Nome utente amministratore**, immettere il nome utente di autenticazione SQL per il server di database che ospiterà SSISDB. 

   f. Per **Password amministratore**, immettere la password di autenticazione SQL per il server di database che ospiterà SSISDB. 

   g. Per **Catalog Database Service Tier** (Livello di servizio database di catalogo), selezionare il livello di servizio per il server di database che ospiterà SSISDB: livello Basic/Standard/Premium o il nome del pool elastico. 

   h. Fare clic su **Test connessione** e, in caso di esito positivo, fare clic su **Avanti**. 

1. Nella pagina **Impostazioni avanzate** completare la procedura seguente: 

   ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vuole usare più di un core per eseguire un singolo pacchetto pesante/di grandi dimensioni con un uso intensivo della capacità di calcolo o della memoria. Selezionare un numero elevato se si vuole eseguire uno o più pacchetti leggeri/di piccole dimensioni un singolo core. 

   b. Per **Custom Setup Container SAS URI** (URI di firma di accesso condiviso del contenitore di installazione personalizzata), immettere facoltativamente l'URI (Uniform Resource Identifier) di firma di accesso condiviso del contenitore BLOB di archiviazione di Azure in cui sono archiviati gli script di installazione e i relativi file associati. Vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Per la casella di controllo**Select a VNet...** (Selezionare una rete virtuale...), selezionare se aggiungere il runtime di integrazione a una rete virtuale. Selezionare questa casella di controllo se si usa il database SQL di Azure con endpoint del servizio Rete virtuale/Istanza gestita (anteprima) per ospitare il database SSISDB o occorre accedere a dati in locale. Vedere [Creare un runtime di integrazione SSIS di Azure in una rete virtuale](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Fare clic su **Fine** per avviare la creazione del runtime di integrazione. 

   > [!IMPORTANT]
   > Il completamento di questo processo richiede da 20 a 30 minuti circa.
   >
   > Il servizio Data Factory si connette al server di database SQL di Azure per preparare il catalogo SSIS (database SSISDB). 
   > 
   > Quando si effettua il provisioning di un'istanza del runtime di integrazione SSIS di Azure, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati supportate dai componenti predefiniti. È anche possibile installare componenti aggiuntivi. Per altre informazioni, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). 

1. Nella scheda **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Selezionare **Aggiorna** per aggiornare lo stato. 

   ![Stato di creazione con pulsante "Aggiorna"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Usare i collegamenti nella colonna **Azioni** per arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato. 

   ![Collegamenti nella colonna "Azioni"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure

1. Nell'interfaccia utente di Azure Data Factory, passare alla scheda **Modifica**, selezionare **Connessioni** e quindi selezionare la scheda **Integration Runtimes** (Runtime di integrazione) per visualizzare i runtime di integrazione esistenti nella data factory. 

   ![Selezioni per la visualizzazione di runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selezionare **Nuovo** per creare un runtime di integrazione SSIS di Azure. 

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Nella finestra **Integration Runtime Setup** (Installazione di Integration Runtime) selezionare **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi selezionare **Avanti**. 

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Per i passaggi rimanenti per configurare un runtime di integrazione SSIS di Azure, vedere la sezione [Effettuare il provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime). 

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS
Usare ora SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire i pacchetti SSIS in Azure. Connettersi al server di database SQL di Azure che ospita il catalogo SSIS (database SSISDB). Il nome del server di database SQL di Azure ha il formato `<servername>.database.windows.net`. 

Vedere gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire, eseguire e monitorare un pacchetto SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Connettersi al catalogo SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pianificare l'esecuzione di pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Effettuare il provisioning di un runtime di integrazione SSIS di Azure.

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
> [Copiare i dati nel cloud](tutorial-copy-data-portal.md)
