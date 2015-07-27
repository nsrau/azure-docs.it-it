<properties
	pageTitle="Introduzione alla sincronizzazione dati dei database SQL"
	description="Questa esercitazione fornisce informazioni per iniziare a usare anteprima di sincronizzazione dati di SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="spelluru"/>


#Introduzione all'anteprima di sincronizzazione dati di SQL Azure
In questa esercitazione vengono fornite le nozioni di base della sincronizzazione dati SQL Azure tramite il portale di Azure (anteprima).


Per questa esercitazione è necessario conoscere già in generale SQL Server e il database SQL di Azure. Nell'esercitazione verrà creato un gruppo di sincronizzazione ibrido (istanze del database SQL Server e del database SQL) completamente configurato e impostato per la sincronizzazione in base alla pianificazione specificata.


## Passaggio 1: connettersi al database SQL di Azure

1. Accedere al [portale di gestione](http://manage.windowsazure.com).

2. Nel riquadro sinistro fare clic su **SQL DATABASES**.

3. Fare clic su **SYNC** nella parte inferiore della pagina. Quando si fa clic su SYNC, viene visualizzato un elenco degli elementi che è possibile aggiungere: **New Sync Group** e **New Sync Agent**.

4. Per avviare la procedura guidata New SQL Data Sync Agent, fare clic su **New Sync Agent**.

5. Se l'agente non è stato aggiunto in precedenza, **fare clic qui per scaricarlo**.

	![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## Passaggio 2: aggiungere un agente client
Questo passaggio è necessario solo se il gruppo di sincronizzazione includerà un database di SQL Server locale. È possibile andare al passaggio 4: creare un gruppo di sincronizzazione, se il gruppo ha solo istanze del database SQL.

<a id="InstallRequiredSoftware"></a>
### Passaggio 2a: installare il software necessario
Nel computer in cui viene installato l'agente client devono essere installati anche i seguenti prodotti.

- **.NET Framework 4.0**

 È possibile installare .NET Framework 4.0 da [qui](http://go.microsoft.com/fwlink/?linkid=205836).

- **Tipi CLR di sistema di Microsoft SQL Server 2008 R2 SP1 (x86)**

 È possibile installare i tipi CLR di sistema di Microsoft SQL Server 2008 R2 SP1 (x86) da [qui](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Oggetti di gestione condivisa di Microsoft SQL Server 2008 R2 SP1 (x86)**

 È possibile installare gli oggetti di gestione condivisa di Microsoft SQL Server 2008 R2 SP1 (x86) da [qui](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### Passaggio 2b: installare un nuovo agente client

Per installare l'agente, seguire le istruzioni disponibili in [Installare un agente client di sincronizzazione dati SQL](http://msdn.microsoft.com/library/jj823137.aspx).



<a id="RegisterSSDb"></a>
### Passaggio 2c: completare la procedura guidata Nuovo agente di sincronizzazione dati SQL

1. 	Tornare alla procedura guidata New SQL Data Sync Agent.
2.	Specificare un nome significativo per l'agente.
3.	Nell'elenco a discesa **REGION** selezionare il data center per ospitare l'agente.
4.	Nell'elenco a discesa **SUBSCRIPTION** selezionare la sottoscrizione per ospitare l'agente.
5.	Fare clic sulla freccia destra.



## Passaggio 3: registrare un database di SQL Server con l'agente client

Dopo aver installato l'agente client, registrare con l'agente tutti i database di SQL Server locali che si desidera includere in un gruppo di sincronizzazione. Per registrare un database con l'agente, seguire le istruzioni in [Registrare un database SQL Server con un agente client](http://msdn.microsoft.com/library/jj823138.aspx).



## Passaggio 4: creare un gruppo di sincronizzazione


<a id="StartNewSGWizard"></a>
### Passaggio 4a: avviare la procedura guidata Nuovo gruppo di sincronizzazione

1.	Tornare al [portale di gestione](http://manage.windowsazure.com).
2.	Fare clic su **SQL DATABASES**.
3.	Fare clic su **ADD SYNC** in fondo alla pagina, quindi selezionare New Sync Group.

	![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### Passaggio 4b: specificare le impostazioni di base


1.	Immettere un nome significativo per il gruppo di sincronizzazione.
2.	Nell'elenco a discesa **REGION** selezionare il data center per ospitare il gruppo di sincronizzazione.
3. Fare clic sulla freccia destra.

	![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### Passaggio 4c: definire l'hub di sincronizzazione

1. Nell'elenco a discesa selezionare l'istanza di database SQL da usare come hub per il gruppo di sincronizzazione.
2. Immettere le credenziali per l'istanza di database SQL in **HUB USERNAME** e **HUB PASSWORD**.
3. Attendere che la sincronizzazione dati SQL verifichi il nome utente e la password. Quando le credenziali risultano verificate, viene visualizzato un segno di spunta verde a destra del campo PASSWORD.
4. Nell'elenco a discesa **CONFLICT RESOLUTION** selezionare i criteri di risoluzione dei conflitti.

 **Hub Wins** indica che tutte le modifiche scritte nel database hub vengono scritte nei database di riferimento, sovrascrivendo le modifiche nello stesso record del database di riferimento. Dal punto di vista funzionale, questo significa che la prima modifica scritta nell'hub viene propagata negli altri database.


 **Client Wins** indica che le modifiche scritte nell'hub vengono sovrascritte dalle modifiche presenti nei database di riferimento. Dal punto di vista funzionale, questo significa che l'ultima modifica scritta nell'hub corrisponde a quella conservata e propagata negli altri database.

5.	Fare clic sulla freccia destra.

	![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### Passaggio 4d: aggiungere un database di riferimento


Ripetere questo passaggio per ogni ulteriore database da aggiungere al gruppo di sincronizzazione.

1. Selezionare il database da aggiungere nell'elenco a discesa.

	I database elencati sono i database di SQL Server registrati con l'agente e le istanze del database SQL.
2.	Immettere le credenziali per il database in **USERNAME** e **PASSWORD**.
3.	Nell'elenco a discesa **SYNC DIRECTION** selezionare la direzione di sincronizzazione per il database.

	**Bi-directional** indica che le modifiche nel database di riferimento vengono scritte nel database hub e che le modifiche apportate al database hub vengono scritte nel database di riferimento.

	**Sync from the Hub** indica che il database riceve gli aggiornamenti dall'hub. Le modifiche non vengono inviate all'hub.

	**Sync to the Hub** indica che il database invia gli aggiornamenti all'hub. Le modifiche apportate all'hub non vengono scritte in questo database.

4.	Per completare la creazione del gruppo di sincronizzazione, fare clic sul segno di spunta nella parte inferiore destra della procedura guidata. Attendere che la sincronizzazione dati SQL verifichi le credenziali. Un segno di spunta verde indica che le credenziali sono state verificate.

5.	Fare clic sul segno di spunta una seconda volta. Si tornerà alla pagina **SYNC** in SQL Databases. Il gruppo di sincronizzazione è ora elencato con gli altri gruppi di sincronizzazione e con gli agenti.

	![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## Passaggio 5: definire i dati da sincronizzare

La sincronizzazione dati SQL di Azure consente di selezionare le tabelle e le colonne da sincronizzare. Se si desidera inoltre filtrare una colonna in modo che vengano sincronizzate solo le righe con valori specifici (ad esempio Età>=65), utilizzare il portale di sincronizzazione dei dati SQL in Azure e la documentazione relativa alla selezione di tabelle, colonne e righe da sincronizzare per definire quali dati sincronizzare.

1.	Tornare al [portale di gestione](http://manage.windowsazure.com).
2.	Fare clic su **SQL DATABASES**.
3.	Fare clic sulla scheda **SYNC**.
4.	Fare clic sul nome del gruppo di sincronizzazione.
5.	Fare clic sulla scheda **SYNC RULES**.
6.	Selezionare il database da specificare per lo schema del gruppo di sincronizzazione.
7.	Fare clic sulla freccia destra.
8.	Fare clic su **REFRESH SCHEMA**.
9.	Per ogni tabella del database, selezionare le colonne che si desidera includere nelle sincronizzazioni.
	- Le colonne con tipi di dati non supportati non possono essere selezionate.
	- Se in una tabella non sono selezionate colonne, la tabella non viene inclusa nel gruppo di sincronizzazione.
	- Per selezionare o deselezionare tutte le tabelle, fare clic su SELECT in fondo alla schermata.
10.	Fare clic su **SAVE**, quindi attendere il completamento del provisioning del gruppo di sincronizzazione.
11.	Per tornare alla pagina iniziale di sincronizzazione dati, fare clic sulla freccia Indietro nella parte superiore sinistra della schermata (sopra al nome del gruppo di sincronizzazione).

	![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## Passaggio 6: configurare il gruppo di sincronizzazione

È sempre possibile sincronizzare un gruppo di sincronizzazione facendo clic su SINCRONIZZA nella parte inferiore della pagina di destinazione Sincronizzazione dati. Se si desidera sincronizzare un gruppo di sincronizzazione in base a una pianificazione, configurarlo.

1.	Tornare al [portale di gestione](http://manage.windowsazure.com).
2.	Fare clic su **SQL DATABASES**.
3.	Fare clic sulla scheda **SYNC**.
4.	Fare clic sul nome del gruppo di sincronizzazione.
5.	Fare clic sulla scheda **CONFIGURE**.
6.	**SINCRONIZZAZIONE AUTOMATICA**
	- Per configurare il gruppo di sincronizzazione in modo che venga sincronizzato con una determinata frequenza, fare clic su **ON**. È comunque possibile eseguire la sincronizzazione su richiesta facendo clic su SYNC.
	- Fare clic su **OFF** per configurare la sincronizzazione del gruppo solo quando si fa clic su SYNC.
7.	**FREQUENZA SINCRONIZZAZIONE**
	- Se l'opzione SINCRONIZZAZIONE AUTOMATICA è impostata su ATTIVATO, impostare la frequenza per la sincronizzazione. La frequenza deve essere compresa tra 5 minuti e 1 mese.
8.	Fare clic su **SAVE**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

A questo punto è stato creato un gruppo di sincronizzazione che include sia un'istanza di database SQL che un database di SQL Server.

## Passaggi successivi
Per altre informazioni sul database SQL e la sincronizzazione dati SQL, vedere:

* [Contenuti relativi alla sincronizzazione dati SQL in MSDN Library](https://msdn.microsoft.com/library/azure/hh456371.aspx)
* [Panoramica del database SQL](sql-database-technical-overview.md)
* [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
 

 

<!---HONumber=July15_HO3-->