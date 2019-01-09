---
title: Proteggere un database singolo di database SQL di Azure | Microsoft Docs
description: Informazioni sulle tecniche e le funzionalità per proteggere un database singolo in database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: aea95c245b86905b7bef0a35ffaa6c5e00567111
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558635"
---
# <a name="tutorial-secure-a-single-database-in-azure-sql-database"></a>Esercitazione: Proteggere un database singolo di database SQL di Azure

Il database SQL protegge i dati in un singolo database SQL di Azure nei modi seguenti:

- Limitazione dell'accesso al database con le regole del firewall
- Uso di meccanismi di autenticazione che verificano l'identità
- Autorizzazione ai dati tramite appartenenze e autorizzazioni basate su ruoli
- Sicurezza a livello di riga
- Maschera dati dinamica

Il database SQL offre anche funzioni sofisticate di monitoraggio, controllo e rilevamento delle minacce.

> [!IMPORTANT]
> Database SQL di Azure consente di proteggere un database in un'istanza gestita usando le regole di sicurezza di rete e gli endpoint privati. Per altre informazioni, vedere [Istanza gestita di database SQL di Azure](sql-database-managed-instance-index.yml) e [Architettura della connettività di Istanza gestita di database SQL di Azure](sql-database-managed-instance-connectivity-architecture.md).

È possibile migliorare la protezione del database contro utenti malintenzionati o accessi non autorizzati con pochi semplici passaggi. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Configurare regole del firewall a livello di server per il server nel portale di Azure
> - Configurare regole del firewall a livello di database per il database con SQL Server Management Studio
> - Connettersi al database usando una stringa di connessione protetta
> - Configurare un amministratore di Azure Active Directory per Azure SQL
> - Gestire l'accesso utente
> - Proteggere i dati con la crittografia
> - Abilitare il controllo del database SQL
> - Abilitare il rilevamento delle minacce per il database SQL

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

- Installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).
- Installato Microsoft Excel.
- Creato un server e un database SQL di Azure. Vedere [Creare un database SQL di Azure nel portale di Azure](sql-database-get-started-portal.md), [Creare un singolo database SQL di Azure usando l'interfaccia della riga di comando di Azure](sql-database-cli-samples.md) e [Creare un singolo database SQL di Azure usando PowerShell](sql-database-powershell-samples.md).

> [!NOTE]
> Questa esercitazione presuppone che sia già configurato Azure Active Directory o che si usi il dominio gestito di Azure Active Directory iniziale. Per informazioni sulla configurazione di Azure Active Directory per vari scenari, consultare [Integrazione delle identità locali con Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Windows Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Windows Azure), [Amministrazione della directory di Azure AD](../active-directory/fundamentals/active-directory-administer.md) e [Gestire Azure AD con Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](../active-directory/hybrid/reference-connect-ports.md).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

I database SQL sono protetti da un firewall in Azure. Per impostazione predefinita, vengono rifiutate tutte le connessioni al server e ai database all'interno del server ad eccezione delle connessioni di altri servizi di Azure. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](sql-database-firewall-configure.md).

La configurazione più sicura consiste nell'impostare "Consenti l'accesso a Servizi di Azure" su NON ATTIVO. Se è necessario connettersi al database da un servizio cloud o da una macchina virtuale di Azure, è necessario creare un [indirizzo IP riservato](../virtual-network/virtual-networks-reserved-public-ip.md) (distribuzione classica) e consentire solo a questo indirizzo l'accesso attraverso il firewall. Se si usa il modello di distribuzione di [Resource Manager](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm), alla risorsa viene assegnato un indirizzo IP pubblico dedicato che dovrà essere autorizzato attraverso il firewall.

Seguire questa procedura per creare una [regola del firewall a livello di server del database SQL](sql-database-firewall-configure.md) per il server per consentire le connessioni dall'indirizzo IP specifico.

> [!NOTE]
> Se è stato creato un database di esempio in Azure usando una delle esercitazioni o guide rapide precedenti e si sta eseguendo questa esercitazione in un computer con lo stesso indirizzo IP assegnato durante l'esecuzione delle esercitazioni, è possibile ignorare questo passaggio perché la regola del firewall a livello di server è già stata creata.

1. Fare clic su **Database SQL** nel menu a sinistra e scegliere il database in cui si desidera configurare la regola di firewall nella pagina **Database SQL**. Si apre la pagina di panoramica per il database che visualizza il nome completo del server, ad esempio **mynewserver20170313.database.windows.net**, e offre altre opzioni di configurazione.

      ![Regola del firewall del server](./media/sql-database-security-tutorial/server-firewall-rule.png)

2. Fare clic su **Imposta firewall server** sulla barra degli strumenti, come illustrato nell'immagine precedente. Si apre la pagina **Impostazioni del firewall** per il server del database SQL.

3. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP pubblico del computer connesso al portale o immettere manualmente la regola del firewall e quindi fare clic su **Salva**.

      ![Impostare la regola del firewall del server](./media/sql-database-security-tutorial/server-firewall-rule-set.png)

4. Fare clic su **OK** e quindi sulla **X** per chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi a qualsiasi database nel server con l'indirizzo IP specificato o con un intervallo di indirizzi IP.

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Creare una regola del firewall a livello di database con SQL Server Management Studio

Le regole del firewall a livello di database consentono di creare impostazioni del firewall diverse per database differenti all'interno dello stesso server logico e di creare regole del firewall che possono essere trasferite, ovvero che seguono il database durante un failover anziché essere archiviate nel computer SQL Server. Le regole del firewall a livello di database possono essere configurate solo usando le istruzioni Transact-SQL e solo dopo aver configurato la prima regola del firewall a livello di server. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](sql-database-firewall-configure.md).

Per creare una regola del firewall specifiche del database seguire questa procedura.

1. Connettersi al database, ad esempio usando [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. In Esplora oggetti fare clic con il tasto destro del mouse sul database a cui si desidera aggiungere una regola del firewall e fare clic su **Nuova query**. Viene visualizzata una finestra di query vuota, connessa al database.

3. Nella finestra di query sostituire l'indirizzo IP con l'indirizzo IP pubblico e quindi eseguire la query seguente:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Sulla barra degli strumenti fare clic su **Esegui** per creare la regola del firewall.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Come connettere un'applicazione al database usando una stringa di connessione protetta

Per garantire una connessione crittografata e protetta tra l'applicazione client e il database SQL, la stringa di connessione deve essere configurata per:

- Richiedere una connessione crittografata, e
- Non considerare attendibile il certificato del server.

Questo consente di stabilire una connessione tramite Transport Layer Security (TLS) e di ridurre il rischio di attacco man-in-the-middle . È possibile ottenere stringhe di connessione configurate correttamente per il database SQL per i driver di client supportati nel portale di Azure, come illustrato per ADO.net in questo screenshot. Per informazioni su TLS e la connettività, vedere [Considerazioni su TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**.

2. Nella pagina **Panoramica** del database fare clic su **Mostra stringhe di connessione del database**.

3. Esaminare la stringa di connessione completa **ADO.NET**.

    ![Stringa di connessione ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Effettuare il provisioning di un amministratore di Azure Active Directory per il server di database SQL di Azure

Effettuare il provisioning di un amministratore di Azure Active Directory per il server SQL di Azure tramite il portale di Azure.

1. Nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com/) scegliere la connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory. Scegliere la directory corretta come directory predefinita di Azure AD. Questo passaggio collega la directory di Active Directory associata alla sottoscrizione con il server SQL di Azure, assicurando che la stessa sottoscrizione venga usata per Azure AD e per SQL Server. Il server SQL di Azure può ospitare il database SQL di Azure o Azure SQL Data Warehouse.

    ![choose-ad](./media/sql-database-aad-authentication/8choose-ad.png)

2. Nella pagina **SQL Server** selezionare **Amministratore di Active Directory** e, nella pagina **Amministratore di Active Directory**, selezionare **Imposta amministratore**. ![Selezionare Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  

   > [!IMPORTANT]
   > Per eseguire questa attività, è necessario essere un amministratore della società o un amministratore globale.

3. Nella pagina **Aggiungi amministratore** cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi scegliere **Seleziona**. La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Per l'elenco degli amministratori supportati, vedere la sezione **Funzionalità e limitazioni di Azure AD** in [Usare l'autenticazione di Azure Active Directory per l'autenticazione di un database SQL o di SQL Data Warehouse](sql-database-aad-authentication.md). Il controllo di accesso basata sui ruoli (RBAC) si applica solo al portale e non viene propagato a SQL Server.
    ![Selezionare l'amministratore](./media/sql-database-aad-authentication/select-admin.png)  

4. Nella parte superiore della pagina **Amministratore di Active Directory** scegliere **SALVA**.
    ![Salvare l'impostazione dell'amministratore](./media/sql-database-aad-authentication/save-admin.png)

Il processo di modifica dell'amministratore può richiedere alcuni minuti. Il nuovo amministratore è quindi visualizzato nella casella **Amministratore di Active Directory** .

   > [!NOTE]
   > Quando si configura l'amministratore di Azure AD il nuovo nome dell'amministratore, utente o gruppo, non può essere già presente nel database master virtuale come un utente dell'autenticazione del server SQL. Se presente, l’impostazione dell’amministratore di Microsoft Azure avrà esito negativo; eseguire il rollback della creazione e indicare che tale (nome) di amministratore già esiste. Poiché tale utente dell'autenticazione del server SQL non è parte di Azure AD, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure AD ha esito negativo.

## <a name="creating-database-users"></a>Creazione degli utenti del database

Prima di creare gli utenti, è innanzitutto necessario scegliere uno dei due tipi di autenticazione supportati dal database SQL di Azure:

**Autenticazione di SQL**, che usa nome utente e password per gli accessi e gli utenti che sono validi solo nel contesto di un database specifico all'interno di un server logico.

**Autenticazione di Azure Active Directory**, che usa identità gestite da Azure Active Directory.

### <a name="create-a-user-using-sql-authentication"></a>Creare un utente tramite l'autenticazione di SQL

Seguire questa procedura per creare un utente tramite l'autenticazione di SQL:

1. Connettersi al database, ad esempio usando [SQL Server Management Studio](./sql-database-connect-query-ssms.md) con le credenziali di amministratore del server.

2. In Esplora oggetti fare clic con il tasto destro del mouse sul database a cui si desidera aggiungere un nuovo utente e fare clic su **Nuova query**. Viene visualizzata una finestra di query vuota, connessa al database selezionato.

3. Nella finestra delle query, immettere la query seguente:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Sulla barra degli strumenti fare clic su **Esegui** per creare l'utente.

5. Per impostazione predefinita, l'utente può connettersi al database, ma non dispone delle autorizzazioni per leggere o scrivere dati. Per concedere le autorizzazioni all'utente appena creato, eseguire i due comandi seguenti in una nuova finestra di query

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

È consigliabile creare gli account non amministratore a livello di database per la connessione al database, a meno che non sia necessario eseguire le attività di amministrazione quali la creazione di nuovi utenti. Rivedere l'[esercitazione di Azure Active Directory](./sql-database-aad-authentication-configure.md) su come eseguire l'autenticazione con Azure Active Directory.

### <a name="create-a-user-using-azure-active-directory-authentication"></a>Creare un utente tramite l'autenticazione di Azure Active Directory

L'autenticazione di Azure Active Directory richiede la creazione di utenti del database come utenti di database indipendente. Un utente di database indipendente basato su un'identità di Azure AD è un utente di database che non può accedere al database master e del quale viene eseguito il mapping a un'identità nella directory di Azure AD associata al database. L'identità di Azure AD può essere un singolo account utente o un gruppo. Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Gli utenti del database, tranne gli amministratori, non possono essere creati tramite il portale di Azure. I ruoli RBAC non vengono propagati a SQL Server, al database SQL o a SQL Data Warehouse. I ruoli Controllo degli accessi in base al ruolo di Azure vengono usati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni di database. Ad esempio, il ruolo **Collaboratore SQL Server** non consente l'accesso per connettersi al database SQL oppure a SQL Data Warehouse. L'autorizzazione di accesso deve essere concessa direttamente nel database tramite istruzioni Transact-SQL.
> [!WARNING]
> Caratteri speciali, ad esempio i due punti `:` o la e commerciale `&`, quando vengono inclusi come nomi utente in istruzioni T-SQL CREATE LOGIN e CREATE USER, non sono supportati.

1. Connettersi al server SQL di Azure usando un account di Azure Active Directory che abbia almeno l'autorizzazione **ALTER ANY USER**.
2. In Esplora oggetti fare clic con il tasto destro del mouse sul database a cui si desidera aggiungere un nuovo utente e fare clic su **Nuova query**. Viene visualizzata una finestra di query vuota, connessa al database selezionato.

3. Nella finestra della query immettere la query seguente e modificare `<Azure_AD_principal_name>` con il nome dell'entità utente Azure AD desiderata o con il nome visualizzato di un gruppo di Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

   > [!NOTE]
   > Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo E (EXTERNAL_USER). I gruppi sono contrassegnati con il tipo X (EXTERNAL_GROUPS). Per altre informazioni, vedere [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="protect-your-data-with-encryption"></a>Proteggere i dati con la crittografia

Transparent Data Encryption, ovvero TDE, di database SQL di Azure consente di crittografare automaticamente i dati inattivi, senza richiedere alcuna modifica all'applicazione che accede al database crittografato. Per i nuovi database creati, la crittografia TDE è attiva per impostazione predefinita. Per abilitare la crittografia TDE per il database oppure per verificare che sia attivata, seguire questa procedura:

1. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**.

2. Fare clic su **Transparent Data Encryption** per aprire la pagina di configurazione di TDE.

    ![Transparent Data Encryption](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Se necessario, impostare **Crittografia dati** su Sì e fare clic su **Salva**.

Il processo di crittografia viene avviato in background. È possibile monitorare lo stato di avanzamento connettendosi al database SQL con [SQL Server Management Studio](./sql-database-connect-query-ssms.md) ed eseguendo una query sulla colonna encryption_state della visualizzazione [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). Lo stato 3 indica che il database è crittografato.

## <a name="enable-sql-database-auditing-if-necessary"></a>Abilitare il controllo del database SQL, se necessario

Il controllo del database SQL di Azure tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di Archiviazione di Azure dell'utente. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare potenziali violazioni della sicurezza. Per creare un criterio di controllo predefinito per il database SQL, seguire questa procedura:

1. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**.

2. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce**. Si noti che il controllo a livello di server è disabilitato ed è disponibile un collegamento **Visualizza impostazioni del server** che consente di visualizzare o modificare le impostazioni di controllo del server da questo contesto.

    ![Pannello di controllo](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Se si preferisce abilitare un tipo di controllo (o una posizione) diverso da quello specificato a livello di server, **attivare** Controllo e scegliere il tipo di controllo **BLOB**. Se il controllo BLOB del server è abilitato, il controllo configurato del database coesisterà con il controllo BLOB del server.

    ![Attivare il controllo](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Selezionare i **Dettagli di archiviazione** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione, dopo il quale verranno eliminati i vecchi log, quindi fare clic su **OK** nella parte inferiore.

   > [!TIP]
   > Per sfruttare al massimo i modelli di report di controllo, usare lo stesso account di archiviazione per tutti i database controllati.

5. Fare clic su **Save**.

> [!IMPORTANT]
> Se si vuole, è possibile personalizzare gli eventi controllati tramite PowerShell o l'API REST. Per altre informazioni, vedere l'articolo relativo al [servizio di controllo del database SQL](sql-database-auditing.md).
>

## <a name="enable-sql-database-threat-detection"></a>Abilitare il rilevamento delle minacce per il database SQL

Il rilevamento delle minacce offre un nuovo livello di protezione, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti possono esaminare gli eventi sospetti tramite il servizio di controllo del database SQL per determinare se sono il risultato di un tentativo di accesso, una violazione o un exploit dei dati del database. Il rilevamento delle minacce rende più semplice affrontare le minacce potenziali al database, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati.
Ad esempio, la funzionalità di rilevamento delle minacce individua determinate attività anomale nel database che indicano potenziali tentativi di attacco SQL injection. L'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori di attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, per violare o modificare i dati nel database.

1. Passare al pannello di configurazione del database SQL che si vuole monitorare. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce**.

    ![Riquadro di spostamento](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. Nel pannello di configurazione **Controllo e rilevamento minacce** impostare il controllo su **SÌ**, il che consentirà di visualizzare le impostazioni di rilevamento minacce.

3. Impostare il rilevamento delle minacce su **SÌ**.

4. Configurare l'elenco di indirizzi di posta elettronica che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.

5. Fare clic su **Salva** nel pannello di configurazione **Controllo e rilevamento minacce** per salvare il controllo nuovo o aggiornato e i criteri di rilevamento delle minacce.

    ![Riquadro di spostamento](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Se vengono rilevate attività anomale del database, si riceverà una notifica tramite posta elettronica.  Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server e l'ora dell'evento. Verranno anche fornite informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database. I passaggi successivi illustrano la procedura consigliata per un utente che riceva un messaggio di posta simile al seguente:

    ![Messaggio di posta elettronica sul rilevamento delle minacce](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. Nel messaggio di posta elettronica fare clic sul collegamento al **log di controllo SQL di Azure** che avvierà il portale di Azure visualizzando i record di controllo pertinenti intorno all'ora dell'evento sospetto.

    ![Record di controllo](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Fare clic sui record di controllo per visualizzare altre informazioni sulle attività di database sospette come l'istruzione SQL, il motivo dell'errore e l'indirizzo IP del client.

    ![Dettagli sui record](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. Nel pannello Auditing Records (Controllo record) fare clic su  **Apri in Excel** per aprire un modello Excel preconfigurato per importare ed eseguire un'analisi più approfondita del log di controllo sull'orario in cui si è verificato l'evento sospetto.

    > [!NOTE]
    > In Excel 2010 o in una versione successiva sono richiesti Power Query e l'impostazione **Combinazione rapida**.

    ![Aprire i record in Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Per configurare l'impostazione **Combinazione rapida**, nella scheda della barra multifunzione **POWER QUERY** selezionare **Opzioni** per visualizzare la finestra di dialogo Opzioni. Selezionare la sezione Privacy e scegliere la seconda opzione - "Ignora i livelli di privacy per un potenziale miglioramento delle prestazioni":

    ![Combinazione rapida di Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Per caricare i log di controllo SQL, assicurarsi che i parametri nella scheda Impostazioni siano configurati correttamente e quindi selezionare "Dati" sulla barra multifunzione e fare clic sul pulsante "Aggiorna tutto".

    ![Parametri di Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. I risultati vengono visualizzati nel foglio dei **log di controllo SQL** che consente di eseguire un'analisi più approfondita delle attività anomale rilevate e di ridurre l'impatto dell'evento di sicurezza nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come migliorare la protezione del database contro utenti malintenzionati o accessi non autorizzati con pochi semplici passaggi.  Si è appreso come:

> [!div class="checklist"]
> - Configurare regole del firewall per il server e/o il database
> - Connettersi al database usando una stringa di connessione protetta
> - Configurare un amministratore di Azure Active Directory per Azure SQL
> - Gestire l'accesso utente
> - Proteggere i dati con la crittografia
> - Abilitare il controllo del database SQL
> - Abilitare il rilevamento delle minacce per il database SQL

Passare all'esercitazione successiva per informazioni su come implementare un database di distribuzione geografica.

> [!div class="nextstepaction"]
>[Implementare un database con distribuzione geografica](sql-database-implement-geo-distributed-database.md)
