---
title: Creare un runtime di integrazione SSIS di Azure in Azure Data Factory
description: Informazioni su come creare un runtime di integrazione Azure-SSIS in Azure Data Factory in modo da rendere possibile distribuire ed eseguire pacchetti SSIS in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 44a8253baa7f15262799f3721069c3dfddcd5384
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496116"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione SSIS di Azure in Azure Data Factory

Questo articolo illustra la procedura per eseguire il provisioning di un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) in Azure Data Factory. Azure-SSIS IR supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto).
- Esecuzione di pacchetti distribuiti in file system, condivisioni file o File di Azure (modello di distribuzione del progetto). 

Dopo aver effettuato il provisioning di un'istanza di Azure-SSIS IR, è possibile usare i consueti strumenti per distribuire ed eseguire i pacchetti in Azure. Questi strumenti includono SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) e strumenti da riga di comando come `dtinstall`, `dtutil`e `dtexec`.

L'esercitazione sul [provisioning Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) illustra come creare una Azure-SSIS IR tramite il portale di Azure o l'app data factory. L'esercitazione illustra anche come usare facoltativamente un server di database SQL di Azure o un'istanza gestita per ospitare SSISDB. Questo articolo espande l'esercitazione e descrive come eseguire queste attività facoltative:

- Usare un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per l'hosting di SSISDB. Come prerequisito, è necessario configurare le autorizzazioni e le impostazioni della rete virtuale per la Azure-SSIS IR per l'aggiunta a una rete virtuale.

- Usare l'autenticazione Azure Active Directory (Azure AD) con l'identità gestita per la data factory per connettersi a un server di database SQL di Azure o a un'istanza gestita. Come prerequisito, è necessario aggiungere l'identità gestita per il data factory come utente del database che può creare un'istanza di SSISDB.

- Aggiungere il Azure-SSIS IR a una rete virtuale o configurare un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR per accedere ai dati in locale.

Questo articolo illustra come effettuare il provisioning di un Azure-SSIS IR usando il portale di Azure, Azure PowerShell e un modello di Azure Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si ha già una sottoscrizione, è possibile creare un account di [valutazione gratuito](https://azure.microsoft.com/pricing/free-trial/) .

- **Server di database SQL di Azure o istanza gestita (facoltativo)** . Se non si ha ancora un server di database, crearne uno nel portale di Azure prima di iniziare. Data Factory creerà a sua volta un'istanza di SSISDB in questo server di database. 

  È consigliabile creare il server di database nella stessa area di Azure del runtime di integrazione. Questa configurazione consente al runtime di integrazione di scrivere i log di esecuzione in SSISDB senza attraversare aree di Azure.

  Tenere presenti questi punti:

  - In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per indicazioni sulla scelta del tipo di server di database per l'hosting di SSISDB, vedere la sezione [confrontare un singolo database SQL di Azure, un pool elastico e un'istanza gestita](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) in questo articolo. 
  
    Se si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB o se è necessario accedere ai dati locali senza configurare un runtime di integrazione self-hosted, è necessario aggiungere il Azure-SSIS IR a una rete virtuale. Per ulteriori informazioni, vedere [aggiungere un Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Verificare che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server di database. Questa impostazione non è applicabile quando si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB. Per altre informazioni, vedere [Proteggere il database SQL di Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Per abilitare questa impostazione con PowerShell, vedere [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Aggiungere l'indirizzo IP del computer client o un intervallo di indirizzi IP che includa l'indirizzo IP del computer client all'elenco di indirizzi IP client nelle impostazioni del firewall per il server di database. Per altre informazioni, vedere [Regole firewall a livello di database e di server di database SQL di Azure](../sql-database/sql-database-firewall-configure.md).

  - È possibile connettersi al server di database usando l'autenticazione SQL con le credenziali di amministratore del server o l'autenticazione di Azure AD con l'identità gestita per la data factory. Nel secondo caso è necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per ulteriori informazioni, vedere [abilitare l'autenticazione Azure ad per un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Verificare che nel server di database non sia già presente un'istanza di SSISDB. Il provisioning di Azure-SSIS IR non supporta l'uso di un'istanza di SSISDB esistente.

- **Rete virtuale di Azure Resource Manager (facoltativa)** . È necessario avere una rete virtuale di Azure Resource Manager in presenza di almeno una delle condizioni seguenti:
  - Si sta ospitando SSISDB in un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato.
  - Si vuole connettersi agli archivi dati locali dai pacchetti SSIS in esecuzione nel Azure-SSIS IR senza configurare un runtime di integrazione self-hosted.

- **Azure PowerShell (facoltativo)** . Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps) se si vuole eseguire uno script di PowerShell per effettuare il provisioning di Azure-SSIS IR.

### <a name="regional-support"></a>Supporto locale

Per un elenco delle aree di Azure in cui sono disponibili Data Factory e un Azure-SSIS IR, vedere [Data Factory e disponibilità del runtime di integrazione SSIS per area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Confronto tra un database singolo del database SQL, un pool elastico e un'istanza gestita

La tabella seguente confronta determinate funzionalità di un server di database SQL di Azure e di un'istanza gestita in relazione al runtime di integrazione SSIR di Azure:

| Funzionalità | Singolo database/pool elastico| Istanza gestita |
|---------|--------------|------------------|
| **Pianificazione** | Il SQL Server Agent non è disponibile.<br/><br/>Vedere [pianificare l'esecuzione di un pacchetto in una pipeline Data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agente Istanza gestita disponibile. |
| **autenticazione** | È possibile creare un'istanza di SSISDB con un utente di database indipendente che rappresenta qualsiasi gruppo di Azure AD con l'identità gestita del data factory come membro del ruolo **db_owner** .<br/><br/>Vedere [abilitare l'autenticazione Azure ad per creare un'istanza di SSISDB in un server di database SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | È possibile creare un'istanza di SSISDB con un utente di database indipendente che rappresenta l'identità gestita del data factory. <br/><br/>Vedere [abilitare l'autenticazione Azure ad per creare un'istanza di SSISDB in un'istanza gestita di database SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Livello di servizio** | Quando si crea un Azure-SSIS IR con il server di database SQL di Azure, è possibile selezionare il livello di servizio per SSISDB. Sono disponibili più livelli di servizio. | Quando si crea un Azure-SSIS IR con l'istanza gestita, non è possibile selezionare il livello di servizio per il database SSISDB. Tutti i database nell'istanza gestita condividono la stessa risorsa allocata a tale istanza. |
| **Rete virtuale** | Il Azure-SSIS IR può essere aggiunto a una rete virtuale Azure Resource Manager se si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale. | Il Azure-SSIS IR può essere aggiunto a una rete virtuale Azure Resource Manager se si usa un'istanza gestita con endpoint privato. La rete virtuale è obbligatoria quando non si Abilita un endpoint pubblico per l'istanza gestita.<br/><br/>Se si aggiunge il Azure-SSIS IR alla stessa rete virtuale dell'istanza gestita, assicurarsi che l'Azure-SSIS IR si trovi in una subnet diversa dall'istanza gestita. Se si aggiunge il Azure-SSIS IR a una rete virtuale diversa dall'istanza gestita, è consigliabile usare un peering di rete virtuale o una connessione da rete a rete. Vedere [connettere l'applicazione a un'istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transazioni distribuite** | Questa funzionalità è supportata tramite le transazioni elastiche. Le transazioni di Microsoft Distributed Transaction Coordinator (MSDTC) non sono supportate. Se i pacchetti SSIS usano MSDTC per coordinare le transazioni distribuite, è consigliabile eseguire la migrazione a transazioni elastiche per il database SQL di Azure. Per altre informazioni, vedere [transazioni distribuite in database cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Non supportato. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Usare il portale di Azure per creare un runtime di integrazione

In questa sezione si usa il portale di Azure, in particolare l'interfaccia utente Data Factory o l'app, per creare un Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Creare una data factory

Per creare la data factory tramite il portale di Azure, seguire la procedura descritta in [Creare una data factory tramite l'interfaccia utente](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Durante questa procedura, selezionare **Aggiungi al dashboard** per un accesso rapido dopo la creazione. 

Una volta creata la data factory, aprire la relativa pagina di panoramica nel portale di Azure. Selezionare il riquadro **crea & monitoraggio** per aprire la relativa pagina **introduttiva** in una scheda separata. In questa pagina è possibile continuare a creare il Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Effettuare il provisioning di un runtime di integrazione SSIS di Azure

1. Nella pagina **Attività iniziali** selezionare il riquadro **Configure SSIS Integration Runtime** (Configura SSIS Integration Runtime).

   ![Riquadro Configure SSIS Integration Runtime (Configura SSIS Integration Runtime)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Nella sezione **Impostazioni generali** del pannello **Integration Runtime installazione** completare i passaggi seguenti.

   ![Impostazioni generali](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Per **Nome**, immettere il nome del runtime di integrazione.

   1. Per **Descrizione**, immettere la descrizione del runtime di integrazione.

   1. Per **Località** selezionare la località del runtime di integrazione. Vengono visualizzate solo le località supportate. È consigliabile selezionare la stessa località del server di database che ospiterà SSISDB.

   1. Per **dimensioni nodo**selezionare le dimensioni del nodo nel cluster di Integration Runtime. Vengono visualizzate solo le dimensioni dei nodi supportate. Selezionare una dimensione del nodo elevata (scalabilità verticale) se si prevede di eseguire numerosi pacchetti con un uso intensivo di calcoli o di memoria.

   1. Per **Numero nodo**, selezionare il numero di nodi nel cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei nodi supportati. Selezionare un cluster di grandi dimensioni con molti nodi (scalabilità orizzontale) se si prevede di eseguire numerosi pacchetti in parallelo.

   1. Per **edizione/licenza**, selezionare l'edizione SQL Server per il runtime di integrazione: standard o Enterprise. Selezionare Enterprise se si prevede di usare funzionalità avanzate nel runtime di integrazione.

   1. Per **risparmiare denaro**, selezionare l'opzione vantaggio Azure Hybrid per il runtime di integrazione: **Sì** o **No**. Selezionare **Sì** se si vuole usare la propria licenza di SQL Server con Software Assurance per trarre vantaggio dai risparmi sui costi con Hybrid Use.

   1. Selezionare **Avanti**.

1. Nella sezione **Impostazioni SQL** completare i passaggi seguenti.

   ![Impostazioni SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Selezionare la casella di controllo **Crea catalogo SSIS (SSISDB) ospitato da server di database SQL di Azure/istanza gestita per archiviare i progetti/pacchetti/ambienti/log di esecuzione** per scegliere il modello di distribuzione per i pacchetti da eseguire nel Azure-SSIS IR. Scegliere il modello di distribuzione del progetto in base a cui i pacchetti vengono distribuiti nell'istanza di SSISDB ospitata dal server di database oppure il modello di distribuzione del pacchetto in base a cui i pacchetti vengono distribuiti in file system, condivisioni file o File di Azure. 
    
      Se si seleziona la casella di controllo, è necessario portare il proprio server di database per ospitare l'istanza di SSISDB che verrà creata e gestita per conto dell'utente.
   
      1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure contenente il server di database che ospiterà SSISDB. 

      1. Per **Località**, selezionare la stessa località del server di database che ospiterà SSISDB. È consigliabile selezionare la stessa località del runtime di integrazione. 

      1. Per **Catalog Database Server Endpoint** (Endpoint server di database del catalogo), selezionare l'endpoint del server di database che ospiterà SSISDB. 
    
         In base al server di database selezionato, l'istanza di SSISDB può essere creata per conto dell'utente come database singolo, come parte di un pool elastico o in un'istanza gestita e resa accessibile in una rete pubblica o tramite aggiunta a una rete virtuale. Per indicazioni sulla scelta del tipo di server di database per l'hosting di SSISDB, vedere la sezione [confrontare un singolo database SQL di Azure, un pool elastico e un'istanza gestita](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) in questo articolo. 
    
         Se si seleziona un server di database SQL di Azure con regole firewall IP/endpoint servizio rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB o se è necessario accedere ai dati locali senza configurare un runtime di integrazione self-hosted, è necessario aggiungere il Azure-SSIS IR a una rete virtuale. Per ulteriori informazioni, vedere [aggiungere un Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Selezionare la casella di controllo **Use AAD authentication with the managed identity for your ADF** (Usa l'autenticazione di AAD con l'identità gestita per Azure Data Factory) per scegliere il metodo di autenticazione per il server di database in cui ospitare SSISDB. Scegliere l'autenticazione SQL o l'autenticazione di AAD con l'identità gestita per la data factory. 
    
         Se si seleziona la casella di controllo, sarà necessario aggiungere l'identità gestita per la data factory nel gruppo di Azure AD con autorizzazioni di accesso al server di database. Per ulteriori informazioni, vedere [abilitare l'autenticazione Azure ad per un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. Per **Nome utente amministratore**, immettere il nome utente di autenticazione SQL per il server di database in cui ospitare SSISDB. 

      1. Per **Password amministratore**, immettere la password di autenticazione SQL per il server di database in cui ospitare SSISDB. 

      1. Per **Catalog Database Service Tier** (Livello di servizio del database di catalogo) selezionare il livello di servizio per il server di database in cui ospitare SSISDB. Selezionare il livello Basic, Standard o Premium oppure il nome di un pool elastico. 

      1. Selezionare **Test connessione**. Se il test ha esito positivo, selezionare **Avanti**. 

1. Nella sezione **Impostazioni avanzate** completare i passaggi seguenti.

   ![Impostazioni avanzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Per **Maximum Parallel Executions Per Node** (Numero massimo di esecuzioni parallele per nodo), selezionare il numero massimo di pacchetti da eseguire contemporaneamente per ogni nodo del cluster del runtime di integrazione. Vengono visualizzati solo i numeri dei pacchetti supportati. Selezionare un numero basso se si vogliono usare più core per eseguire un singolo pacchetto di grandi dimensioni a uso intensivo di calcoli o memoria. Selezionare un numero alto se si vogliono eseguire uno o più pacchetti di piccole dimensioni in un singolo core.

   1. Selezionare la casella di controllo **Personalizza le Azure-SSIS Integration Runtime con le installazioni aggiuntive del sistema o le installazioni dei componenti** per scegliere se si desidera aggiungere configurazioni personalizzate standard/express nel Azure-SSIS IR. Per altre informazioni, vedere [Configurazione personalizzata di Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con configurazioni personalizzate](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Per l'URI di firma di accesso condiviso del **contenitore di installazione personalizzata**, immettere l'URI SAS del contenitore in cui vengono archiviati gli script e i file associati per le configurazioni personalizzate standard.

      1. Per l' **installazione personalizzata rapida**, **selezionare nuovo** per aprire il pannello **Aggiungi installazione personalizzata rapida** , quindi selezionare tutti i tipi nel menu a discesa **tipo di installazione personalizzata Express** , ad esempio eseguire il **comando cmdkey**, **aggiungere la variabile di ambiente**, **installare il componente concesso in licenza**e così via.

         Se si seleziona installa il tipo di **componente con licenza** , è possibile selezionare tutti i componenti integrati dai partner ISV nel menu a discesa **nome componente** e, se necessario, immettere il codice Product Key acquistato da tali componenti nel campo **chiave di licenza** .
  
         Le impostazioni di aggiunta personalizzate Express verranno visualizzate nella sezione **Impostazioni avanzate** . Per rimuoverli, è possibile selezionare le caselle di controllo e quindi selezionare **Elimina**.

   1. Selezionare la casella di controllo **selezionare un VNet per il Azure-SSIS Integration Runtime da unire, consentire ad ADF di creare determinate risorse di rete e, facoltativamente, inserire gli indirizzi IP pubblici statici** per scegliere se si vuole aggiungere il runtime di integrazione a una rete virtuale. 

      Selezionarla se si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB o se è necessario l'accesso ai dati locali, ovvero se si dispone di origini dati locali o destinazioni nei pacchetti SSIS, senza configurare un runtime di integrazione self-hosted. Per altre informazioni, vedere [aggiungere Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale.

      1. Per **Località** viene selezionata la stessa località del runtime di integrazione.

      1. Per **tipo**selezionare il tipo di rete virtuale: classico o Azure Resource Manager. Si consiglia di selezionare un Azure Resource Manager rete virtuale, perché le reti virtuali classiche saranno presto deprecate.

      1. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Deve corrispondere a quello usato per il server di database SQL di Azure con endpoint di servizio della rete virtuale o istanza gestita con endpoint privato per ospitare SSISDB. Oppure deve essere lo stesso connesso alla rete locale. In caso contrario, può trattarsi di qualsiasi rete virtuale per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

      1. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve corrispondere a quello usato per il server di database SQL di Azure con gli endpoint del servizio rete virtuale per ospitare SSISDB. Oppure deve essere una subnet diversa da quella usata per l'istanza gestita con endpoint privato per l'hosting di SSISDB. In caso contrario, può essere una qualsiasi subnet per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

      1. Selezionare la casella di controllo **Bring static IP Public Addresss for your Azure-SSIS Integration Runtime** per scegliere se si desidera portare gli indirizzi IP pubblici statici per Azure-SSIS IR, in modo da poterli consentire nel firewall per le origini dati.

         Se si seleziona la casella di controllo, completare i passaggi seguenti.

         1. Per il **primo indirizzo IP pubblico statico**selezionare il primo indirizzo IP pubblico statico che soddisfi i requisiti per la Azure-SSIS IR. Se non è disponibile, fare clic su **Crea nuovo** collegamento per creare indirizzi IP pubblici statici in portale di Azure, quindi fare clic sul pulsante Aggiorna qui, in modo da poterli selezionare.
      
         1. Per il **secondo indirizzo IP pubblico statico**selezionare il secondo indirizzo IP pubblico statico che soddisfa i requisiti per la Azure-SSIS IR. Se non è disponibile, fare clic su **Crea nuovo** collegamento per creare indirizzi IP pubblici statici in portale di Azure, quindi fare clic sul pulsante Aggiorna qui, in modo da poterli selezionare.

   1. Selezionare la casella di controllo **configura Integration Runtime self-hosted come proxy per il Azure-SSIS Integration Runtime** per scegliere se configurare un runtime di integrazione self-hosted come proxy per il Azure-SSIS IR. Per altre informazioni, vedere [configurare un runtime di integrazione self-hosted come proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      ![Impostazioni avanzate con un runtime di integrazione self-hosted](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Per **Integration runtime indipendenti**, selezionare il runtime di integrazione self-hosted esistente come proxy per Azure-SSIS IR.

      1. Per **servizio collegato archiviazione di staging**selezionare il servizio collegato di archiviazione BLOB di Azure esistente o crearne uno nuovo per la gestione temporanea.

      1. Per **percorso di gestione temporanea**specificare un contenitore BLOB nell'account di archiviazione BLOB di Azure selezionato o lasciarlo vuoto per usare uno predefinito per la gestione temporanea.

   1. Selezionare **convalida VNet** > **continuare**. 

1. Nella sezione **Riepilogo** esaminare tutte le impostazioni di provisioning, aggiungere un segnalibro ai collegamenti alla documentazione consigliata e selezionare **fine** per avviare la creazione del runtime di integrazione.

   > [!NOTE]
   > Ad eccezione del tempo richiesto per un'eventuale configurazione personalizzata, questo processo verrà completato entro 5 minuti. Tuttavia, potrebbero essere necessari 20-30 minuti prima che il Azure-SSIS IR si unisca a una rete virtuale.
   >
   > Se si usa SSISDB, il servizio Data Factory si connetterà al server di database per la preparazione di SSISDB. Configura anche le autorizzazioni e le impostazioni per la rete virtuale, se specificato, e aggiunge il Azure-SSIS IR alla rete virtuale.
   > 
   > Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per informazioni su altri componenti che è possibile installare, vedere [Configurazione personalizzata di Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Nella scheda **Connessioni** passare a **Integration Runtimes** (Runtime di integrazione), se necessario. Selezionare **Aggiorna** per aggiornare lo stato.

   ![Stato creazione](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Usare i collegamenti nella colonna **Azioni** per arrestare/avviare, modificare o eliminare il runtime di integrazione. Usare l'ultimo collegamento per visualizzare il codice JSON per il runtime di integrazione. I pulsanti di modifica ed eliminazione sono abilitati solo quando il runtime di integrazione è arrestato.

   ![Azioni del runtime di integrazione SSIS di Azure](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtime di integrazione SSIS di Azure nel portale

1. Nell'interfaccia utente di Azure Data Factory passare alla scheda **Modifica** e selezionare **Connessioni**. Quindi passare alla scheda **Runtime di integrazione** per visualizzare i runtime di integrazione attualmente presenti nella data factory.

   ![Visualizzare i runtime di integrazione esistenti](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selezionare **nuovo** per creare una nuova Azure-SSIS IR.

   ![Runtime di integrazione tramite menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Nel pannello del **programma di installazione di Integration Runtime** selezionare i **pacchetti SSIS esistenti per l'esecuzione nel riquadro di Azure** e quindi fare clic su **Avanti**.

   ![Specificare il tipo di runtime di integrazione](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Per i passaggi rimanenti per configurare una Azure-SSIS IR, vedere la sezione effettuare il [provisioning di un runtime di integrazione SSIS di Azure](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Usare Azure PowerShell per creare un runtime di integrazione

In questa sezione si userà Azure PowerShell per creare una Azure-SSIS IR.

### <a name="create-variables"></a>Creare le variabili

Copiare e incollare lo script seguente. Specificare valori per le variabili. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
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

Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) usando il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

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

Se non si utilizza SSISDB, è possibile omettere i parametri `CatalogServerEndpoint`, `CatalogPricingTier`e `CatalogAdminCredential`.

Se non si usa un server di database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB o se è necessario l'accesso ai dati locali, è possibile omettere i parametri `VNetId` e `Subnet` o passare i valori vuoti. È anche possibile ometterli se si configura un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR per accedere ai dati in locale. In caso contrario, non sarà possibile ometterli e passare i valori validi dalla configurazione della rete virtuale. Per ulteriori informazioni, vedere [aggiungere un Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se si utilizza un'istanza gestita per ospitare SSISDB, è possibile omettere il parametro `CatalogPricingTier` o passarvi un valore vuoto. In caso contrario, non è possibile ometterlo e passare un valore valido dall'elenco dei piani tariffari supportati per il database SQL di Azure. Per altre informazioni, vedere [limiti delle risorse del database SQL](../sql-database/sql-database-resource-limits.md).

Se si utilizza Azure AD autenticazione con l'identità gestita per la data factory per la connessione al server di database, è possibile omettere il parametro `CatalogAdminCredential`. È tuttavia necessario aggiungere l'identità gestita per la data factory in un gruppo Azure AD con le autorizzazioni di accesso al server di database. Per ulteriori informazioni, vedere [abilitare l'autenticazione Azure ad per un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). In caso contrario, non è possibile ometterlo e passare un oggetto valido formato dal nome utente dell'amministratore del server e dalla password per l'autenticazione SQL.

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

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per informazioni su altri componenti che è possibile installare, vedere [Configurazione personalizzata di Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Di seguito è riportato lo script completo che consente di creare un runtime di integrazione SSIS di Azure.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

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

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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

2. Per distribuire il modello di Azure Resource Manager, eseguire il comando `New-AzResourceGroupDeployment`, come illustrato nell'esempio seguente. Nell'esempio `ADFTutorialResourceGroup` è il nome del gruppo di risorse. `ADFTutorialARM.json` è il file che contiene la definizione JSON per il data factory e il Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Questo comando crea il data factory e Azure-SSIS IR, ma non avvia il runtime di integrazione.

3. Per avviare la Azure-SSIS IR, eseguire il comando `Start-AzDataFactoryV2IntegrationRuntime`:

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
> Quando si effettua il provisioning di Azure-SSIS IR, vengono installati anche il Feature Pack di Azure per SSIS e Access Redistributable. Questi componenti forniscono la connettività ai file di Excel e di Access e a diverse origini dati di Azure, oltre che alle origini dati già supportate dai componenti predefiniti. Per informazioni su altri componenti che è possibile installare, vedere [Configurazione personalizzata di Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Distribuire pacchetti SSIS

Se si utilizza SSISDB, è possibile distribuirvi i pacchetti ed eseguirli nel Azure-SSIS IR usando gli strumenti SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS). Questi strumenti si connettono al server di database tramite l'endpoint del server: 

- Per un server di database SQL di Azure, il formato dell'endpoint server è `<server name>.database.windows.net`.
- Per un'istanza gestita con endpoint privato, viene `<server name>.<dns prefix>.database.windows.net`il formato dell'endpoint server.
- Per un'istanza gestita con endpoint pubblico, il formato dell'endpoint server è `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se non si utilizza SSISDB, è possibile distribuire i pacchetti in file System, condivisioni file o File di Azure ed eseguirli nel Azure-SSIS IR utilizzando gli strumenti da riga di comando `dtinstall`, `dtutil`e `dtexec`. Per altre informazioni, vedere [Distribuire pacchetti SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

In entrambi i casi, è anche possibile eseguire i pacchetti distribuiti in Azure-SSIS IR usando l'attività Esegui pacchetto SSIS in pipeline Data Factory. Per altre informazioni, vedere [Richiamare l'esecuzione di pacchetti SSIS come attività di Data Factory di prima classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Passaggi successivi

Vedere altri Azure-SSIS IR argomenti in questa documentazione:

- [Runtime di integrazione Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo fornisce informazioni sui runtime di integrazione in generale, tra cui Azure-SSIS IR.
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come recuperare e comprendere le informazioni sul Azure-SSIS IR.
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare i Azure-SSIS IR. Viene inoltre illustrato come scalare il Azure-SSIS IR aggiungendo altri nodi.
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce informazioni sull'aggiunta di Azure-SSIS IR a una rete virtuale.
