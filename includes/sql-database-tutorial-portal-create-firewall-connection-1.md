<a id="log-in-to-the-azure-portal" class="xliff"></a>

## Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

## Creare un database SQL vuoto usando il portale di Azure

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](../articles/sql-database/sql-database-service-tiers.md). Il database viene creato in un [gruppo di risorse di Azure](../articles/azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](../articles/sql-database/sql-database-features.md). 

Per creare un database SQL vuoto, attenersi alla procedura seguente. 

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** nella pagina **Nuovo** e quindi **Database SQL** nella pagina **Database**. 

   ![creare database vuoto](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. Compilare il modulo Database SQL con le informazioni seguenti, come illustrato nell'immagine precedente:   

   | Impostazione | Valore consigliato | Descrizione |
   | --------| --------------- | ----------- | 
   | **Database name** (Nome database) | mySampleDatabase | Per i nomi di database validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database). | 
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   | **Select source** (Seleziona origine) | Database vuoto | Indica che deve essere creato un database vuoto. |
   ||||

4. Fare clic su **Server** per creare e configurare un nuovo server per il nuovo database. Compilare il **modulo del nuovo server** con le informazioni seguenti: 

   | Impostazione | Valore consigliato | Descrizione |
   | --------| --------------- | ----------- | 
   | **Server name** (Nome server) | Qualsiasi nome univoco globale. | Per i nomi di server validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). | 
   | **Nome di accesso amministratore server** | Qualsiasi nome valido. | Per i nomi di accesso validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database).|
   | **Password** | Qualsiasi password valida. | La password deve contenere almeno otto caratteri delle tre categorie seguenti: maiuscole, minuscole, numeri e caratteri non alfanumerici. |
   | **Posizione** | Qualsiasi posizione valida. | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |
   ||||

   ![Creare il server di database](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. Fare clic su **Seleziona**.

6. Fare clic su **Piano tariffario** per specificare il livello di servizio e il livello delle prestazioni per il nuovo database. Per esercitazione selezionare **20 DTU** e **250** GB di memoria.

   ![Creare il database s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Fare clic su **Apply**.  

8. Selezionare **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations) (Regole di confronto)

9. Fare clic su **Crea** per effettuare il provisioning del database. Il provisioning richiede circa un minuto e mezzo per il completamento. 

10. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

   ![notifica](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

## Creare una regola del firewall a livello di server con il portale di Azure

Il servizio di database SQL crea un firewall a livello di server. Inizialmente il firewall impedisce a strumenti esterni e applicazioni di connettersi al server o ai database nel server. Le connessioni sono consentite dopo aver creato una regola del firewall per aprire indirizzi IP specifici. Seguire questi passaggi per creare una [regola del firewall a livello di server di database SQL](../articles/sql-database/sql-database-firewall-configure.md) per l'indirizzo IP del client e per abilitare la connettività esterna tramite il firewall del database SQL solo per il proprio indirizzo IP. 


> [!NOTE]
> Il database SQL di Azure comunica sulla porta 1433. È possibile connettersi al database SQL solo quando il firewall della rete consente il traffico in uscita attraverso la porta 1433.


1. Al termine della distribuzione, scegliere **Database SQL** dal menu a sinistra e fare clic su **mySampleDatabase** nella pagina **Database SQL**. Si apre la pagina di panoramica per il database, in cui è indicato il nome completo del server (ad esempio, **mynewserver20170313.database.windows.net**) e in cui sono disponibili altre opzioni di configurazione. Copiare il nome completo del server per usarlo in seguito.

   > [!IMPORTANT]
   > Questo nome completo del server è necessario per connettersi al server e ai relativi database nelle guide introduttive successive.
   > 

   ![Nome del server](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. Fare clic su **Imposta firewall server** sulla barra degli strumenti, come illustrato nell'immagine precedente. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

   ![Regola del firewall del server](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

4. Fare clic su **Salva**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

   ![Impostare la regola del firewall del server](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Fare clic su **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi al server di database SQL di Azure e ai suoi database usando uno strumento come SQL Server Management Studio (SSMS). La connessione viene eseguita da questo indirizzo IP e usa l'account amministratore del server creato in precedenza.


> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionando **NO** in questa pagina permette di disabilitare tutti i servizi di Azure.


<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

## Ottenere i valori della stringa di connessione usando il portale di Azure

Ottenere il nome completo del server per il server del database SQL di Azure nel portale di Azure. Usare il nome completo del server per connettersi al server tramite SQL Server Management Studio.

1. Accedere al [Portale di Azure](https://portal.azure.com/).

2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 

3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database individuare e quindi copiare il **Nome server**.

   ![informazioni di connessione](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
