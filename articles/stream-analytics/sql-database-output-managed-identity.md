---
title: Usare le identità gestite per accedere al database SQL di Azure - Analisi di flusso di Azure
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di Analisi di flusso di Azure nell'output del database SQL di Azure.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 757dd7280867e9b31fdc0750fc0848de8f266770
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045621"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Usare le identità gestite per accedere al database SQL di Azure da un processo di Analisi di flusso di Azure (anteprima)

Analisi di flusso di Azure supporta l'[autenticazione con identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per i sink di output del database SQL di Azure. Le identità gestite eliminano le limitazioni associate ai metodi di autenticazione basata sull'utente, ad esempio la necessità di ripetere l'autenticazione a causa di modifiche della password o di scadenze dei token utente ogni 90 giorni. Quando si elimina la necessità di eseguire l'autenticazione manualmente, le distribuzioni di Analisi di flusso possono essere completamente automatizzate.

Un'identità gestita è un'applicazione gestita registrata in Azure Active Directory che rappresenta un determinato processo di Analisi di flusso. L'applicazione gestita viene usata per l'autenticazione in una risorsa di destinazione. Questo articolo spiega come abilitare l'identità gestita per uno o più output del database SQL di Azure di un processo di Analisi di flusso tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per questa funzionalità sono obbligatori i seguenti:

- Un processo di Analisi di flusso di Azure.

- Una risorsa del database SQL di Azure.

## <a name="create-a-managed-identity"></a>Creare un'identità gestita

Per prima cosa, si crea un'identità gestita per il processo di Analisi di flusso di Azure.

1. Nel [portale di Azure](https://portal.azure.com) aprire il processo di Analisi di flusso di Azure.

1. Nel menu di spostamento a sinistra selezionare **Identità gestita** in **Configura**. Selezionare quindi la casella accanto a **Usa identità gestita assegnata dal sistema** e selezionare **Salva**.

   ![Selezionare l'identità gestita assegnata dal sistema](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   In Azure Active Directory viene creata un'entità servizio per l'identità del processo di Analisi di flusso. Il ciclo di vita dell'identità creata viene gestito da Azure. Quando si elimina il processo di Analisi di flusso, l'identità associata (ovvero, l'entità servizio) viene eliminata automaticamente da Azure. 

1. Quando si salva la configurazione, l'ID oggetto (OID) dell'entità servizio viene indicato come ID entità, come illustrato di seguito: 

   ![ID oggetto indicato come ID entità](./media/sql-db-output-managed-identity/principal-id.png)

   L'entità servizio ha lo stesso nome del processo di Analisi di flusso. Ad esempio, se il nome del processo è *MyASAJob*, anche il nome dell'entità servizio è *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Selezionare un amministratore di Active Directory

Dopo aver creato un'identità gestita, è necessario selezionare un amministratore di Active Directory.

1. Passare alla risorsa del database SQL di Azure e selezionare il server SQL in cui si trova il database. È possibile trovare il nome del server SQL accanto a *Nome server* nella pagina di panoramica delle risorse. 

1. Selezionare **Amministratore di Active Directory** in **Impostazioni**. Selezionare quindi **Imposta amministratore**. 

   ![Pagina Amministratore di Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Nella pagina Amministratore di Active Directory cercare un utente o un gruppo da impostare come amministratore per SQL Server e fare clic su **Seleziona**.

   ![Aggiungere un amministratore di Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Per l'elenco degli amministratori supportati, vedere la sezione  **Funzionalità e limitazioni di Azure AD**  in  [Usare l'autenticazione di Azure Active Directory per l'autenticazione di un database SQL o di Azure Synapse](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). Il controllo di accesso basata sui ruoli (RBAC) si applica solo al portale e non viene propagato a SQL Server. Inoltre, l'utente o il gruppo selezionato è l'utente che sarà in grado di creare l'**Utente di database indipendente** nella sezione successiva.

1. Selezionare **Salva** nella pagina **Amministratore di Active Directory**. Il processo per la modifica dell'amministratore può durare alcuni minuti.

   Quando si configura l'amministratore di Azure AD il nuovo nome dell'amministratore, utente o gruppo, non può essere presente nel database master virtuale come un utente dell'autenticazione del server SQL. Se il nome è già presente, l'impostazione dell'amministratore di Azure AD avrà esito negativo. Verrà quindi eseguito il rollback del processo di creazione segnalando che un nome di amministratore specificato esiste già. Poiché l'utente dell'autenticazione del server SQL non è parte di Azure AD, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure AD con le credenziali di quell'utente ha esito negativo. 

## <a name="create-a-contained-database-user"></a>Creazione di un utente di database indipendente

Successivamente, occorre creare un utente di un database indipendente nel database SQL mappato all'identità di Azure Active Directory. L'utente di un database indipendente non ha un account di accesso per il database master, ma viene mappato a un'identità nella directory associata al database. L'identità di Azure Active Directory può essere un singolo account utente o un gruppo. In questo caso, si vuole creare un utente di un database indipendente per il processo di Analisi di flusso. 

1. Connettersi al database SQL utilizzando SQL Server Management Studio. Il **Nome utente** è un utente Azure Active Directory con l'autorizzazione **ALTER ANY USER**. L'amministratore impostato in SQL Server è un esempio. Usare l'autenticazione **Azure Active Directory - Universale con supporto MFA**. 

   ![Connessione a SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Il nome del server `<SQL Server name>.database.windows.net` può essere differente nelle diverse aree. Ad esempio, l'area Cina deve usare `<SQL Server name>.database.chinacloudapi.cn`.
 
   È possibile specificare un database SQL specifico selezionando **Opzioni > Proprietà connessione > Connetti al database**.  

   ![Proprietà di connessione SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Quando ci si connette per la prima volta, è possibile che venga visualizzata la finestra seguente:

   ![Finestra Nuova regola del firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. In tal caso, passare alla risorsa di SQL Server nel portale di Azure. Nella sezione **Sicurezza** aprire la pagina **Firewall e rete virtuale**. 
   1. Aggiungere una nuova regola con un nome qualsiasi.
   1. Usare l'indirizzo IP *Da* della finestra **Nuova regola del firewall** per *IP iniziale*.
   1. Usare l'indirizzo IP *A* della finestra **Nuova regola del firewall** per *IP finale*. 
   1. Selezionare **Salva** e provare di nuovo a connettersi da SQL Server Management Studio. 

1. Dopo avere stabilito la connessione, creare l'utente del database indipendente. Il comando SQL seguente crea un utente del database indipendente con lo stesso nome del processo di Analisi di flusso. Assicurarsi di includere le parentesi quadre intorno ad *ASA_JOB_NAME*. Usare la sintassi T-SQL seguente ed eseguire la query. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. Affinché Azure Active Directory di Microsoft verifichi se il processo di Analisi di flusso ha accesso al database SQL, è necessario concedere ad Azure Active Directory autorizzazione per la comunicazione con il database. A tale scopo, passare alla pagina "firewall e rete virtuale" in portale di Azure di nuovo e abilitare "Consenti ai servizi e alle risorse di Azure di accedere a questo server". 

   ![Firewall e rete virtuale](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Concedere autorizzazioni al processo di Analisi di flusso

Dopo aver creato un utente del database indipendente e dato accesso ai servizi di Azure nel portale, come descritto nella sezione precedente, il processo di Analisi di flusso dispone dell'autorizzazione dall'identità gestita per connettersi **(CONNECT)** alla risorsa del Database SQL tramite identità gestita. Si consiglia di concedere le autorizzazioni SELECT e INSERT al processo di Analisi di flusso, perché saranno necessarie in un secondo momento nel flusso di lavoro di Analisi di flusso. L'autorizzazione **SELECT** consente al processo di testare la connessione alla tabella nel database SQL. L'autorizzazione **INSERT** consente di testare le query di Analisi di flusso end-to-end dopo aver configurato un input e l'output del database SQL. È possibile concedere queste autorizzazioni al processo di Analisi di flusso usando SQL Server Management Studio. Per altre informazioni, vedere la guida di riferimento a [GRANT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15).

Per concedere solo l'autorizzazione a una determinata tabella od oggetto nel database, utilizzare la sintassi T-SQL seguente ed eseguire la query. 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

In alternativa, è possibile fare clic con il pulsante destro del mouse sul database SQL in SQL Server Management Studio e selezionare **Proprietà > Autorizzazioni**. Dal menu Autorizzazioni è possibile visualizzare il processo di Analisi di flusso aggiunto in precedenza e concedere o negare manualmente le autorizzazioni in base alle esigenze.

## <a name="create-an-azure-sql-database-output"></a>Creare un output del database SQL di Azure

Ora che l'identità gestita è configurata, si è pronti per aggiungere il database SQL di Azure come output al processo di Analisi di flusso.

Assicurarsi di aver creato una tabella nel database SQL con lo schema di output appropriato. Il nome di questa tabella è una delle proprietà obbligatorie da compilare quando si aggiunge l'output del database SQL al processo di Analisi di flusso. Assicurarsi anche che il processo abbia le autorizzazioni **SELECT** e **INSERT** per testare la connessione ed eseguire query di Analisi di flusso. Vedere la sezione [Concedere autorizzazioni al processo di Analisi di flusso](#grant-stream-analytics-job-permissions) se non è già stato fatto. 

1. Tornare al processo di Analisi di flusso e passare alla pagina **Output** in **Topologia processo**. 

1. Selezionare **Aggiungi > Database SQL**. Nella finestra delle proprietà di output del sink di output del database SQL selezionare **Identità gestita** dall'elenco a discesa Modalità di autenticazione.

1. Compilare le proprietà rimanenti. Per altre informazioni sulla creazione di un output del database SQL, vedere [Creare un output del database SQL con Analisi di flusso](stream-analytics-define-outputs.md#sql-database). Al termine dell'operazione, selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)
* [Output di Analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md)
