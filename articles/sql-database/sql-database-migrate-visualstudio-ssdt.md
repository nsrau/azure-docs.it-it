<properties 
   pageTitle="Migrazione mediante Visual Studio e SSDT"
	description="Database SQL di Microsoft Azure, migrazione del database, importazione del database, esportazione del database, migrazione guidata"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#Aggiornare il database sul posto, quindi distribuire database SQL di Azure

![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Utilizzare questa opzione quando la migrazione di un database locale in Azure SQL Database Versione 12 richiede modifiche dello schema poichè il database utilizza funzionalità do SQL Server non supportate nel database SQL di Azure o per verificare che siano presenti funzionalità non supportate nel database locale.

Utilizzare il[più recente SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)con Visual Studio 2013 aggiornamento 4 o versione successiva.

Con questa opzione:

 - SQL Server Data Tools per Visual Studio viene utilizzato innanzitutto per creare un progetto di database dal database di origine. 
 - La piattaforma di destinazione del progetto viene quindi impostata sul database SQL V12 di Azure e il progetto viene compilato per identificare tutti i problemi di compatibilità. 
 - Una volta compilato correttamente il progetto, lo schema viene pubblicato nuovamente in una copia del database di origine (non sovrascrivere il database di origine).
 - La funzionalità di confronto dati in SSDT viene quindi utilizzata per confrontare il database di origine al database SQL Azure compatibile appena creato e quindi aggiornare il nuovo database con i dati dal database di origine. 
 - Il database aggiornato viene quindi distribuito in Azure con SSMS, direttamente o mediante l’esportazione e l’importazione di un file BACPAC.
 
>[AZURE.NOTE]Se è necessario eseguire la distribuzione del solo schema, è possibile pubblicare lo schema aggiornato direttamente da Visual Studio al database SQL di Azure.

## Passaggi della migrazione

1.	Aprire **Esplora oggetti di SQL Server** in Visual Studio. Utilizzare **Aggiungi SQL Server** per connettersi all'istanza di SQL Server contenente il database in corso di migrazione. Individuare il database in Esplora risorse, fare clic su di esso con il pulsante destro del mouse e selezionare **Crea nuovo progetto...** 

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Configurare le impostazioni di importazione su **Importa solo oggetti con ambito applicazione**. Deselezionare le opzioni per importare gli account di accesso, le autorizzazioni e le impostazioni del database.

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Fare clic su **Avvia** per importare il database e creare il progetto che conterrà un file di script T-SQL per ogni oggetto nel database. I file di script vengono nidificati nelle cartelle all'interno del progetto.

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto di database e scegliere Proprietà. Verrà aperta la pagina **Impostazioni progetto** in cui è necessario configurare la piattaforma di destinazione su database SQL V12 di Microsoft Azure.

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Fare clic con il pulsante destro del mouse sul progetto e selezionare **Compila** per compilare il progetto.

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	L’**elenco errori** visualizza ogni incompatibilità. In questo caso, il nome utente NT AUTHORITY\\NETWORK SERVICE non è compatibile. Poiché non è compatibile, è possibile impostarlo come commento o rimuoverlo (e indirizzare le implicazioni della rimozione di questo accesso e del ruolo dalla soluzione del database).

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	Fare doppio clic sul primo script per aprire lo script in una finestra di query e impostare lo script come commento e quindi eseguire lo script. ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	Ripetere questo processo per ogni script che contiene le incompatibilità fino a quando non rimangono errori. ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	Quando il database è privo di errori, fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica** per compilare e pubblicare il database in una copia del database di origine (si consiglia di utilizzare una copia, almeno inizialmente). 
 - Prima della pubblicazione, a seconda della versione di SQL Server di origine (precedenti a SQL Server 2014), potrebbe essere necessario reimpostare la piattaforma di destinazione del progetto per consentire la distribuzione. 
 - Se si esegue la migrazione di un database SQL Server precedente, non è necessario introdurre nel progetto alcuna funzionalità che non sia supportata nell’SQL Server di origine, a meno che il database non venga prima migrato a una versione più recente di SQL Server. 

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	In Esplora oggetti di SQL Server, fare clic con il tasto destro del mouse sul database di origine e fare clic su **Confronto dati** per confrontare il progetto con database originale per comprendere quali modifiche sono state apportate dalla procedura guidata. Selezionare la versione del database Azure SQL V12 e quindi fare clic su **Fine**.

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	Esaminare le differenze rilevate e quindi fare clic su **Aggiorna destinazione** per la migrazione dei dati dal database di origine al database di Azure SQL V12.

	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	Distribuire lo schema del database compatibile con Azure SQL V12 e i dati al Database di SQL Azure mediante SSMS. Vedere [Migrazione di un database compatibile mediante SSMS.](sql-database-migrate-ssms.md)

<!---HONumber=August15_HO9-->