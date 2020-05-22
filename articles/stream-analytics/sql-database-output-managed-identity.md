---
title: Usare le identità gestite per accedere al database SQL di Azure - Analisi di flusso di Azure
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di Analisi di flusso di Azure nell'output del database SQL di Azure.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595840"
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

1. Selezionare **Salva** nella pagina **Amministratore di Active Directory**. Il processo per la modifica dell'amministratore può durare alcuni minuti.  

## <a name="create-a-database-user"></a>Creare un utente di database

Successivamente, occorre creare un utente di un database indipendente nel database SQL mappato all'identità di Azure Active Directory. L'utente di un database indipendente non ha un account di accesso per il database master, ma viene mappato a un'identità nella directory associata al database. L'identità di Azure Active Directory può essere un singolo account utente o un gruppo. In questo caso, si vuole creare un utente di un database indipendente per il processo di Analisi di flusso. 

1. Connettersi al database SQL tramite SQL Server Management Studio. Il **Nome utente** è un utente Azure Active Directory con l'autorizzazione **ALTER ANY USER**. L'amministratore impostato in SQL Server è un esempio. Usare l'autenticazione **Azure Active Directory - Universale con supporto MFA**. 

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

## <a name="grant-stream-analytics-job-permissions"></a>Concedere autorizzazioni al processo di Analisi di flusso

Il processo di Analisi di flusso dispone dell'autorizzazione concessa dall'identità gestita per la connessione alla risorsa del database SQL (**CONNECT**). Probabilmente, sarebbe più efficiente consentire al processo di Analisi di flusso di eseguire comandi come **SELECT**. È possibile concedere queste autorizzazioni al processo di Analisi di flusso usando SQL Server Management Studio. Per altre informazioni, vedere la guida di riferimento a [GRANT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15).

In alternativa, è possibile fare clic con il pulsante destro del mouse sul database SQL in SQL Server Management Studio e selezionare **Proprietà > Autorizzazioni**. Dal menu Autorizzazioni è possibile visualizzare il processo di Analisi di flusso aggiunto in precedenza e concedere o negare manualmente le autorizzazioni in base alle esigenze.

## <a name="create-an-azure-sql-database-output"></a>Creare un output del database SQL di Azure

Ora che l'identità gestita è configurata, si è pronti per aggiungere il database SQL di Azure come output al processo di Analisi di flusso.

1. Tornare al processo di Analisi di flusso e passare alla pagina **Output** in **Topologia processo**. 

1. Selezionare **Aggiungi > Database SQL**. Nella finestra delle proprietà di output del sink di output del database SQL selezionare **Identità gestita** dall'elenco a discesa Modalità di autenticazione.

1. Compilare le proprietà rimanenti. Per altre informazioni sulla creazione di un output del database SQL, vedere [Creare un output del database SQL con Analisi di flusso](stream-analytics-define-outputs.md#sql-database). Al termine dell'operazione, selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)
* [Output di Analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md)
