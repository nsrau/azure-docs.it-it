---
title: Proteggere il database SQL di Azure | Microsoft Docs
description: "Informazioni sulle tecniche e le funzionalità per proteggere il database SQL di Azure."
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/28/2017
ms.author: daredis
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 4ae3145daa4968ae619f965f3346ef03bccb0109
ms.contentlocale: it-it
ms.lasthandoff: 09/05/2017

---
# <a name="secure-your-azure-sql-database"></a>Proteggere il database SQL di Azure

Il database SQL protegge i dati limitando l'accesso al database usando regole del firewall, i meccanismi di autenticazione che richiedono agli utenti di dimostrare la propria identità e l'autorizzazione per i dati tramite le appartenenze basate sui ruoli e le autorizzazioni, oltre che tramite la sicurezza a livello di riga e la maschera dati dinamica.

È possibile migliorare la protezione del database contro utenti malintenzionati o accessi non autorizzati con pochi semplici passaggi. In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Configurare regole del firewall a livello di server per il server nel portale di Azure
> * Configurare regole del firewall a livello di database per il database con SQL Server Management Studio
> * Connettersi al database usando una stringa di connessione protetta
> * Gestire l'accesso utente
> * Proteggere i dati con la crittografia
> * Abilitare il controllo del database SQL
> * Abilitare il rilevamento delle minacce per il database SQL

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

- Installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Installato Microsoft Excel.
- Creato un server e un database SQL di Azure. Vedere [Creare un database SQL di Azure nel portale di Azure](sql-database-get-started-portal.md), [Creare un singolo database SQL di Azure usando l'interfaccia della riga di comando di Azure](sql-database-get-started-cli.md) e [Creare un singolo database SQL di Azure usando PowerShell](sql-database-get-started-powershell.md). 

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

I database SQL sono protetti da un firewall in Azure. Per impostazione predefinita, vengono rifiutate tutte le connessioni al server e ai database all'interno del server ad eccezione delle connessioni di altri servizi di Azure. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](sql-database-firewall-configure.md).

La configurazione più sicura consiste nell'impostare "Consenti l'accesso a Servizi di Azure" su NON ATTIVO. Se è necessario connettersi al database da un servizio cloud o da una macchina virtuale di Azure, è necessario creare un [indirizzo IP riservato](../virtual-network/virtual-networks-reserved-public-ip.md) e consentire solo a questo indirizzo l'accesso tramite firewall. 

Seguire questa procedura per creare una [regola del firewall a livello di server del database SQL](sql-database-firewall-configure.md) per il server per consentire le connessioni dall'indirizzo IP specifico. 

> [!NOTE]
> Se è stato creato un database di esempio in Azure usando una delle esercitazioni o guide rapide precedenti e si sta eseguendo questa esercitazione in un computer con lo stesso indirizzo IP assegnato durante l'esecuzione delle esercitazioni, è possibile ignorare questo passaggio perché la regola del firewall a livello di server è già stata creata.
>

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

Le regole del firewall a livello di database consentono di creare impostazioni del firewall diverse per database differenti all'interno dello stesso server logico e di creare regole del firewall che possono essere trasferite, ovvero che seguono il database durante un [failover](sql-database-geo-replication-overview.md) anziché essere archiviate nel computer SQL Server. Le regole del firewall a livello di database possono essere configurate solo usando le istruzioni Transact-SQL e solo dopo aver configurato la prima regola del firewall a livello di server. Per altre informazioni, vedere [Regole firewall a livello di server e di database per il database SQL di Azure](sql-database-firewall-configure.md).

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

Questo consente di stabilire una connessione tramite Transport Layer Security (TLS) e di ridurre il rischio di attacco man-in-the-middle . È possibile ottenere stringhe di connessione configurate correttamente per il database SQL per i driver di client supportati nel portale di Azure, come illustrato per ADO.net in questo screenshot.

1. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**.

2. Nella pagina **Panoramica** del database fare clic su **Mostra stringhe di connessione del database**.

3. Esaminare la stringa di connessione completa **ADO.NET**.

    ![Stringa di connessione ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Creazione degli utenti del database

Prima di creare gli utenti, è innanzitutto necessario scegliere uno dei due tipi di autenticazione supportati dal database SQL di Azure: 

**Autenticazione di SQL**, che usa nome utente e password per gli accessi e gli utenti che sono validi solo nel contesto di un database specifico all'interno di un server logico. 

**Autenticazione di Azure Active Directory**, che usa identità gestite da Azure Active Directory. 

Se si desidera usare [Azure Active Directory](./sql-database-aad-authentication.md) per l'autenticazione nel database SQL, deve esistere un Azure Active Directory popolato prima di continuare.

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


## <a name="protect-your-data-with-encryption"></a>Proteggere i dati con la crittografia

Transparent Data Encryption, ovvero TDE, di database SQL di Azure consente di crittografare automaticamente i dati inattivi, senza richiedere alcuna modifica all'applicazione che accede al database crittografato. Per i nuovi database creati, la crittografia TDE è attiva per impostazione predefinita. Per abilitare la crittografia TDE per il database oppure per verificare che sia attivata, seguire questa procedura:

1. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 

2. Fare clic su **Transparent Data Encryption** per aprire la pagina di configurazione di TDE.

    ![Transparent Data Encryption](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Se necessario, impostare **Crittografia dati** su Sì e fare clic su **Salva**.

Il processo di crittografia viene avviato in background. Per monitorare lo stato, connettersi al database SQL con [SQL Server Management Studio](./sql-database-connect-query-ssms.md) effettuando una query della colonna encryption_state nella visualizzazione `sys.dm_database_encryption_keys`.

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
   > 

5. Fare clic su **Salva**.

> [!IMPORTANT]
> Se si vuole personalizzare gli eventi controllati, è possibile farlo tramite l'API di PowerShell o REST: per altre informazioni, vedere la sezione [Automazione (API di PowerShell/REST)](sql-database-auditing.md#subheading-7).
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

    Se vengono rilevate attività anomale del database, si riceverà una notifica tramite posta elettronica. Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server e l'ora dell'evento. Verranno anche fornite informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database. I passaggi successivi illustrano la procedura consigliata per un utente che riceva un messaggio di posta simile al seguente:

    ![Messaggio di posta elettronica sul rilevamento delle minacce](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. Nel messaggio di posta elettronica fare clic sul collegamento al **log di controllo SQL di Azure** che avvierà il portale di Azure visualizzando i record di controllo pertinenti intorno all'ora dell'evento sospetto.

    ![Record di controllo](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Fare clic sui record di controllo per visualizzare altri dettagli sulle attività di database sospette, come l'istruzione SQL, il motivo dell'errore e l'indirizzo IP del client.

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
> * Configurare le regole del firewall per il server e/o il database
> * Connettersi al database usando una stringa di connessione protetta
> * Gestire l'accesso utente
> * Proteggere i dati con la crittografia
> * Abilitare il controllo del database SQL
> * Abilitare il rilevamento delle minacce per il database SQL

Passare all'esercitazione successiva per informazioni su come implementare un database di distribuzione geografica.

> [!div class="nextstepaction"]
>[Implementare un database con distribuzione geografica](sql-database-implement-geo-distributed-database.md)


