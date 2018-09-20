---
title: Creare il database SQL a disponibilità elevata in Azure Stack | Microsoft Docs
description: Informazioni su come creare un provider di risorse di SQL Server computer host e i database di SQL AlwaysOn a disponibilità elevata con Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b353b1c359ea663201b3fa5a72b28ab3298acd09
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369162"
---
# <a name="tutorial-create-highly-available-sql-databases"></a>Esercitazione: Creare il database SQL a disponibilità elevata

Come un utente di tenant di Azure Stack, è possibile configurare le macchine virtuali server per ospitare i database di SQL Server. Dopo un SQL server di hosting è stata creata e gestita da Azure Stack, utenti che hanno sottoscritto a SQL services possono facilmente creare database SQL.

Questa esercitazione illustra come usare un modello di avvio rapido di Azure Stack per creare un [gruppo di disponibilità AlwaysOn di SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), aggiungerlo come un Server di Hosting SQL di Azure Stack e quindi creare un database SQL a disponibilità elevata.

Che cosa si apprenderà:

> [!div class="checklist"]
> * Creare un gruppo di disponibilità AlwaysOn di SQL Server da un modello
> * Creare un Server di Hosting SQL di Azure Stack
> * Creare un database SQL a disponibilità elevata

In questa esercitazione, un gruppo di disponibilità della macchina virtuale SQL Server AlwaysOn due verrà creato e configurato utilizzando elementi disponibili del marketplace Azure Stack. 

Prima di iniziare i passaggi descritti in questa esercitazione, assicurarsi che l'operatore di Stack di Azure ha reso i seguenti elementi disponibile nel marketplace di Azure Stack:

> [!IMPORTANT]
> Tutti gli elementi seguenti sono necessari per il modello di avvio rapido di Azure Stack da utilizzare.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) immagine del marketplace.
- SQL Server 2016 SP1 o SP2 (Enterprise, Standard o Developer) nell'immagine server Windows Server 2016. Questa esercitazione Usa la [SQL Server 2016 SP2 Enterprise in Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.sqlserver2016sp2enterprisewindowsserver2016) immagine del marketplace.
- [Estensione di SQL Server IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) versione 1.2.30 o versione successiva. L'estensione SQL IaaS consente di installare i componenti necessari richiesti per gli elementi del Marketplace SQL Server per tutte le versioni di Windows. Consente le impostazioni specifiche del linguaggio SQL deve essere configurata nelle macchine virtuali di SQL. Se l'estensione non è installato in locale Marketplace, verrà eseguito il provisioning di SQL.
- [Estensione script personalizzata per Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) versione 1.9.1 o versione successiva. L'estensione Script personalizzato è uno strumento che può essere utilizzato per avviare automaticamente le attività di personalizzazione di post-distribuzione della macchina virtuale.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) versione 2.76.0.0 o versione successiva. DSC è una piattaforma di gestione di Windows PowerShell che consente la distribuzione e la gestione dei dati di configurazione per i servizi software e la gestione dell'ambiente in cui questi servizi vengono eseguiti.

  > [!TIP]
  > Sarà possibile visualizzare l'estensione IaaS di SQL Server, estensione script personalizzata o elementi del marketplace DSC di PowerSHell durante la creazione di una macchina virtuale dal portale per gli utenti. Contattare l'operatore di Azure Stack per assicurarsi che questi elementi sono stati scaricati da Azure prima di iniziare i passaggi descritti in questa esercitazione.

Per altre informazioni sull'aggiunta di elementi nel Marketplace di Azure Stack, vedere la [Panoramica di Azure Stack Marketplace](.\.\azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Creare un gruppo di disponibilità AlwaysOn di SQL Server
Usare i passaggi descritti in questa sezione per distribuire il gruppo di disponibilità AlwaysOn di SQL Server usando il [modello di avvio rapido di Azure Stack alwayson di sql 2016](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Questo modello distribuisce due istanze di SQL Server Enterprise Edition, Standard o Developer in un gruppo di disponibilità AlwaysOn. Crea le risorse seguenti:

- Un gruppo di sicurezza di rete
- Una rete virtuale
- Account di archiviazione quattro (uno per Active Directory (AD), uno per SQL, una per la condivisione file di controllo e uno per la diagnostica della macchina virtuale)
- Quattro indirizzi IP pubblici (uno per Active Directory, due per ogni VM di SQL e uno per il bilanciamento del carico pubblico associato al listener di SQL AlwaysOn)
- Uno esterno bilanciamento del carico per le macchine virtuali SQL con indirizzo IP pubblico associato al listener di SQL AlwaysOn
- Una macchina virtuale (Windows Server 2016) configurata come Controller di dominio per una nuova foresta con un singolo dominio
- Due macchine virtuali (Windows Server 2016) configurata con SQL Server 2016 SP1 o SP2 Enterprise, Standard o Developer Edition e in cluster. Deve trattarsi di immagini del marketplace.
- Una macchina virtuale (Windows Server 2016) è configurata come condivisione file di controllo per il cluster
- Un set che contiene il controllo di condivisione file e SQL le macchine virtuali di disponibilità  

> [!NOTE]
> Eseguire questi passaggi dal portale per gli utenti Azure Stack come un utente del tenant con una sottoscrizione che fornisce funzionalità IaaS (calcolo, rete, servizi di archiviazione).

1. Accedere al portale per gli utenti:
    - Per una distribuzione del sistema integrato, l'indirizzo di portale varia in base della soluzione area e nome di dominio esterno. Sarà nel formato https://portal.&lt; *area geografica*&gt;.&lt; *FQDN*&gt;.
    - Se si usa Azure Stack Development Kit (ASDK), l'indirizzo del portale utenti è [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selezionare **\+** **crea una risorsa** > **Custom**, quindi **distribuzione modello**.

   ![Distribuzione del modello personalizzato](media/azure-stack-tutorial-sqlrp/custom-deployment.png)


3. Nel **distribuzione personalizzata** blade, selezionare **modifica modello** > **modello di avvio rapido** e quindi usare l'elenco a discesa di modelli personalizzati disponibili per Selezionare il **alwayson di sql 2016** modello, fare clic su **OK**e quindi **Salva**.

   ![Seleziona modello di avvio rapido](./media/azure-stack-tutorial-sqlrp/quickstart-template.png)


4. Nel **distribuzione personalizzata** blade, selezionare **Upravit parametry** ed esaminare i valori predefiniti. Modificare i valori in base alle esigenze per fornire tutte le informazioni sui parametri necessari e quindi fare clic su **OK**.<br><br> Come minimo:

    - Specificare le password complesse per i parametri ADMINPASSWORD SQLSERVERSERVICEACCOUNTPASSWORD e SQLAUTHPASSWORD.
    - Immettere il suffisso DNS per la ricerca inversa in lettere minuscole per il parametro del suffisso DNS (**azurestack.external** per le installazioni ASDK).
    
    ![Parametri di distribuzione personalizzato](./media/azure-stack-tutorial-sqlrp/edit-parameters.png)

5. Nel **distribuzione personalizzata** blade, scegliere la sottoscrizione da usare e creare un nuovo gruppo di risorse o selezionare un gruppo di risorse per la distribuzione personalizzata.<br><br> Successivamente, selezionare la località del gruppo di risorse (**locale** per le installazioni ASDK) e quindi fare clic su **crea**. Le impostazioni di distribuzione personalizzato verranno convalidate e quindi avviare la distribuzione.

    ![Parametri di distribuzione personalizzato](./media/azure-stack-tutorial-sqlrp/create-deployment.png)


6. Nel portale per gli utenti, selezionare **gruppi di risorse** e quindi il nome del gruppo di risorse creato per la distribuzione personalizzata (semplicemente **resource-group** per questo esempio). Visualizzare lo stato della distribuzione per verificare che tutte le distribuzioni sono state completate correttamente.<br><br>Successivamente, esaminare gli elementi del gruppo di risorse e selezionare il **SQLPIPsql\<nome del gruppo di risorse\>**  elemento dell'indirizzo IP pubblico. Registrare l'indirizzo IP pubblico e l'indirizzo IP pubblico del servizio di bilanciamento carico di dominio completo. È necessario fornirlo a un operatore di Azure Stack, che consente di creare un server di hosting SQL sfruttando questo gruppo di disponibilità SQL AlwaysOn.

   > [!NOTE]
   > La distribuzione del modello richiederà diverse ore.

   ![Parametri di distribuzione personalizzato](./media/azure-stack-tutorial-sqlrp/deployment-complete.png)

### <a name="enable-automatic-seeding"></a>Abilitare il seeding automatico
Dopo che il modello è distribuito e configurato il gruppo di disponibilità SQL AlwaysON, è necessario abilitare [SEED automatica](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) in ogni istanza di SQL Server nel gruppo di disponibilità. 

Quando si crea un gruppo di disponibilità con seeding automatico, SQL Server crea automaticamente le repliche secondarie per ogni database nel gruppo senza alcun altro intervento manuale necessario per assicurare la disponibilità elevata dei database AlwaysOn.

Usare questi comandi SQL per configurare il seeding automatico per il gruppo di disponibilità AlwaysOn.

Nell'istanza SQL primaria (sostituire <InstanceName> con il nome di istanza primaria SQL Server):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Script di istanza SQL principale](./media/azure-stack-tutorial-sqlrp/sql1.png)

Nelle istanze SQL secondarie (sostituire < availability_group_name > con il nome del gruppo di disponibilità AlwaysOn):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Script dell'istanza di SQL secondario](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Configurare l'autenticazione del database indipendente
Prima di aggiungere un database indipendente a un gruppo di disponibilità, verificare che l'opzione di database indipendente l'autenticazione server è impostato su 1 per ogni istanza del server che ospita una replica di disponibilità per il gruppo di disponibilità. Per altre informazioni, vedere [l'autenticazione del database indipendente](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Usare i comandi seguenti per impostare l'opzione di server di autenticazione di database indipendente per ogni istanza di SQL Server nel gruppo di disponibilità:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Autenticazione del database set di contenuto](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Creare un Server di Hosting SQL di Azure Stack
Dopo il gruppo di disponibilità AlwaysOn di SQL Server è stato creato e configurato correttamente, un operatore di Azure Stack necessario creare un Azure Stack di SQL Server di Hosting per rendere disponibili agli utenti di creare i database alla capacità aggiuntiva. 

Assicurarsi di fornire l'operatore di Azure Stack, l'indirizzo IP pubblico o dominio completo per l'indirizzo IP pubblico del bilanciamento del carico SQL che è stato registrato in precedenza quando è stato creato il gruppo di risorse del gruppo di disponibilità SQL AlwaysOn (**SQLPIPsql\<risorsa nome del gruppo\>**). Inoltre, l'operatore necessario conoscere le credenziali di autenticazione usate per accedere alle istanze di SQL nel gruppo di disponibilità AlwaysOn di SQL Server.

> [!NOTE]
> Questo passaggio deve essere eseguito dal portale di amministrazione di Azure Stack da un operatore di Azure Stack.

Con listener di servizio di bilanciamento di carico del gruppo di disponibilità SQL AlwaysOn SQL e indirizzo IP pubblico informazioni di accesso di autenticazione fornite dall'utente tenant, un operatore di Stack di Azure possono ora [creare un Server di Hosting SQL usando il gruppo di disponibilità SQL AlwaysOn ](.\.\azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Assicurarsi anche che l'operatore di Stack di Azure ha creato i piani e offerte da rendere disponibile la creazione del database SQL AlwaysOn per gli utenti. L'operatore dovrà aggiungere il **Microsoft.SqlAdapter** a un piano di servizio e creare una nuova quota in modo specifico per i database a disponibilità elevata. Per altre informazioni sulla creazione dei piani, vedere [Panoramica di piano, offerta, quote e sottoscrizione](.\.\azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> Il **Microsoft.SqlAdapter** servizio non sarà disponibile per l'aggiunta ai piani finché il [provider di risorse di SQL Server è stato distribuito](.\.\azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Creare un database SQL a disponibilità elevata
Dopo aver SQL AlwaysOn gruppo di disponibilità è stato creato, configurato e aggiunto come un Server di Hosting SQL di Azure Stack da un operatore di Azure Stack, un utente di tenant con una sottoscrizione, incluse le funzionalità di database di SQL Server può creare database SQL che supportano Funzionalità AlwaysOn seguendo i passaggi descritti in questa sezione. 

> [!NOTE]
> Eseguire questi passaggi dal portale per gli utenti Azure Stack come un utente di tenant con una sottoscrizione che fornisce funzionalità di SQL Server (servizio Microsoft.SQLAdapter).

1. Accedere al portale per gli utenti:
    - Per una distribuzione del sistema integrato, l'indirizzo di portale varia in base della soluzione area e nome di dominio esterno. Sarà nel formato https://portal.&lt; *area geografica*&gt;.&lt; *FQDN*&gt;.
    - Se si usa Azure Stack Development Kit (ASDK), l'indirizzo del portale utenti è [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selezionare **\+** **crea una risorsa** > **dati \+ archiviazione**e quindi **SQL Database**.<br><br>Fornire le informazioni sulle proprietà di database necessari inclusi nome, le regole di confronto, la dimensione massima e la sottoscrizione, gruppo di risorse e percorso da usare per la distribuzione. 

   ![Creare il database SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Selezionare **SKU** e quindi scegliere il SQL che ospita Server SKU appropriato da usare. In questo esempio, l'operatore di Stack di Azure ha creato il **Enterprise-disponibilità elevata** SKU per supportare la disponibilità elevata per i gruppi di disponibilità SQL AlwaysOn.

   ![Selezionare lo SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Selezionare **account di accesso** > **creare un nuovo account di accesso** e quindi fornire le credenziali di autenticazione SQL da utilizzare per il nuovo database. Al termine, fare clic su **OK** e quindi **crea** per avviare il processo di distribuzione di database.

   ![Creare account di accesso](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Quando la distribuzione del database SQL viene completato correttamente, esaminare le proprietà del database per individuare la stringa di connessione da utilizzare per la connessione al nuovo database a disponibilità elevata. 

   ![Visualizza stringa di connessione](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un gruppo di disponibilità AlwaysOn di SQL Server da un modello
> * Creare un Server di Hosting SQL di Azure Stack
> * Creare un database SQL a disponibilità elevata

Passare all'esercitazione successiva per apprendere come:
> [!div class="nextstepaction"]
> [Creare il database MySQL a disponibilità elevata](azure-stack-tutorial-mysql.md)