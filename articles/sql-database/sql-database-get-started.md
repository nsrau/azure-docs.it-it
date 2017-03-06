---
title: 'Guida introduttiva: il primo database SQL di Azure | Microsoft Docs'
description: Informazioni su come creare un server logico di database SQL, una regola del firewall a livello di server e database nel portale di Azure. Viene illustrato anche come usare SQL Server Management Studio con il database SQL di Azure.
keywords: esercitazione sul database sql, creare un database sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3
ms.lasthandoff: 02/18/2017


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Creare i primi database SQL di Azure, connettersi ed eseguire query nel portale di Azure e con SSMS

Questa esercitazione descrive come creare database SQL di Azure, connettersi e eseguire query nel portale di Azure e con SQL Server Management Studio. Al termine di questa esercitazione:

* Si sarà creato un gruppo di risorse contenente un server logico, una regola del firewall a livello di server e due database.
* Si sarà appreso come visualizzare le proprietà del server e dei database nel portale di Azure e con SQL Server Management Studio.
* Si sarà appreso come eseguire query su un database nel portale di Azure e con SQL Server Management Studio.

**Tempo stimato**: per questa esercitazione sono necessari circa 30 minuti, presupponendo che i prerequisiti siano già soddisfatti.

> [!TIP]
> Si può anche apprendere come creare un database SQL di Azure, connettersi ed eseguire query con [PowerShell](sql-database-get-started-powershell.md) o [C#](sql-database-get-started-csharp.md).
>

> [!NOTE]
> Questa esercitazione offre informazioni sul contenuto di questi argomenti: [panoramica del server del database SQL](sql-database-server-overview.md), [panoramica del database SQL](sql-database-overview.md) e [panoramica delle regole del firewall del database SQL di Azure](sql-database-firewall-configure.md). Per una panoramica del servizio di database SQL, vedere [Informazioni sul database SQL](sql-database-technical-overview.md).
>  

## <a name="prerequisites"></a>Prerequisiti

* **Account Azure**. È possibile [aprire un account Azure gratuito](https://azure.microsoft.com/free/) o [attivare i benefici della sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Autorizzazioni di creazione di Azure**. È necessario essere in grado di connettersi al portale di Azure con un account membro del ruolo proprietario o collaboratore della sottoscrizione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione alla gestione degli accessi nel portale di Azure](../active-directory/role-based-access-control-what-is.md).

* **SQL Server Management Studio**. Per scaricare e installare l'ultima versione di SQL Server Management Studio (SSMS), vedere [Scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Per la connessione al database SQL di Azure usare sempre l'ultima versione di SSMS, perché vengono continuamente rilasciate nuove funzionalità.

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

I passaggi di questa procedura illustrano come connettersi al portale di Azure con l'[account Azure](https://account.windowsazure.com/Home/Index).

1. Aprire il browser preferito e connettersi al [portale di Azure](https://portal.azure.com/).
2. Accedere al [portale di Azure](https://portal.azure.com/).
3. Nella **pagina di accesso** specificare le credenziali per la sottoscrizione.
   
   ![pagina di accesso](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Creare un nuovo server logico di SQL Server

I passaggi di questa procedura illustrano come creare un server logico nell'area scelta nel portale di Azure. Un server logico è l'oggetto in cui si creano i database SQL e in cui si creano le regole del firewall per consentire agli utenti la connessione tramite il firewall del database SQL di Azure. 

1. Fare clic su **Nuovo**, digitare **sql server** e quindi fare clic su **INVIO**.

    ![server logico di sql](./media/sql-database-get-started/logical-sql-server.png)
2. Fare clic su **SQL Server (server logico)**.
   
    ![crea - server logico di sql](./media/sql-database-get-started/create-logical-sql-server.png)
3. Fare clic su **Crea** per aprire il nuovo pannello SQL Server (solo server logico).

    ![nuovo - server logico di sql](./media/sql-database-get-started/new-logical-sql-server.png)
3. Nella casella di testo **Nome server** specificare un nome valido per il nuovo server logico. Un segno di spunta verde indica che è stato specificato un nome valido.
    
    ![nuovo nome server](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Il nome completo del nuovo server deve essere univoco a livello globale e nel formato **<nome_server>.database.windows.net**. Questo nome completo del server verrà usato più avanti in questa esercitazione per connettersi al server e ai database.
    >
    
4. Nella casella di testo **Account di accesso amministratore server** specificare un nome utente per l'account di accesso con autenticazione SQL per questo server. Questo account è denominato account di accesso dell'entità server. Un segno di spunta verde indica che è stato specificato un nome valido.
    
    ![account di accesso amministratore di sql](./media/sql-database-get-started/sql-admin-login.png)
5. Nelle caselle di testo **Password** e **Conferma password** specificare una password per l'account di accesso dell'entità server. Un segno di spunta verde indica che è stata specificata una password valida.
    
    ![password amministratore di sql](./media/sql-database-get-started/sql-admin-password.png)
6. Nella casella di riepilogo a discesa **Sottoscrizione** selezionare una sottoscrizione in cui si ha l'autorizzazione per creare oggetti.

    ![sottoscrizione](./media/sql-database-get-started/subscription.png)
7. Nella casella di testo **Gruppo di risorse** selezionare **Crea nuovo** e quindi specificare un nome valido per il nuovo gruppo di risorse. Un segno di spunta verde indica che è stato specificato un nome valido.

    ![nuovo gruppo di risorse](./media/sql-database-get-started/new-resource-group.png)

8. Nella casella di testo **Località** selezionare un data center in cui creare il server logico.
    
    ![località del server](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > La casella di controllo **Consenti ai servizi di Azure di accedere al server** non può essere modificata in questo pannello. È possibile modificare questa impostazione nel pannello firewall del server. Per altre informazioni, vedere [Get started with security](sql-database-control-access-sql-authentication-get-started.md) (Introduzione alla sicurezza).
    >
    
9. Selezionare la casella di controllo **Aggiungi al dashboard**.

10. Fare clic su **Crea** per distribuire lo script in Azure per creare il server logico.

    ![Pulsante Crea](./media/sql-database-get-started/create.png)

11. Al termine della creazione del server, esaminarne le proprietà che vengono visualizzate per impostazione predefinita. 

    ![pannello di sql server](./media/sql-database-get-started/sql-server-blade.png)
12. Fare clic su **Proprietà** per visualizzare proprietà aggiuntive del server logico di SQL.

    ![proprietà di sql server](./media/sql-database-get-started/sql-server-properties.png)
13. Copiare il nome completo del server negli Appunti per usarli più avanti in questa esercitazione.

    ![nome completo del server sql](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

I passaggi di questa procedura illustrano come creare una regola del firewall a livello di server nel portale di Azure. Per impostazione predefinita, un firewall di database SQL di Azure impedisce la connettività esterna al server logico e ai relativi database. Per potersi connettere al server, è necessario creare una regola del firewall per l'indirizzo IP del computer da cui ci si connette nelle procedura successiva. Per altre informazioni, vedere [Panoramica sulle regole del firewall per il database SQL di Azure](sql-database-firewall-configure.md).

1. Nel pannello SQL Server fare clic su **Firewall** per aprire il pannello del firewall per il server. Si noti che viene visualizzato l'indirizzo IP per il computer client.

    ![Firewall di SQL server](./media/sql-database-get-started/sql-server-firewall.png)

2. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per creare una regola del firewall per l'indirizzo IP corrente.

    ![Aggiungi IP client](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > È possibile creare una regola del firewall per un singolo indirizzo IP o per un intero intervallo di indirizzi. L'apertura del firewall consente ad amministratori e utenti SQL di accedere a qualsiasi database nel server per cui hanno credenziali valide.
    >

4. Fare clic su **Salva** sulla barra degli strumenti per salvare la regola del firewall a livello di server e quindi su **OK** per chiudere la finestra di dialogo Operazione riuscita.

    ![esito positivo](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Connettersi al server con SSMS

I passaggi di questa procedura illustrano come connettersi al server logico di SQL con SQL Server Management Studio. SSMS è il principale strumento usato dagli amministratori di database per gestire server e database SQL.

1. Aprire SQL Server Management Studio. A tale scopo, digitare **Microsoft SQL Server Management Studio** nella casella di ricerca di Windows e premere **INVIO**.

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Nella finestra di dialogo **Connetti al server** immettere il nome completo del server della procedura precedente, selezionare Autenticazione di SQL Server e quindi inserire l'account di accesso e la password specificati durante il provisioning del server.

    ![connetti al server](./media/sql-database-get-started/connect-to-server.png)
4. Fare clic su **Connetti** per avviare la connessione e aprire Esplora oggetti in SSMS.

    ![connesso al server](./media/sql-database-get-started/connected-to-server.png)
5. In Esplora oggetti espandere **Database**, **Database di sistema**, **Master** per visualizzare gli oggetti nel database master.

    ![database master](./media/sql-database-get-started/master-database.png)
6. Fare clic con il pulsante destro del mouse su **Master** e quindi scegliere **Nuova query**.

    ![eseguire query nel database master](./media/sql-database-get-started/query-master-database.png)

8. Nella finestra della query digitare la query seguente:

   ```select * from sys.objects```

9.  Sulla barra degli strumenti fare clic su **Execute** (Esegui) per restituire un elenco degli oggetti di sistema nel database master.

    ![eseguire query su oggetti di sistema del database master](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Per iniziare a usare le funzionalità di sicurezza di SQL, vedere l'[introduzione all'autenticazione SQL](sql-database-control-access-sql-authentication-get-started.md).
    >

## <a name="create-a-database-with-sample-data"></a>Creare un database con dati di esempio

I passaggi di questa procedura illustrano come creare un database con dati di esempio associato al server logico creato in precedenza nel portale di Azure. 

1. Nel portale di Azure fare clic su **Database SQL** nel pannello predefinito.

    ![database sql](./media/sql-database-get-started/new-sql-database.png)
2. Nel pannello dei database SQL fare clic su **Aggiungi**. 

    ![aggiungi database sql](./media/sql-database-get-started/add-sql-database.png)

    ![pannello database sql](./media/sql-database-get-started/sql-database-blade.png)
3. Nella casella di testo **Nome database** specificare un nome di database valido.

    ![nome del database sql](./media/sql-database-get-started/sql-database-name.png)
4. In **Selezionare l'origine** selezionare **Sample (AdventureWorksLT)**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
5. In **Server** verificare che sia selezionato il proprio server. Quando si aggiunge un database a un server, è possibile aggiungerlo come database singolo (impostazione predefinita) oppure aggiungerlo a un pool elastico. Per altre informazioni sui pool elastici, vedere [Elastic pools](sql-database-elastic-pool.md) (Pool elastici).

6. In **Piano tariffario** modificare il piano tariffario in **Basic** e fare clic su **Seleziona**. È possibile incrementare il piano tariffario in un secondo momento, se necessario, ma ai fini dell'esercitazione è consigliabile usare il piano a costo più basso.

    ![piano tariffario](./media/sql-database-get-started/pricing-tier.png)
7. Selezionare la casella di controllo **Aggiungi al dashboard** e quindi fare clic su **Crea**.

    ![Pulsante Crea](./media/sql-database-get-started/create.png)

8. Al termine della creazione del database, visualizzarne le proprietà nel portale di Azure. Le esercitazioni successive consentiranno di comprendere le opzioni disponibili nel pannello. 

    ![nuovo pannello del database di esempio](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Eseguire query nel database nel portale di Azure

I passaggi di questa procedura illustrano come eseguire query sul database direttamente nel portale di Azure. 

1. Nel pannello del database SQL fare clic su **Strumenti** sulla barra degli strumenti.

    ![strumenti](./media/sql-database-get-started/tools.png)
2. Nel pannello Strumenti fare clic su **Editor di query (anteprima)**.

    ![Editor di query](./media/sql-database-get-started/query-editor.png)
3. Selezionare la casella di controllo per confermare che l'Editor di query è una funzionalità disponibile in anteprima, quindi fare clic su **OK**.
4. Nel pannello **Editor di query** fare clic su **Accedi**.

    ![Pannello Editor di query](./media/sql-database-get-started/query-editor-blade.png)
5. Verificare i valori per il tipo di autorizzazione e per l'accesso, quindi specificare la password per l'accesso. 

    ![Accesso all'Editor di query](./media/sql-database-get-started/query-editor-login.png)
6. Fare clic su **OK** per provare ad accedere.
7. Dopo l'autenticazione, nella finestra di query digitare la query seguente e quindi fare clic su **Esegui**.

   ```select * from sys.objects```

    ![Query dell'Editor di query](./media/sql-database-get-started/query-editor-query.png)

8. Esaminare i risultati della query nel riquadro **Risultati**.

    ![Risultati dell'Editor di query](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>Eseguire query sul database con SSMS

I passaggi di questa procedura illustrano come connettersi al database con SQL Server Management Studio e quindi eseguire query sui dati di esempio per visualizzare gli oggetti nel database.

1. Passare ad SQL Server Management Studio e, in Esplora oggetti, fare clic su **Database** e quindi su **Aggiorna** sulla barra degli strumenti per visualizzare il database di esempio.

    ![nuovo database di esempio con ssms](./media/sql-database-get-started/new-sample-db-ssms.png)
2. In Esplora oggetti espandere il nuovo database per visualizzarne gli oggetti.

    ![nuovi oggetti del database di esempio con ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Fare clic con il pulsante destro del mouse sul database di esempio e quindi fare clic su **Nuova query**.

    ![nuova query del database di esempio con ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. Nella finestra della query digitare la query seguente:

   ```select * from sys.objects```
   
9.  Sulla barra degli strumenti fare clic su **Execute** (Esegui) per restituire un elenco degli oggetti di sistema nel database di esempio.

    ![nuovi oggetti del sistema per l'esecuzione di query sul database di esempio con ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>Creare un database vuoto con SSMS

I passaggi di questa procedura illustrano come creare un nuovo database con SQL Server Management Studio.

1. In Esplora oggetti fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**.

    ![nuovo database vuoto con ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

2. Nella finestra di dialogo **Nuovo database** specificare un nome di database nella casella di testo Nome database. 

    ![nuovo nome del database vuoto con ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. Nella finestra di dialogo Nuovo database fare clic su **Opzioni** e quindi modificare l'edizione in **Basic**.

    ![nuove opzioni del database vuoto con ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Esaminare le altre opzioni nella finestra di dialogo che è possibile modificare per un database SQL di Azure. Per altre informazioni su queste opzioni, vedere [Create Database](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Fare clic su **OK** per creare il database vuoto.
5. Al termine, aggiornare il nodo del database in Esplora oggetti per visualizzare il database vuoto appena creato. 

    ![nuovo database vuoto in esplora oggetti](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Risolvere i problemi di connettività

Quando la connessione al database SQL di Azure non riesce, vengono visualizzati messaggi di errore. I problemi di connessione possono essere causati dalla riconfigurazione del database SQL di Azure, dalle impostazioni del firewall, dal timeout della connessione o da informazioni di accesso non corrette. Per uno strumento di risoluzione dei problemi di connettività, vedere [Troubleshooting connectivity issues with Microsoft Azure SQL Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) (Risoluzione dei problemi di connettività con il database SQL di Microsoft Azure).

## <a name="delete-a-single-database-in-the-azure-portal"></a>Eliminare un database singolo nel portale di Azure

I passaggi di questa procedura illustrano come eliminare un database singolo con il portale di Azure.

1. Nel pannello dei database SQL nel portale di Azure fare clic sul database che si vuole eliminare. 
2.  Fare clic su **Elimina** per tale database SQL.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. Fare clic su **Sì** per confermare che si vuole eliminare definitivamente questo database.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Durante il periodo di conservazione del database è possibile ripristinarlo dai backup automatici avviati dal servizio, se non si elimina il server. È possibile ripristinare i database dell'edizione Basic in sette giorni. Per tutte le altre edizioni, è possibile ripristinare i database entro 35 giorni. Se si elimina il server, non è possibile ripristinare il server o i relativi database eliminati. Per altre informazioni sui backup dei database, vedere [Informazioni sul backup del database SQL](sql-database-automated-backups.md) e per informazioni sul ripristino di un database dai backup, vedere [Ripristino del database](sql-database-recovery-using-backups.md). Per informazioni pratiche sul ripristino di un database eliminato, vedere [Ripristinare un database SQL di Azure con il portale di Azure](sql-database-restore-deleted-database-portal.md).
>


## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa esercitazione, sono disponibili numerose altre esercitazioni che può essere opportuno consultare per mettere in pratica i concetti appresi in questa esercitazione. 

- Per un'introduzione all'esercitazione sull'autenticazione di SQL Server, vedere [SQL authentication and authorization](sql-database-control-access-sql-authentication-get-started.md) (Autenticazione e autorizzazione di SQL).
- Per un'introduzione all'esercitazione sull'autenticazione di Azure Active Directory, vedere [AAD authentication and authorization](sql-database-control-access-aad-authentication-get-started.md) (Autenticazione e autorizzazione di AAD).
* Per eseguire query sul database di esempio nel portale di Azure, vedere [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/) (Anteprima pubblica: esperienza di query interattiva per i database SQL).
* Se si ha familiarità con Excel, vedere l'esercitazione [Connettere Excel a un database SQL di Azure e creare un report](sql-database-connect-excel.md).
* Se si è pronti per iniziare a scrivere codice, scegliere il linguaggio di programmazione in [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).
* Per spostare i database SQL Server locali in Azure, vedere [Migrating a database to SQL Database](sql-database-cloud-migrate.md) (Migrazione di un database al database SQL).
* Per caricare alcuni dati in una nuova tabella da un file CSV con lo strumento da riga di comando BCP, vedere [Caricamento di dati in un database SQL da un file CSV con BCP](sql-database-load-from-csv-with-bcp.md).
* Per iniziare a creare tabelle e altri oggetti, vedere l'argomento "Per creare una tabella" in [Esercitazione per la creazione di una tabella](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive

- Per una panoramica tecnica, vedere [Informazioni sul database SQL](sql-database-technical-overview.md).
- Per informazioni sui prezzi, vedere [Database SQL Azure - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).


