---
title: Proteggere un database
description: Questa esercitazione illustra le tecniche e le funzionalità per proteggere un database singolo o in pool nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: bec60875561a9d821642d850c27e47d4f906aba3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885413"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Esercitazione: Proteggere un database nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Creare regole del firewall a livello di server e di database
> - Configurare un amministratore di Azure Active Directory (Azure AD)
> - Gestire l'accesso degli utenti con l'autenticazione SQL, l'autenticazione di Azure AD e le stringhe di connessione sicure
> - Abilitare funzionalità di sicurezza come Azure Defender per SQL, il controllo, la maschera dati e la crittografia

Il database SQL di Azure protegge i dati consentendo di:

- Limitare l'accesso tramite regole del firewall
- Usare meccanismi di autenticazione che verificano l'identità
- Concedere autorizzazioni tramite appartenenze e autorizzazioni basate sui ruoli
- Abilitare le funzionalità di sicurezza

> [!NOTE]
> L'istanza gestita di SQL di Azure viene protetta tramite regole di sicurezza di rete ed endpoint privati come descritto in [Istanza gestita di SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md) e in [Architettura della connettività](../managed-instance/connectivity-architecture-overview.md).

Per altre informazioni, vedere gli articoli [Database SQL di Azure - Sicurezza avanzata](/azure/sql-database/sql-database-security-index) e [Panoramica della funzionalità di sicurezza del database SQL di Azure](security-overview.md).

> [!TIP]
> Il modulo seguente di Microsoft Learn consente di imparare gratuitamente come [proteggere il database nel database SQL di Azure](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di avere i prerequisiti seguenti:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Un [server](logical-servers.md) e un database singolo
  - Per crearli, usare il [portale di Azure](single-database-create-quickstart.md), l'[interfaccia della riga di comando](az-cli-script-samples-content-guide.md) o [PowerShell](powershell-script-content-guide.md)

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Per tutti i passaggi dell'esercitazione, accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Creare regole del firewall

I database nel database SQL sono protetti da firewall in Azure. Per impostazione predefinita, vengono rifiutate tutte le connessioni al server e al database. Per altre informazioni, vedere [regole del firewall a livello di server e di database](firewall-configure.md).

La configurazione più sicura consiste nell'impostare **Consenti l'accesso a Servizi di Azure** su **NO**. Quindi, creare un [IP riservato (distribuzione classica)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) per la risorsa da connettere, ad esempio una macchina virtuale di Azure o un servizio cloud, e consentire l'accesso tramite il firewall solo a questo indirizzo IP. Se si usa il modello di distribuzione di [Resource Manager](/azure/virtual-network/virtual-network-ip-addresses-overview-arm), è necessario un indirizzo IP pubblico dedicato per ogni risorsa.

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non è possibile connettersi al server a meno che l'amministratore non apra la porta 1433.

### <a name="set-up-server-level-firewall-rules"></a>Configurare le regole del firewall a livello di server

Le regole del firewall IP a livello di server si applicano a tutti i database all'interno dello stesso server.

Per configurare una regola del firewall a livello di server:

1. Nel portale di Azure scegliere **Database SQL** dal menu a sinistra, quindi selezionare il database nella pagina **Database SQL**.

    ![Regola del firewall del server](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Assicurarsi di copiare il nome completo del server, ad esempio *server.database.windows.net*, per l'uso successivo nell'esercitazione.

1. Nella pagina **Panoramica** selezionare **Imposta firewall server**. Verrà visualizzata la pagina **Impostazioni del firewall** per il server.

   1. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. La regola può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP. Selezionare **Salva**.

      ![Impostare la regola del firewall del server](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. Selezionare **OK** e chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi a qualsiasi database nel server con l'indirizzo IP specificato o con un intervallo di indirizzi IP.

### <a name="setup-database-firewall-rules"></a>Configurare le regole del firewall per il database

Le regole del firewall a livello di database di applicano solo ai singoli database. Il database conserverà queste regole durante un failover del server. Le regole del firewall a livello di database possono essere configurate solo tramite istruzioni Transact-SQL (T-SQL) e solo dopo aver configurato una regola del firewall a livello di server.

Per configurare una regola del firewall a livello di database:

1. Connettersi al database, ad esempio usando [SQL Server Management Studio](connect-query-ssms.md).

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

1. Nella finestra della query aggiungere questa istruzione e sostituire l'indirizzo IP con l'indirizzo IP pubblico:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Sulla barra degli strumenti selezionare **Esegui** per creare la regola del firewall.

> [!NOTE]
> È anche possibile creare una regola del firewall a livello di server in SSMS usando il comando [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), ma è necessario essere connessi al database *master*.

## <a name="create-an-azure-ad-admin"></a>Creare un amministratore di Azure AD

Assicurarsi di usare il dominio gestito di Azure Active Directory (AD) appropriato. Selezionare il dominio di AD nell'angolo in alto a destra del portale di Azure. Con questa procedura è possibile accertarsi che venga usata la stessa sottoscrizione sia per Azure AD sia per il server SQL logico che ospita il database o il data warehouse.

   ![choose-ad](./media/secure-database-tutorial/8choose-ad.png)

Per impostare l'amministratore di Azure AD:

1. Nella pagina **SQL Server** del portale di Azure selezionare **Amministratore di Active Directory**. Quindi selezionare **Imposta amministratore**.

    ![Active Directory: selezione](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Per eseguire questa attività, è necessario essere un amministratore della società o un amministratore globale.

1. Nella pagina **Aggiungi amministratore** individuare e selezionare l'utente o il gruppo di AD, quindi scegliere **Seleziona**. L'elenco include tutti i membri e i gruppi di Active Directory e quelli disattivati non sono supportati come amministratori di Azure AD. Vedere [Funzionalità e limitazioni di Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![Selezionare l'amministratore](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Il controllo degli accessi in base al ruolo si applica solo al portale e non viene propagato a SQL Server.

1. Nella parte superiore della pagina **Amministratore di Active Directory** selezionare **Salva**.

    Il processo di modifica dell'amministratore può richiedere diversi minuti. Il nuovo amministratore verrà visualizzato nella casella **Amministratore di Active Directory**.

> [!NOTE]
> Quando si imposta un amministratore di Azure AD, il nome del nuovo amministratore (utente o gruppo) non può essere già presente nel database *master* come utente o account di accesso di SQL Server. Se presente, la configurazione non riesce e verrà eseguito il rollback delle modifiche, a indicare che tale nome di amministratore esiste già. Poiché l'utente o l'account di accesso di SQL Server non fa parte di Azure AD, non è possibile connetterlo al server tramite l'autenticazione di Azure AD.

Per informazioni sulla configurazione di Azure AD, vedere:

- [Integrare le identità locali con Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Aggiungere un nome di dominio personalizzato ad Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure supporta la federazione con AD di Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Amministrare la directory di Azure AD](../../active-directory/fundamentals/active-directory-whatis.md)
- [Gestire Azure AD con PowerShell](/powershell/azure/?view=azureadps-2.0)
- [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gestire l'accesso al database

Per gestire l'accesso al database, aggiungervi gli utenti oppure consentire loro l'accesso tramite stringhe di connessione sicure. Le stringhe di connessione sono utili per le applicazioni esterne. Per altre informazioni, vedere [Gestire gli account di accesso e gli account utente](logins-create-manage.md) e [Autenticazione di Active Directory](authentication-aad-overview.md).

Per aggiungere utenti, scegliere il tipo di autenticazione del database:

- **Autenticazione di SQL**: usa nome utente e password per gli accessi, che sono validi solo nel contesto di uno specifico database all'interno del server

- **Autenticazione di Azure AD**: usa le identità gestite da Azure AD

### <a name="sql-authentication"></a>Autenticazione SQL

Per aggiungere un utente con l'autenticazione di SQL:

1. Connettersi al database, ad esempio usando [SQL Server Management Studio](connect-query-ssms.md).

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

1. Nella finestra della query immettere il comando seguente:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Sulla barra degli strumenti selezionare **Esegui** per creare l'utente.

1. Per impostazione predefinita, l'utente può connettersi al database, ma non dispone delle autorizzazioni per leggere o scrivere dati. Per concedere le autorizzazioni, eseguire i due comandi seguenti in una nuova finestra della query:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Creare gli account per gli utenti non amministratori a livello di database, a meno che non abbiano l'esigenza di eseguire attività di amministrazione come la creazione di nuovi utenti.

### <a name="azure-ad-authentication"></a>Autenticazione di Azure AD

Per l'autenticazione di Azure Active Directory, è necessario che gli utenti del database vengano creati come utenti di database indipendente. L'utente di un database indipendente viene mappato a un'identità nella directory di Azure AD associata al database e non ha un account di accesso nel database *master*. L'identità di Azure AD può essere relativa un singolo utente o a un gruppo. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](/sql/relational-databases/security/contained-database-users-making-your-database-portable) e consultare l'[esercitazione di Azure AD](authentication-aad-configure.md) sull'autenticazione tramite Azure AD.

> [!NOTE]
> Gli utenti del database, ad eccezione degli amministratori, non possono essere creati tramite il portale di Azure. I ruoli di Azure non vengono propagati in SQL Server, database o data warehouse. Vengono usati solo per la gestione delle risorse di Azure e non si applicano alle autorizzazioni per il database.
>
> Ad esempio, il ruolo *Collaboratore SQL Server* non concede l'accesso per connettersi a un database o a un data warehouse. Questa autorizzazione deve essere concessa all'interno del database tramite istruzioni T-SQL.

> [!IMPORTANT]
> I caratteri speciali, come i due punti `:` o la e commerciale `&`, non sono supportati nei nomi utente e nelle istruzioni T-SQL `CREATE LOGIN` e `CREATE USER`.

Per aggiungere un utente con l'autenticazione di Azure AD:

1. Connettersi al server in Azure usando un account Azure AD che abbia almeno l'autorizzazione *ALTER ANY USER*.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

1. Nella finestra della query immettere il comando seguente e sostituire `<Azure_AD_principal_name>` con il nome dell'entità utente di Azure AD o con il nome visualizzato di un gruppo di Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo `E (EXTERNAL_USER)` e con il tipo `X (EXTERNAL_GROUPS)` per i gruppi. Per altre informazioni, vedere [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Stringhe di connessione sicure

Per garantire una connessione crittografata e sicura tra l'applicazione client e il database SQL, è necessario configurare una stringa di connessione per:

- Richiedere una connessione crittografata
- Non considerare attendibile il certificato del server

La connessione viene stabilita tramite Transport Layer Security (TLS), riducendo il rischio di attacchi man-in-the-middle. Le stringhe di connessione sono disponibili per ogni database e sono preconfigurate per supportare driver client come ADO.NET, JDBC, ODBC e PHP. Per informazioni su TLS e la connettività, vedere [Considerazioni su TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Per copiare una stringa di connessione sicura:

1. Nel portale di Azure scegliere **Database SQL** dal menu a sinistra, quindi selezionare il database nella pagina **Database SQL**.

1. Nella pagina **Panoramica** selezionare **Mostra stringhe di connessione del database**.

1. Selezionare la scheda di un driver e copiare la stringa di connessione completa.

    ![Stringa di connessione ADO.NET](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza

Il database SQL di Azure include funzionalità di sicurezza accessibili dal portale di Azure. Queste funzionalità sono disponibili sia per il database che per il server, ad eccezione della maschera dei dati, che è disponibile solo nel database. Per altre informazioni, vedere gli articoli relativi a [Azure Defender per SQL](azure-defender-for-sql.md), [controllo](../../azure-sql/database/auditing-overview.md), [maschera dati dinamica](dynamic-data-masking-overview.md) e [Transparent Data Encryption](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>Azure Defender per SQL

La funzionalità Azure Defender per SQL rileva le potenziali minacce nel momento in cui si verificano e invia avvisi di sicurezza per le attività anomale. Gli utenti possono esaminare gli eventi sospetti con la funzionalità di controllo per determinare se si tratta di un tentativo di accesso, violazione o exploit dei dati del database. Possono inoltre accedere a una panoramica sulla sicurezza, che include una valutazione della vulnerabilità oltre allo strumento di individuazione e classificazione dei dati.

> [!NOTE]
> Un esempio di minaccia è SQL injection, un processo in cui gli utenti malintenzionati inseriscono codice SQL dannoso negli input dell'applicazione. L'applicazione può quindi eseguire inconsapevolmente tale codice, consentendo agli utenti malintenzionati di violare o modificare i dati del database.

Per abilitare Azure Defender per SQL:

1. Nel portale di Azure scegliere **Database SQL** dal menu a sinistra, quindi selezionare il database nella pagina **Database SQL**.

1. Nella pagina **Panoramica** selezionare il collegamento **Nome server**. Verrà visualizzata la pagina del server.

1. Nella pagina **SQL Server** cercare la sezione **Sicurezza** e selezionare **Centro sicurezza**.

   1. Selezionare **ON** in **Azure Defender per SQL** per abilitare la funzionalità. Scegliere un account di archiviazione per il salvataggio dei risultati della valutazione della vulnerabilità. Selezionare quindi **Salva**.

      ![Riquadro di spostamento](./media/secure-database-tutorial/threat-settings.png)

      È anche possibile configurare gli indirizzi di posta elettronica a cui ricevere avvisi di sicurezza, dettagli sulle risorse di archiviazione e tipi di rilevamento delle minacce.

1. Tornare nella pagina **Database SQL** del database e selezionare **Centro sicurezza** nella sezione **Sicurezza**. In questa sezione sono disponibili vari indicatori della sicurezza per il database.

    ![Stato minacce](./media/secure-database-tutorial/threat-status.png)

Se vengono rilevate attività anomale, si riceverà un messaggio di posta elettronica con informazioni sull'evento, tra cui la natura dell'attività, il database, il server, l'ora dell'evento, le possibili cause e le azioni consigliate per analizzare e contrastare la potenziale minaccia. Se si riceve un messaggio di questo tipo, selezionare il collegamento che punta al **log di controllo del database SQL di Azure** per avviare il portale di Azure e visualizzare i record di controllo pertinenti per l'ora dell'evento.

   ![Messaggio di posta elettronica sul rilevamento delle minacce](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Controllo

La funzionalità di controllo tiene traccia degli eventi del database e li scrive in un log di controllo nell'archiviazione di Azure, nei log di Monitoraggio di Azure o in un hub eventi. Il controllo consente di rispettare la conformità alle normative, identificare le attività del database e acquisire informazioni su discrepanze e anomalie che potrebbero indicare potenziali violazioni della sicurezza.

Per abilitare il controllo:

1. Nel portale di Azure scegliere **Database SQL** dal menu a sinistra, quindi selezionare il database nella pagina **Database SQL**.

1. Nella sezione **Sicurezza** selezionare **Controllo**.

1. Nelle impostazioni di **Controllo** specificare i valori seguenti:

   1. Impostare **Controllo** su **SÌ**.

   1. Selezionare una delle opzioni seguenti per **Destinazione del log di controllo**:

       - **Archiviazione**, un account di archiviazione di Azure in cui vengono salvati i log degli eventi, che possono essere scaricati come file con estensione *xel*

          > [!TIP]
          > Per sfruttare al meglio i modelli di report di controllo, usare lo stesso account di archiviazione per tutti i database controllati.

       - **Log Analytics**, un servizio che archivia automaticamente gli eventi per le query o per ulteriori analisi

           > [!NOTE]
           > Per supportare funzionalità avanzate come l'analisi, le regole personalizzate degli avvisi e le esportazioni in Excel o Power BI, è necessaria un'**area di lavoro di Log Analytics**. Senza quest'area di lavoro, è disponibile solo l'editor di query.

       - **Hub eventi**, che consente di instradare gli eventi per l'uso in altre applicazioni

   1. Selezionare **Salva**.

      ![Impostazione di Controllo](./media/secure-database-tutorial/audit-settings.png)

1. A questo punto è possibile selezionare **Visualizza log di controllo** per visualizzare i dati sugli eventi del database.

    ![Record di controllo](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> Per informazioni su come personalizzare ulteriormente gli eventi di controllo tramite PowerShell o l'API REST, vedere [Introduzione al controllo del database SQL](../../azure-sql/database/auditing-overview.md).

### <a name="dynamic-data-masking"></a>Maschera dati dinamica

La funzionalità di maschera dei dati nasconde automaticamente i dati sensibili nel database.

Per abilitare la maschera dei dati:

1. Nel portale di Azure scegliere **Database SQL** dal menu a sinistra, quindi selezionare il database nella pagina **Database SQL**.

1. Nella sezione **Sicurezza** selezionare **Dynamic Data Masking**.

1. Nelle impostazioni di **Dynamic Data Masking** selezionare **Aggiungi maschera** per aggiungere una regola di mascheramento. Azure popola automaticamente gli schemi, le tabelle e le colonne del database disponibili tra cui scegliere.

    ![Impostazioni della maschera](./media/secure-database-tutorial/mask-settings.png)

1. Selezionare **Salva**. Le informazioni selezionate saranno ora mascherate per la privacy.

    ![Esempio di maschera](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

Questa funzionalità crittografa automaticamente i dati inattivi e non richiede modifiche alle applicazioni che accedono al database crittografato. Per i nuovi database, la crittografia è attivata per impostazione predefinita. È anche possibile crittografare i dati usando SSMS e la funzionalità [Always Encrypted](always-encrypted-certificate-store-configure.md).

Per abilitare o verificare la crittografia:

1. Nel portale di Azure scegliere **Database SQL** dal menu a sinistra, quindi selezionare il database nella pagina **Database SQL**.

1. Nella sezione **Sicurezza** selezionare **Transparent Data Encryption**.

1. Se necessario, impostare **Crittografia dati** su **SÌ**. Selezionare **Salva**.

    ![Transparent Data Encryption](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> Per visualizzare lo stato di crittografia, connettersi al database tramite [SSMS](connect-query-ssms.md) ed eseguire una query sulla colonna `encryption_state` della visualizzazione [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). Lo stato `3` indica che il database è crittografato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come migliorare la sicurezza del database con pochi, semplici passaggi. Si è appreso come:

> [!div class="checklist"]
>
> - Creare regole del firewall a livello di server e di database
> - Configurare un amministratore di Azure Active Directory (AD)
> - Gestire l'accesso degli utenti con l'autenticazione SQL, l'autenticazione di Azure AD e le stringhe di connessione sicure
> - Abilitare funzionalità di sicurezza come Azure Defender per SQL, il controllo, la maschera dati e la crittografia

Passare all'esercitazione successiva per informazioni su come implementare la distribuzione geografica.

> [!div class="nextstepaction"]
>[Implementare un database con distribuzione geografica](geo-distributed-application-configure-tutorial.md)
