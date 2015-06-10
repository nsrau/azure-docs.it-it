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
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#Migrazione di un database compatibile mediante SSMS 

![testo alternativo](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Se uno schema di database è già compatibile con database SQL di Azure, la migrazione sarà semplice. Poiché non è necessaria alcuna trasformazione, la migrazione richiede solo che il database venga importato in Azure. Questa operazione può essere eseguita in un unico passaggio tramite SSMS, 'distribuendo' il database a database SQL di Azure o come processo a due passaggi, esportando dapprima un file BACPAC e quindi importandolo in un server di database SQL di Azure per creare un nuovo database.

È possibile caricare il file BACPAC esportato nella risorsa di archiviazione di Azure e importarlo tramite il portale. L’esecuzione dell’importazione nel cloud consentirà di ridurre la latenza in fase di importazione, in modo da migliorare le prestazioni e l’affidabilità della migrazione con database di grandi dimensioni.

Se la distribuzione viene eseguita direttamente da SSMS verranno sempre distribuiti schema e dati, mentre l’esportazione e l’importazione distribuiranno sempre lo schema fornendo un’opzione per distribuire i dati da tutte le tabelle o da un subset di queste. Sia che la distribuzione avvenga tramite SSMS o mediante l’esportazione e quindi l’importazione da SSMS (o successivamente dal portale) viene utilizzata la stessa tecnologia DAC e il risultato è lo stesso.

Questa opzione viene utilizzata anche come passaggio finale nell'opzione 3 per eseguire la migrazione dei database dopo che è stata aggiornata per renderla compatibile con il database SQL di Azure.

##Utilizzo di SSMS per la distribuzione al database SQL di Azure
1.	Eseguire il provisioning di un server utilizzando il portale di Azure, come descritto in Creare il server di destinazione per il database migrato.
2. Individuare il database di origine in Esplora oggetti di SSMS ed eseguire l'attività **Distribuire il database al database SQL di Azure...**

	![testo alternativo](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	Nella distribuzione guidata configurare la connessione al server di database SQL di Azure di destinazione di cui è stato eseguito il provisioning nel passaggio.
4.	Fornire il **nome** per il database e impostare **Edizione** (livello di servizio) e **Obiettivo servizio** (livello di prestazioni). Vedere Scelta di un livello di prestazioni o di un livello di prezzo per la migrazione, per ulteriori informazioni sulla configurazione di queste impostazioni. 

	![testo alternativo](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Completare la procedura guidata per eseguire la migrazione del database. A seconda delle dimensioni e della complessità della distribuzione del database può richiedere da pochi minuti a diverse ore. Se si verificano errori che indicano che lo schema del database non è compatibile con il database SQL, è necessario utilizzare un’altra opzione.
##Utilizzare SSMS per esportare un file BACPAC e quindi importarlo in database SQL
Il processo di distribuzione può essere suddiviso in due passaggi: esportazione e importazione. Nel primo passaggio viene creato un file BACPAC che viene quindi utilizzato come input nel secondo passaggio.

1.	Eseguire il provisioning di un server utilizzando l’aggiornamento di database SQL più recente, come descritto in Creare il server di destinazione per il database migrato.
2.	Individuare il database di origine in Esplora oggetti di SSMS e selezionare l'attività **Distribuire il database al database SQL di Windows Azure...**

	![testo alternativo](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. Nell’esportazione guidata configurare l'esportazione in modo da salvare il file BACPAC localmente. Il file BACPAC esportato include sempre lo schema di database completo e, per impostazione predefinita, i dati di tutte le tabelle. Se si desidera escludere i dati da alcune o tutte le tabelle, utilizzare la scheda Avanzate. È possibile, ad esempio, scegliere di esportare solo i dati per le tabelle di riferimento.
	>[AZURE.NOTE]Nota: quando il portale di gestione di Azure supporta l'importazione in esecuzione in Azure, è possibile scegliere di salvare il file BACPAC esportato nella risorsa di archiviazione di Azure ed eseguire l'importazione nel cloud.

	![testo alternativo](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Dopo aver creato il file BACPAC, connettersi al server creato nel passaggio 1, fare clic con il pulsante destro del mouse sulla cartella Database e selezionare Importa applicazione livello dati...

	![testo alternativo](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	Nell’importazione guidata selezionare il file BACPAC appena esportato per creare il nuovo database in database SQL di Azure.

	![testo alternativo](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Fornire il nome per il database e impostare Edizione (livello di servizio) e Obiettivi di servizio (livello di prestazioni).
	 
7.	Completare la procedura guidata per importare il file BACPAC e creare il database nel database SQL di Azure.

	![testo alternativo](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##Alternative
È inoltre possibile utilizzare l’utilità della riga di comando sqlpackage.exe per distribuire il database oppure esportare e importare un file BACPAC. Sqlpackage.exe utilizza la stessa tecnologia DAC di SSMS, pertanto il risultato è lo stesso. Per ulteriori informazioni, vedere [qui](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=58-->