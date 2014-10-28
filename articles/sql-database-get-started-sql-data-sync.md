<properties linkid="manage-services-sql-databases-datasync" urlDisplayName="How to sync data" pageTitle="Getting started with SQL Databases Data Sync" metaKeywords="" description="" metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Data Sync" authors="spelleru" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelleru"></tags>

# Introduzione alla sincronizzazione dati SQL di Azure

In questa esercitazione vengono fornite le nozioni di base della sincronizzazione dati SQL Azure tramite il portale di Azure (anteprima).

Per questa esercitazione è necessario conoscere già in generale SQL Server e il database SQL di Azure. Nell'esercitazione verrà creato un gruppo di sincronizzazione ibrido (istanze del database SQL Server e del database SQL) completamente configurato e impostato per la sincronizzazione in base alla pianificazione specificata.

## Sommario

-   [Passaggio 1: Connettersi al database SQL di Azure][]
-   [Passaggio 2: Aggiungere un agente client][]
-   [Passaggio 3: Registrare un database di SQL Server con l'agente client][]
-   [Passaggio 4: Creare un gruppo di sincronizzazione][]
-   [Passaggio 5: Definire i dati da sincronizzare][]
-   [Passaggio 6: Configurare il gruppo di sincronizzazione][]

## <span id="Connect"></span></a>Passaggio 1: Connettersi al database SQL di Azure

1.  Accedere al [portale di gestione][].

2.  Nel riquadro sinistro fare clic su **SQL DATABASES**.

3.  Fare clic su **SYNC** nella parte inferiore della pagina. Quando si fa clic su SYNC, viene visualizzato un elenco degli elementi che è possibile aggiungere: **New Sync Group** e **New Sync Agent**.

4.  Per avviare la procedura guidata New SQL Data Sync Agent, fare clic su **New Sync Agent**.

5.  Se l'agente non è stato aggiunto in precedenza, **fare clic qui per scaricarlo**.

    ![Immagine1][]

## <span id="AddAgent"></span></a>Passaggio 2: Aggiungere un agente client

Questo passaggio è necessario solo se il gruppo di sincronizzazione includerà un database di SQL Server locale. In caso contrario, è possibile andare al passaggio 4: Creare un gruppo di sincronizzazione, se il gruppo ha solo istanze del database SQL.

### <span id="InstallRequiredSoftware"></span></a>Passaggio 2a: Installare il software necessario

Nel computer in cui viene installato l'agente client devono essere installati anche i seguenti prodotti.

-   **.NET Framework 4.0**

È possibile installare .NET Framework 4.0 da [qui][].

-   **Tipi CLR di sistema di Microsoft SQL Server 2008 R2 SP1 (x86)**

È possibile installare i tipi CLR di sistema di Microsoft SQL Server 2008 R2 SP1 (x86) da [qui][1]

-   **Oggetti di gestione condivisa di Microsoft SQL Server 2008 R2 SP1 (x86)**

È possibile installare gli oggetti di gestione condivisa di Microsoft SQL Server 2008 R2 SP1 (x86) da [qui][1]

### <span id="InstallClient"></span></a>Passaggio 2b: Installare un nuovo agente client

Per installare l'agente, seguire le istruzioni disponibili in [Installare un agente client di sincronizzazione dati SQL][].

### <span id="RegisterSSDb"></span></a>Passaggio 2c: Completare la procedura guidata New SQL Data Sync Agent

1.  Tornare alla procedura guidata New SQL Data Sync Agent.
2.  Specificare un nome significativo per l'agente.
3.  Nell'elenco a discesa **REGION** selezionare il data center per ospitare l'agente.
4.  Nell'elenco a discesa **SUBSCRIPTION** selezionare la sottoscrizione per ospitare l'agente.
5.  Fare clic sulla freccia destra.

## <span id="RegisterSSDB"></span></a>Passaggio 3: Registrare un database di SQL Server con l'agente client

Dopo aver installato l'agente client, registrare con l'agente tutti i database di SQL Server locali che si desidera includere in un gruppo di sincronizzazione.
Per registrare un database con l'agente, seguire le istruzioni in [Registrare un database SQL Server con un agente client][].

## <span id="CreateSG"></span></a>Passaggio 4: Creare un gruppo di sincronizzazione

### <span id="StartNewSGWizard"></span></a>Passaggio 4a: Avviare la procedura guidata New Sync Group

1.  Tornare al [portale di gestione][].
2.  Fare clic su **SQL DATABASES**.
3.  Fare clic su **ADD SYNC** in fondo alla pagina, quindi selezionare New Sync Group.

    ![Immagine2][]

### <span></span></a>Passaggio 4b: Specificare le impostazioni di base

1.  Immettere un nome significativo per il gruppo di sincronizzazione.
2.  Nell'elenco a discesa **REGION** selezionare il data center per ospitare il gruppo di sincronizzazione.
3.  Fare clic sulla freccia destra.

    ![Immagine3][]

### <span id="DefineHubDB"></span></a>Passaggio 4c: Definire l'hub di sincronizzazione

1.  Nell'elenco a discesa selezionare l'istanza di database SQL da utilizzare come hub per il gruppo di sincronizzazione.
2.  Immettere le credenziali per l'istanza di database SQL in **HUB USERNAME** e **HUB PASSWORD**.
3.  Attendere che la sincronizzazione dati SQL verifichi il nome utente e la password. Quando le credenziali risultano verificate, viene visualizzato un segno di spunta verde a destra del campo PASSWORD.
4.  Nell'elenco a discesa **CONFLICT RESOLUTION** selezionare i criteri di risoluzione dei conflitti.

**Hub Wins** indica che tutte le modifiche scritte nel database hub vengono scritte nei database di riferimento, sovrascrivendo le modifiche nello stesso record del database di riferimento. Dal punto di vista funzionale, questo significa che la prima modifica scritta nell'hub viene propagata negli altri database.

**Client Wins** indica che le modifiche scritte nell'hub vengono sovrascritte dalle modifiche presenti nei database di riferimento. Dal punto di vista funzionale, questo significa che l'ultima modifica scritta nell'hub corrisponde a quella conservata e propagata negli altri database.

1.  Fare clic sulla freccia destra.

    ![Immagine4][]

### <span id="AddRefDB"></span></a>Passaggio 4d: Aggiungere un database di riferimento

Ripetere questo passaggio per ogni ulteriore database da aggiungere al gruppo di sincronizzazione.

1.  Selezionare il database da aggiungere nell'elenco a discesa.

    I database elencati sono i database di SQL Server registrati con l'agente e le istanze del database SQL.

2.  Immettere le credenziali per il database in **USERNAME** e **PASSWORD**.
3.  Nell'elenco a discesa **SYNC DIRECTION** selezionare la direzione di sincronizzazione per il database.

    **Bi-directional** indica che le modifiche nel database di riferimento vengono scritte nel database hub e che le modifiche apportate al database hub vengono scritte nel database di riferimento.

    **Sync from the Hub** indica che il database riceve gli aggiornamenti dall'hub. Le modifiche non vengono inviate all'hub.

    **Sync to the Hub** indica che il database invia gli aggiornamenti all'hub. Le modifiche apportate all'hub non vengono scritte in questo database.

4.  Per completare la creazione del gruppo di sincronizzazione, fare clic sul segno di spunta nella parte inferiore destra della procedura guidata. Attendere che la sincronizzazione dati SQL verifichi le credenziali. Un segno di spunta verde indica che le credenziali sono state verificate.

5.  Fare clic sul segno di spunta una seconda volta. Si tornerà alla pagina **SYNC** in SQL Databases. Il gruppo di sincronizzazione è ora elencato con gli altri gruppi di sincronizzazione e con gli agenti.

    ![Immagine5][]

## <span id="SyncRules"></span></a>Passaggio 5: Definire i dati da sincronizzare

La sincronizzazione dati SQL di Azure consente di selezionare le tabelle e le colonne da sincronizzare. Se si desidera inoltre filtrare una colonna in modo che vengano sincronizzate solo le righe con valori specifici (ad esempio Età\>=65), utilizzare il portale di sincronizzazione dei dati SQL in Azure e la documentazione relativa alla selezione di tabelle, colonne e righe da sincronizzare per definire quali dati sincronizzare.

1.  Tornare al [portale di gestione][].
2.  Fare clic su **SQL DATABASES**.
3.  Fare clic sulla scheda **SYNC**.
4.  Fare clic sul nome del gruppo di sincronizzazione.
5.  Fare clic sulla scheda **SYNC RULES**.
6.  Selezionare il database da specificare per lo schema del gruppo di sincronizzazione.
7.  Fare clic sulla freccia destra.
8.  Fare clic su **REFRESH SCHEMA**.
9.  Per ogni tabella del database, selezionare le colonne che si desidera includere nelle sincronizzazioni.

    -   Le colonne con tipi di dati non supportati non possono essere selezionate.
    -   Se in una tabella non sono selezionate colonne, la tabella non viene inclusa nel gruppo di sincronizzazione.
    -   Per selezionare o deselezionare tutte le tabelle, fare clic su SELECT in fondo alla schermata.

10. Fare clic su **SAVE**, quindi attendere il completamento del provisioning del gruppo di sincronizzazione.
11. Per tornare alla pagina iniziale di sincronizzazione dati, fare clic sulla freccia Indietro nella parte superiore sinistra della schermata (sopra al nome del gruppo di sincronizzazione).

    ![Immagine6][]

## <span id="Configure"></span></a>Passaggio 6: Configurare il gruppo di sincronizzazione

È sempre possibile sincronizzare un gruppo di sincronizzazione facendo clic su SYNC in fondo alla pagina di destinazione della sincronizzazione dati.
Se si desidera sincronizzare un gruppo di sincronizzazione in base a una pianificazione, configurarlo.

1.  Tornare al [portale di gestione][].
2.  Fare clic su **SQL DATABASES**.
3.  Fare clic sulla scheda **SYNC**.
4.  Fare clic sul nome del gruppo di sincronizzazione.
5.  Fare clic sulla scheda **CONFIGURE**.
6.  **AUTOMATIC SYNC**

    -   Per configurare il gruppo di sincronizzazione in modo che venga sincronizzato con una determinata frequenza, fare clic su **ON**. È comunque possibile eseguire la sincronizzazione su richiesta facendo clic su SYNC.
    -   Fare clic su **OFF** per configurare la sincronizzazione del gruppo solo quando si fa clic su SYNC.

7.  **SYNC FREQUENCY**

    -   Se AUTOMATIC SYNC è impostato su ON, impostare la frequenza per la sincronizzazione. La frequenza deve essere compresa tra 5 minuti e 1 mese.

8.  Fare clic su **SAVE**.

![Immagine7][]

A questo punto è stato creato un gruppo di sincronizzazione che include sia un'istanza di database SQL che un database di SQL Server.

## <span id="NextSteps"></span></a>Passaggi successivi

Per ulteriori informazioni sul database SQL e la sincronizzazione dati SQL, vedere:

-   [Iscrizione per l'offerta Premium per il database SQL] (../sign-up-for-sql-database-premium/)
-   [Sincronizzazione dati SQL (portale di Azure)][]
-   [Introduzione al database SQL di Azure][]
-   [Ciclo di vita del database di SQL Server][]

  [Passaggio 1: Connettersi al database SQL di Azure]: #Connect
  [Passaggio 2: Aggiungere un agente client]: #AddAgent
  [Passaggio 3: Registrare un database di SQL Server con l'agente client]: #RegisterSSDB
  [Passaggio 4: Creare un gruppo di sincronizzazione]: #CreateSG
  [Passaggio 5: Definire i dati da sincronizzare]: #SyncRules
  [Passaggio 6: Configurare il gruppo di sincronizzazione]: #Configure
  [portale di gestione]: http://manage.windowsazure.com
  [Immagine1]: ./media/sql-database-get-started-data-sync/SQLDatabaseScreen-Figure1.PNG
  [qui]: http://go.microsoft.com/fwlink/?linkid=205836
  [1]: http://www.microsoft.com/download/en/details.aspx?id=26728
  [Installare un agente client di sincronizzazione dati SQL]: http://msdn.microsoft.com/it-it/library/jj823137.aspx
  [Registrare un database SQL Server con un agente client]: http://msdn.microsoft.com/it-it/library/jj823138.aspx
  [Immagine2]: ./media/sql-database-get-started-data-sync/NewSyncGroup-Figure2.png
  [Immagine3]: ./media/sql-database-get-started-data-sync/NewSyncGroupName-Figure3.PNG
  [Immagine4]: ./media/sql-database-get-started-data-sync/NewSyncGroupHub-Figure4.PNG
  [Immagine5]: ./media/sql-database-get-started-data-sync/NewSyncGroupReference-Figure5.PNG
  [Immagine6]: ./media/sql-database-get-started-data-sync/NewSyncGroupSyncRules-Figure6.PNG
  [Immagine7]: ./media/sql-database-get-started-data-sync/NewSyncGroupConfigure-Figure7.PNG
  [Sincronizzazione dati SQL (portale di Azure)]: http://msdn.microsoft.com/it-it/library/windowsazure/jj856263.aspx
  [Introduzione al database SQL di Azure]: ../getting-started-w-sql-databases/
  [Ciclo di vita del database di SQL Server]: http://go.microsoft.com/fwlink/?LinkId=275193
