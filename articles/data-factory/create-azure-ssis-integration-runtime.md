---
title: Creare un runtime di integrazione SSIS di Azure in Azure Data Factory
description: Informazioni su come creare un runtime di integrazione Azure-SSIS in Azure Data Factory in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336237"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione SSIS di Azure in Azure Data Factory

Questo articolo illustra i passaggi per il provisioning di un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) in Azure Data Factory.This article provides steps for provisioning an Azure-SQL Server Integration Services (SSIS) integration runtime (IR) in Azure Data Factory. Azure-SSIS IR supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto).
- Esecuzione di pacchetti distribuiti in file system, condivisioni file o File di Azure (modello di distribuzione del progetto). 

Dopo aver effettuato il provisioning di un'istanza di Azure-SSIS IR, è possibile usare i consueti strumenti per distribuire ed eseguire i pacchetti in Azure. Questi strumenti includono SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) e strumenti da riga di comando come `dtinstall`, `dtutil` e `dtexec`.

L'esercitazione del sistema di distribuzione Azure-SSIS di provisioning illustra come creare un componente di accesso di Azure-SSIS tramite il portale di Azure o l'app Data Factory.The [Provisioning Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) tutorial shows how to create an Azure-SSIS IR via the Azure portal or the Data Factory app. L'esercitazione illustra anche come usare facoltativamente un server di database SQL di Azure o un'istanza gestita per ospitare SSISDB.The tutorial also shows how to optionally use an Azure SQL Database server or managed instance to host SSISDB. Questo articolo si espande sull'esercitazione e descrive come eseguire queste attività facoltative:This article expands on the tutorial and describes how to do these optional tasks:

- Usare un server di database SQL di Azure con regole del firewall IP o endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB.Use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB. Come prerequisito, è necessario configurare le autorizzazioni e le impostazioni della rete virtuale affinché il componente di accesso Azure-SSIS si unisca a una rete virtuale.

- Usare l'autenticazione di Azure Active Directory (Azure AD) con l'identità gestita per la data factory per connettersi a un server di database SQL di Azure o a un'istanza gestita. Come prerequisito, è necessario aggiungere l'identità gestita per la data factory come utente del database in grado di creare un'istanza SSISDB.

- Aggiungere il componente di riutilizzo Azure-SSIS a una rete virtuale o configurare un componente di integrazione self-hosted come proxy per il componente di accesso Azure-SSIS per accedere ai dati in locale.

Questo articolo illustra come eseguire il provisioning di un ir di Azure-SSIS usando il portale di Azure, Azure PowerShell e un modello di Azure Resource Manager.This article shows how to provision an Azure-SSIS IR by using the Azure portal, Azure PowerShell, and an Azure Resource Manager template.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si dispone già di un abbonamento, è possibile creare un account di [prova gratuito.](https://azure.microsoft.com/pricing/free-trial/)

- **Server di database SQL di Azure o istanza gestita (facoltativo).** Se non si ha ancora un server di database, crearne uno nel portale di Azure prima di iniziare. Data Factory creerà a sua volta un'istanza di SSISDB in questo server di database. 

  È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure.

  Tenere presenti questi punti:

  - In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per indicazioni sulla scelta del tipo di server di database per ospitare SSISDB, vedere la sezione [Confrontare un singolo database, pool elastico e istanza gestita](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) di Azure in questo articolo. 
  
    Se per ospitare SSISDB si usa il server di database SQL di Azure con regole del firewall per gli indirizzi IP/endpoint servizio di rete virtuale o un'istanza gestita con un endpoint privato oppure se è richiesto l'accesso ai dati in locale senza configurare il runtime di integrazione self-hosted, è necessario aggiungere Azure-SSIS IR a una rete virtuale. Per altre informazioni, vedere [Aggiungere un componente di riprodotto Azure-SSIS a una rete virtuale.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

  - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Questa impostazione non è applicabile se per ospitare SSISDB si usa un server di database SQL con regole del firewall per gli indirizzi IP/endpoint del servizio di rete virtuale o un'istanza gestita con un endpoint privato. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Per abilitare questa impostazione con PowerShell, vedere [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di database e di server di database SQL di Azure](../sql-database/sql-database-firewall-configure.md).

  - È possibile connettersi al server di database usando l'autenticazione SQL con le credenziali di amministratore del server o l'autenticazione di Azure AD con l'identità gestita per la data factory. Nel secondo caso è necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per altre informazioni, vedere Abilitare l'autenticazione di Azure AD per un provider di servizi di accesso Azure-SSIS.For more information, see [Enable Azure AD authentication for an Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Verificare che nel server di database non sia già presente un'istanza di SSISDB. Il provisioning di Azure-SSIS IR non supporta l'uso di un'istanza di SSISDB esistente.

- **Rete virtuale di Azure Resource Manager (facoltativa)**. È necessario avere una rete virtuale di Azure Resource Manager in presenza di almeno una delle condizioni seguenti:
  - Si ospita SSISDB in un server di database SQL di Azure con regole del firewall IP o endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato.
  - Si vuole connettersi agli archivi dati locali dai pacchetti SSIS in esecuzione nel componente di integrazione apple-SSIS senza configurare un componente di integrazione self-hosted.

- **Azure PowerShell (facoltativo).** Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps) se si vuole eseguire uno script di PowerShell per effettuare il provisioning di Azure-SSIS IR.

### <a name="regional-support"></a>Supporto locale

Per un elenco delle aree di Azure in cui Data Factory e un ir Azure-SSIS sono disponibili, vedere Disponibilità del sistema di distribuzione [dati e IR SSIS per area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Confronto tra un singolo database del database SQL, un pool elastico e un'istanza gestita

La tabella seguente confronta alcune funzionalità di un server di database SQL di Azure e di un'istanza gestita in relazione al sistema di gestione delle informazioni Azure-SSIR:The following table compares certain features of an Azure SQL Database server and managed instance as they relate to Azure-SSIR IR:

| Funzionalità | Singolo database/pool elastico| Istanza gestita |
|---------|--------------|------------------|
| **Pianificazione** | Agente SQL Server non è disponibile.<br/><br/>Vedere [Pianificare l'esecuzione](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)di un pacchetto in una pipeline di Data Factory.| L'agente istanza gestita è disponibile. |
| **Autenticazione** | È possibile creare un'istanza SSISDB con un utente del database indipendente che rappresenta qualsiasi gruppo di Azure AD con l'identità gestita della data factory come membro nel ruolo **di db_owner.**<br/><br/>Vedere [Abilitare l'autenticazione di Azure AD per creare un'istanza SSISDB in un server di database SQL di Azure.See Enable Azure AD authentication to create an SSISDB instance on an Azure SQL Database server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | È possibile creare un'istanza SSISDB con un utente del database indipendente che rappresenta l'identità gestita della data factory. <br/><br/>Vedere [Abilitare l'autenticazione di Azure AD per creare un'istanza SSISDB in un'istanza gestita del database SQL di Azure.See Enable Azure AD authentication to create an SSISDB instance in an Azure SQL Database managed instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Livello di servizio** | Quando si crea un'istanza di Azure-SSIS con il server di database SQL di Azure, è possibile selezionare il livello di servizio per SSISDB. Sono disponibili più livelli di servizio. | Quando si crea un'istanza di azure-SSIS con l'istanza gestita, non è possibile selezionare il livello di servizio per SSISDB. Tutti i database nell'istanza gestita condividono la stessa risorsa allocata a tale istanza. |
| **Rete virtuale** | Il provider di disponibilità di accesso Azure-SSIS può partecipare a una rete virtuale di Azure Resource Manager se si usa un server di database SQL di Azure con regole del firewall IP o endpoint del servizio di rete virtuale. | Il provider di oggetti chiave Azure-SSIS può partecipare a una rete virtuale di Azure Resource Manager se si usa un'istanza gestita con endpoint privato. La rete virtuale è necessaria quando non si abilita un endpoint pubblico per l'istanza gestita.<br/><br/>Se si aggiunge il raggio di ir Azure-SSIS alla stessa rete virtuale dell'istanza gestita, assicurarsi che il componente di accesso Azure-SSIS si trovi in una subnet diversa dall'istanza gestita. Se si aggiunge il raggio di aggiunto Azure-SSIS a una rete virtuale diversa dall'istanza gestita, è consigliabile un peering di rete virtuale o una connessione da rete a rete. Vedere [Connettere l'applicazione a un'istanza gestita del database SQL](../sql-database/sql-database-managed-instance-connect-app.md)di Azure. |
| **Transazioni distribuite** | Questa funzionalità è supportata tramite transazioni elastiche. Le transazioni di Microsoft Distributed Transaction Coordinator (MSDTC) non sono supportate. Se i pacchetti SSIS usano MSDTC per coordinare le transazioni distribuite, è consigliabile eseguire la migrazione a transazioni elastiche per il database SQL di Azure.If your SSIS packages use MSDTC to coordinate distributed transactions, consider migrating to elastic sql transactions for Azure SQL Database. Per ulteriori informazioni, vedere [Transazioni distribuite tra database cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Non supportato. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Usare il portale di Azure per creare un runtime di integrazioneUse the Azure portal to create an integration runtime

In this section, you use the Azure portal, specifically the Data Factory user interface (UI) or app, to create an Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Creare una data factory

Per creare la data factory tramite il portale di Azure, seguire la procedura descritta in [Creare una data factory tramite l'interfaccia utente](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Durante questa procedura, selezionare **Aggiungi al dashboard** per un accesso rapido dopo la creazione. 

Una volta creata la data factory, aprire la relativa pagina di panoramica nel portale di Azure. Selezionare il riquadro **Autore & Monitor** per aprire la pagina **Iniziamo** in una scheda separata. È possibile continuare a creare il componente di ricreazione Azure-SSIS.There, you can continue to create your Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

1. Nella pagina **Attività iniziali** selezionare il riquadro **Configure SSIS Integration Runtime** (Configura SSIS Integration Runtime).

   ![Riquadro Configure SSIS Integration Runtime (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Nella sezione **Impostazioni generali** del pannello **Integration Runtime Setup** (Configurazione di Integration Runtime) completare questa procedura.

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Per **Nome**, immettere il nome del runtime di integrazione.

   1. Per **Descrizione**, immettere la descrizione del runtime di integrazione.

   1. Per **Località** selezionare la località del runtime di integrazione. Vengono visualizzate solo le località supportate. È consigliabile selezionare la stessa località del server di database che ospiterà SSISDB.

   1. In **Dimensioni nodo**selezionare la dimensione del nodo nel cluster di runtime di integrazione. Vengono visualizzate solo le dimensioni dei nodi supportate. Selezionare una dimensione del nodo elevata (scalabilità verticale) se si prevede di eseguire numerosi pacchetti con un uso intensivo di calcoli o di memoria.

   1. Per **Numero nodo**, selezionare il numero di nodi nel cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei nodi supportati. Selezionare un cluster di grandi dimensioni con molti nodi (scalabilità orizzontale) se si prevede di eseguire numerosi pacchetti in parallelo.

   1. Per **Edition/License**, selezionare l'edizione di SQL Server per il runtime di integrazione: Standard o Enterprise. Selezionare Enterprise se si prevede di usare funzionalità avanzate nel runtime di integrazione.

   1. Per **Risparmia denaro**, selezionare l'opzione Vantaggi ibridi di Azure per il runtime di integrazione: **Sì** o **No**. Selezionare **Sì** se si desidera portare la propria licenza di SQL Server con Software Assurance per trarre vantaggio dal risparmio sui costi con l'uso ibrido.

   1. Fare clic su **Avanti**.

1. Nella sezione **Impostazioni SQL** completare la procedura seguente.

   ![Impostazioni SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Selezionare la casella di controllo **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs** (Creare il catalogo SSIS (SSISDB) ospitato dall'istanza gestita/server di database SQL di Azure per archiviare progetti/pacchetti/ambienti/log di esecuzione) per scegliere il modello di distribuzione dei pacchetti da eseguire in Azure-SSIS IR. Scegliere il modello di distribuzione del progetto in base a cui i pacchetti vengono distribuiti nell'istanza di SSISDB ospitata dal server di database oppure il modello di distribuzione del pacchetto in base a cui i pacchetti vengono distribuiti in file system, condivisioni file o File di Azure. 
    
      Se si seleziona la casella di controllo, è necessario portare il proprio server di database per ospitare l'istanza SSISDB che verrà creata e gestita per conto dell'utente.
   
      1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure contenente il server di database che ospiterà SSISDB. 

      1. Per **Località**, selezionare la stessa località del server di database che ospiterà SSISDB. È consigliabile selezionare la stessa località del runtime di integrazione. 

      1. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. 
    
         In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per indicazioni sulla scelta del tipo di server di database per ospitare SSISDB, vedere la sezione [Confrontare un singolo database, pool elastico e istanza gestita](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) di Azure in questo articolo. 
    
         Se per ospitare SSISDB si seleziona il server di database SQL di Azure con regole del firewall per gli indirizzi IP/endpoint servizio di rete virtuale o un'istanza gestita con un endpoint privato oppure se è richiesto l'accesso ai dati in locale senza configurare il runtime di integrazione self-hosted, è necessario aggiungere Azure-SSIS IR a una rete virtuale. Per altre informazioni, vedere [Aggiungere un componente di riprodotto Azure-SSIS a una rete virtuale.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 

      1. Selezionare la casella di controllo **Use AAD authentication with the managed identity for your ADF** (Usa l'autenticazione di AAD con l'identità gestita per Azure Data Factory) per scegliere il metodo di autenticazione per il server di database in cui ospitare SSISDB. Scegliere l'autenticazione SQL o l'autenticazione di AAD con l'identità gestita per la data factory. 
    
         Se si seleziona la casella di controllo, sarà necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per altre informazioni, vedere Abilitare l'autenticazione di Azure AD per un provider di servizi di accesso Azure-SSIS.For more information, see [Enable Azure AD authentication for an Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. Per **Nome utente amministratore**, immettere il nome utente di autenticazione SQL per il server di database in cui ospitare SSISDB. 

      1. Per **Password amministratore**, immettere la password di autenticazione SQL per il server di database in cui ospitare SSISDB. 

      1. Per **Catalog Database Service Tier** (Livello di servizio del database di catalogo) selezionare il livello di servizio per il server di database in cui ospitare SSISDB. Selezionare il livello Basic, Standard o Premium oppure il nome di un pool elastico. 

      1. Selezionare **Test connessione**. Se il test ha esito positivo, selezionare **Avanti**. 

1. Nella sezione **Impostazioni avanzate** completare la procedura seguente.

   ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vogliono usare più core per eseguire un singolo pacchetto di grandi dimensioni a uso intensivo di calcoli o memoria. Selezionare un numero alto se si vogliono eseguire uno o più pacchetti di piccole dimensioni in un singolo core.

   1. Selezionare la casella di controllo **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Personalizzare Azure-SSIS Integration Runtime con configurazioni di sistema/installazioni di componenti aggiuntive) per aggiungere le impostazioni personalizzate standard o Express in Azure-SSIS IR. Per altre informazioni, vedere [Configurazione personalizzata di Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con configurazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Per URI di accesso condiviso **del contenitore di installazione personalizzato**, immettere l'URI di accesso condiviso del contenitore in cui archiviare gli script e i file associati per le configurazioni personalizzate standard.

      1. Per **l'installazione personalizzata rapida,** selezionare **Nuovo** per aprire il pannello Di installazione **rapida personalizzata,** quindi selezionare qualsiasi tipo nel menu a discesa Del tipo di installazione **personalizzato Express,** ad esempio **Esegui comando cmdkey**, **Aggiungi variabile**di ambiente , Installa componente **con licenza,** ecc.

         Se si seleziona il tipo di **componente Installa licenza,** è possibile selezionare tutti i componenti integrati dai partner ISV nel menu a discesa **Nome componente** e, se necessario, immettere il codice di licenza del prodotto acquistato da essi nel campo Chiave **di licenza.**
  
         Le impostazioni personalizzate express aggiunte verranno visualizzate nella sezione **Impostazioni avanzate.** Per rimuoverli, è possibile selezionare le relative caselle di controllo e quindi selezionare **Elimina**.

   1. Selezionare la casella di controllo Selezionare una rete virtuale per il runtime di **integrazione Azure-SSIS, consentire ad ADF di creare determinate risorse di rete e, facoltativamente, portare i propri indirizzi IP pubblici statici** per scegliere se aggiungere il runtime di integrazione a una rete virtuale. 

      Selezionarlo se si usa un server di database SQL di Azure con regole del firewall IP o endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB oppure se è necessario accedere ai dati locali (ovvero, si dispone di origini dati o destinazioni locali nei pacchetti SSIS) senza configurare un provider di servizi token di sicurezza self-hosted. Per altre informazioni, vedere Aggiungere il [componente di riprodotto Azure-SSIS a una rete virtuale.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale.

      1. Per **Località** viene selezionata la stessa località del runtime di integrazione.

      1. Per Tipo selezionare il tipo di rete virtuale: classica o Azure Resource Manager.For **Type**, select the type of your virtual network: classic or Azure Resource Manager. È consigliabile selezionare una rete virtuale di Azure Resource Manager, perché le reti virtuali classiche saranno presto deprecate.

      1. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Deve essere lo stesso usato per il server di database SQL di Azure con endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB. Oppure dovrebbe essere lo stesso connesso alla rete locale. In caso contrario, può essere qualsiasi rete virtuale per portare i propri indirizzi IP pubblici statici per il codice IR Azure-SSIS.

      1. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve essere lo stesso usato per il server di database SQL di Azure con endpoint del servizio di rete virtuale per ospitare SSISDB. In alternativa, deve essere una subnet diversa da quella usata per l'istanza gestita con endpoint privato per ospitare SSISDB. In caso contrario, può essere qualsiasi subnet per portare i propri indirizzi IP pubblici statici per il codice IR Azure-SSIS.

      1. Selezionare la casella di controllo Porta indirizzi IP pubblici statici per il runtime di **integrazione Azure-SSIS** per scegliere se si desidera portare i propri indirizzi IP pubblici statici per il runtime di integrazione Azure-SSIS, in modo da poterli consentire nel firewall per le origini dati.

         Se si seleziona la casella di controllo, completare i passaggi seguenti.

         1. Per **Primo indirizzo IP pubblico statico**, selezionare il primo indirizzo IP pubblico statico che soddisfa i requisiti per il componente di accesso Azure-SSIS. Se non ne hai, fai clic su **Crea nuovo** link per creare indirizzi IP pubblici statici nel portale di Azure, quindi fai clic sul pulsante Aggiorna qui, in modo da poterli selezionare.
      
         1. Per **Secondo indirizzo IP pubblico statico**, selezionare il secondo indirizzo IP pubblico statico che soddisfa i requisiti per il componente di accesso Azure-SSIS. Se non ne hai, fai clic su **Crea nuovo** link per creare indirizzi IP pubblici statici nel portale di Azure, quindi fai clic sul pulsante Aggiorna qui, in modo da poterli selezionare.

   1. Selezionare la casella di controllo **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Configurare un runtime di integrazione self-hosted come proxy per Azure-SSIS Integration Runtime) per scegliere se configurare un runtime di integrazione self-hosted come proxy per Azure-SSIS IR. Per altre informazioni, vedere [Configurare un runtime di integrazione self-hosted come proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con un iR auto-ospitato](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Per **Self-Hosted Integration Runtime**, selezionare il runtime di integrazione self-hosted esistente come proxy per il runtime di integrazione Azure-SSIS.

      1. Per **Servizio collegato Archiviazione gestione temporanea**selezionare il servizio collegato Di archiviazione BLOB di Azure esistente o crearne uno nuovo per la gestione temporanea.

      1. Per **Percorso di gestione temporanea**specificare un contenitore BLOB nell'account di archiviazione BLOB di Azure selezionato o lasciarlo vuoto per usare uno predefinito per la gestione temporanea.

   1. Selezionare **VNet Validation** > **Continue**. 

1. Nella sezione **Riepilogo** esaminare tutte le impostazioni di provisioning, aggiungere un segnalibro per i collegamenti consigliati alla documentazione e fare clic su **Fine** per avviare la creazione del runtime di integrazione.

   > [!NOTE]
   > Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, la partecipazione del componente di accesso Azure-SSIS a una rete virtuale potrebbe richiedere 20-30 minuti.
   >
   > Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura inoltre le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il componente di ricambio Azure-SSIS alla rete virtuale.
   > 
   > Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per informazioni su altri componenti che è possibile installare, vedere [Configurazione personalizzata di Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Nella scheda **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Selezionare **Aggiorna** per aggiornare lo stato.

   ![Stato creazione](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Usare i collegamenti nella colonna **Azioni** per arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato.

   ![Azioni iR SSIS di AzureAzure SSIS IR actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime di integrazione SSIS di Azure nel portale

1. Nell'interfaccia utente di Azure Data Factory passare alla scheda **Modifica** e selezionare **Connessioni**. Quindi passare alla scheda **Runtime di integrazione** per visualizzare i runtime di integrazione attualmente presenti nella data factory.

   ![Visualizzare i runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selezionare Nuovo per creare un nuovo ir Azure-SSIS.Select **New** to create a new Azure-SSIS IR.

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Nel pannello **Integration Runtime Setup** (Installazione di Integration Runtime) selezionare il riquadro **Lift-and-shift existing SSIS packages to execute in Azure** (Trasferisci in modalità lift-and-shift i pacchetti SSIS esistenti per l'esecuzione in Azure) e quindi fare clic su **Avanti**.

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Per i passaggi rimanenti per configurare un runtime di integrazione Azure-SSIS, vedere la sezione Eseguire il provisioning di un runtime di [integrazione SSIS](#provision-an-azure-ssis-integration-runtime) di Azure.For the remaining steps to set up an Azure-SSIS IR, see the Provision an Azure SSIS integration runtime section.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Usare Azure PowerShell per creare un runtime di integrazioneUse Azure PowerShell to create an integration runtime

In this section, you use Azure PowerShell to create an Azure-SSIS IR.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

### <a name="sign-in-and-select-a-subscription"></a>Accedere e selezionare un abbonamento

Aggiungere lo script seguente per accedere e selezionare la sottoscrizione di Azure.Add the following script to sign in and select your Azure subscription.

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

Aggiungere lo script seguente per configurare automaticamente le autorizzazioni e le impostazioni della rete virtuale per il runtime di integrazione Azure-SSIS per partecipare.

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

Se non si utilizza SSISDB, è possibile `CatalogServerEndpoint` `CatalogPricingTier`omettere `CatalogAdminCredential` i parametri , e .

Se non si usa un server di database SQL di Azure con regole del firewall IP o un'istanza gestita con endpoint privato per ospitare `VNetId` SSISDB o si richiede l'accesso ai dati locali, è possibile omettere i parametri e o `Subnet` passare valori vuoti per essi. È anche possibile ometterli se si configura un componente di integrazione self-hosted come proxy per il componente di integrazione Azure-SSIS per accedere ai dati in locale. In caso contrario, non è possibile ometterli e devono passare valori validi dalla configurazione della rete virtuale. Per altre informazioni, vedere [Aggiungere un componente di riprodotto Azure-SSIS a una rete virtuale.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Se si utilizza l'istanza gestita per ospitare SSISDB, è possibile omettere il `CatalogPricingTier` parametro o passare un valore vuoto per esso. In caso contrario, non è possibile ometterlo e deve passare un valore valido dall'elenco dei piani tariffari supportati per il database SQL di Azure.Otherwise, you can't omet it and must pass a valid value from the list of supported pricing tiers for Azure SQL Database. Per ulteriori informazioni, vedere [Limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

Se si usa l'autenticazione di Azure AD con l'identità gestita `CatalogAdminCredential` per la data factory per connettersi al server di database, è possibile omettere il parametro. È tuttavia necessario aggiungere l'identità gestita per la data factory in un gruppo di Azure AD con autorizzazioni di accesso al server di database. Per altre informazioni, vedere Abilitare l'autenticazione di Azure AD per un provider di servizi di accesso Azure-SSIS.For more information, see [Enable Azure AD authentication for an Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). In caso contrario, non è possibile ometterlo e deve passare un oggetto valido formato dal nome utente e dalla password di amministratore del server per l'autenticazione SQL.

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

### <a name="start-the-integration-runtime"></a>Avviare il runtime di integrazioneStart the integration runtime

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
> Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, la partecipazione del componente di accesso Azure-SSIS a una rete virtuale potrebbe richiedere 20-30 minuti.
>
> Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura inoltre le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il componente di ricambio Azure-SSIS alla rete virtuale.
> 
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per informazioni su altri componenti che è possibile installare, vedere [Configurazione personalizzata di Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Ecco lo script completo che crea un runtime di integrazione Azure-SSIS.Here's the full script that creates an Azure-SSIS integration runtime.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Usare un modello di Azure Resource Manager per creare un runtime di integrazioneUse an Azure Resource Manager template to create an integration runtime

In questa sezione si usa un modello di Azure Resource Manager per creare il runtime di integrazione Azure-SSIS.In this section, you use an Azure Resource Manager template to create the Azure-SSIS integration runtime. Ecco una procedura dettagliata di esempio:Here's a sample walkthrough:

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

2. Per distribuire il modello di `New-AzResourceGroupDeployment` Azure Resource Manager, eseguire il comando come illustrato nell'esempio seguente. Nell'esempio `ADFTutorialResourceGroup` è il nome del gruppo di risorse. `ADFTutorialARM.json`è il file che contiene la definizione JSON per la data factory e il codice IR Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Questo comando crea la data factory e il codice AiR Azure-SSIS in essa, ma non avvia il codice a raggi- .

3. Per avviare il runtime di archiviazione `Start-AzDataFactoryV2IntegrationRuntime` Azure-SSIS, eseguire il comando:To start your Azure-SSIS IR, run the command:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, la partecipazione del componente di accesso Azure-SSIS a una rete virtuale potrebbe richiedere 20-30 minuti.
>
> Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura inoltre le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il componente di ricambio Azure-SSIS alla rete virtuale.
> 
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per informazioni su altri componenti che è possibile installare, vedere [Configurazione personalizzata di Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS

Se si usa SSISDB, è possibile distribuirvi i pacchetti ed eseguirli nel runtime di integrazione Azure-SSIS usando gli strumenti di SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS). Questi strumenti si connettono al server di database tramite l'endpoint del server: 

- Per un server di database SQL di Azure, il formato dell'endpoint è `<server name>.database.windows.net`.
- Per un'istanza gestita con un endpoint privato, il formato dell'endpoint è `<server name>.<dns prefix>.database.windows.net`.
- Per un'istanza gestita con un endpoint pubblico, il formato dell'endpoint è `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se non si usa SSISDB, è possibile distribuire i pacchetti in file system, condivisioni file o file di `dtinstall` `dtutil`Azure `dtexec` ed eseguirli nel runtime di glisISAzure-SSIS usando gli strumenti da riga di comando , e . Per altre informazioni, vedere [Distribuire pacchetti SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

In entrambi i casi, è anche possibile eseguire i pacchetti distribuiti in Azure-SSIS IR tramite l'attività di esecuzione di pacchetti SSIS nelle pipeline di Data Factory. Per altre informazioni, vedere [Richiamare l'esecuzione di pacchetti SSIS come attività di Data Factory di prima classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Passaggi successivi

Vedere altri argomenti del prodotto rinisso Azure-SSIS in questa documentazione:See other Azure-SSIS IR topics in this documentation:

- Runtime di [integrazione Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo fornisce informazioni sui runtime di integrazione in generale, tra cui il runtime di integrazione Azure-SSIS.This article provides information about integration runtimes in general, including Azure-SSIS IR.
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come recuperare e comprendere le informazioni sul componente di accesso Azure-SSIS.
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare il raggio di ir Azure-SSIS. Viene inoltre illustrato come scalare orizzontalmente il componente di richiamata Azure-SSIS aggiungendo altri nodi.
- [Distribuire ed eseguire un pacchetto di SQL Server Integration Services (SSIS) in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Pianificare l'esecuzione dei pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)