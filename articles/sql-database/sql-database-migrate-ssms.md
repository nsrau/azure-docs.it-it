<properties
   pageTitle="Migrazione a database SQL tramite SSMS"
   description="Database SQL di Microsoft Azure, eseguire la migrazione di database sql, eseguire la migrazione tramite ssms"
   services="sql-database"
   documentationCenter=""
   authors="pehteh"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="07/17/2015"
   ms.author="pehteh"/>

#Migrazione di un database compatibile mediante SSMS 

![Diagramma di migrazione di SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Se uno schema di database è già compatibile con il database SQL di Azure, la migrazione sarà semplice. Se non è necessaria alcuna modifica dello schema, per la migrazione è solo necessario che il database venga importato in Azure. Questa operazione può essere eseguita in un unico passaggio tramite SSMS, 'distribuendo' il database al database SQL di Azure o in due passaggi, esportando dapprima un file BACPAC del database attuale importandolo poi in Azure per creare un nuovo database SQL .

Inoltre, è possibile caricare il file BACPAC esportato nell’archiviazione di Azure e quindi importare il file BACPAC come database tramite il portale. L’esecuzione dell’importazione in Azure consentirà di ridurre la latenza in fase di importazione, in modo da migliorare le prestazioni e l’affidabilità della migrazione con database di grandi dimensioni.

Se la distribuzione viene eseguita direttamente da SSMS verranno sempre distribuiti schema e dati, mentre l’esportazione e l’importazione distribuiranno sempre lo schema fornendo un’opzione per distribuire i dati da tutte le tabelle o da un subset di queste. Sia che la distribuzione avvenga tramite SSMS o mediante l’esportazione e quindi l’importazione da SSMS (o successivamente dal portale) viene utilizzata la stessa tecnologia DAC e il risultato è lo stesso.

Questa opzione viene utilizzata anche come passaggio finale nell'opzione 3 per eseguire la migrazione dei database dopo che è stata aggiornata per renderla compatibile con il database SQL di Azure.

##Utilizzo di SSMS per la distribuzione al database SQL di Azure
1.	Eseguire il provisioning di un server logico tramite il portale di gestione di Azure.
2. Individuare il database di origine in Esplora oggetti di SSMS ed eseguire l'attività **Distribuire il database al database SQL di Azure...**

	![Distribuire in Azure dal menu Attività](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	Nella distribuzione guidata configurare la connessione al server del database SQL di Azure di destinazione di cui è stato eseguito il provisioning.
4.	Fornire il **nome** per il database e impostare **Edizione** (livello di servizio) e **Obiettivo servizio** (livello di prestazioni). Vedere[livelli di servizio del Database SQL di Azure](sql-database-service-tiers.md)per ulteriori informazioni sulla configurazione di queste impostazioni. 

	![Esportazione impostazioni](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Completare la procedura guidata per la migrazione del database. A seconda delle dimensioni e della complessità del database la distribuzione può richiedere da pochi minuti a diverse ore. Se si verificano errori che indicano che lo schema del database è incompatibile con il Database SQL, utilizzare un'opzione di migrazione diversa.

##Utilizzare SSMS per esportare un file BACPAC e quindi importarlo in database SQL
Il processo di distribuzione può essere suddiviso in due passaggi: esportazione e importazione. Nel primo passaggio viene creato un file BACPAC che viene poi utilizzato come input nel secondo passaggio.

1.	Eseguire il provisioning di un server logico tramite il portale di gestione di Azure.
2.	Individuare il database di origine in Esplora oggetti di SSMS e selezionare l'attività **Distribuire il database al database SQL di Azure...**

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. Nell’esportazione guidata configurare l'esportazione in modo da salvare il file BACPAC localmente. Il file BACPAC esportato include sempre lo schema di database completo e, per impostazione predefinita, i dati di tutte le tabelle. Se si desidera escludere i dati da alcune o tutte le tabelle, utilizzare la scheda Avanzate. È possibile, ad esempio, scegliere di esportare solo i dati per le tabelle di riferimento.
	>[AZURE.NOTE]Nota: quando il portale di gestione di Azure supporta l'importazione in esecuzione in Azure, è possibile scegliere di salvare il file BACPAC esportato nella risorsa di archiviazione di Azure ed eseguire l'importazione nel cloud.

	![Esportazione impostazioni](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Dopo aver creato il file BACPAC, connettersi al server creato nel passaggio 1, fare clic con il pulsante destro del mouse sulla cartella **Database** e cliccare **Importa applicazione livello dati...**

	![Importare la voce di menu dell’applicazione livello dati](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	Nell’importazione guidata selezionare il file BACPAC appena esportato per creare il nuovo database in database SQL di Azure.

	![Importare impostazioni](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Fornire il nome per il database e impostare Edizione (livello di servizio) e Obiettivi di servizio (livello di prestazioni).
	 
7.	Completare la procedura guidata per importare il file BACPAC e creare il database nel database SQL di Azure.

	![Impostazioni database](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##Alternative
È inoltre possibile utilizzare l’utilità della riga di comando sqlpackage.exe per distribuire il database oppure esportare e importare un file BACPAC. Sqlpackage.exe utilizza la stessa tecnologia DAC di SSMS, pertanto il risultato è lo stesso. Per ulteriori informazioni vedere [SqlPackage.exe su MSDN](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=July15_HO4-->