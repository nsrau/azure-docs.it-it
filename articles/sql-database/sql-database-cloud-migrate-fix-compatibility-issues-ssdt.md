<properties
   pageTitle="Risolvere i problemi di compatibilità del database del server SQL prima di effettuare la migrazione al database SQL | Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Eseguire la migrazione di un database di SQL Server a un database SQL di Azure usando SQL Server Data Tools per Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Preparazione aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)

Questo articolo illustra come individuare e risolvere i problemi di compatibilità dei database di SQL Server usando SQL Server Data Tools per Visual Studio prima di eseguire la migrazione al database SQL di Azure.

## Utilizzare SQL Server Data Tools per Visual Studio.

Utilizzare SQL Server Data Tools per Visual Studio ("SSDT") per importare lo schema del database in un progetto database di Visual Studio per l'analisi. Per eseguire l’analisi, specificare Database SQL versione 12 come piattaforma di destinazione per il progetto, quindi compilare il progetto. Se la compilazione ha esito positivo, il database è compatibile. Se la compilazione non viene eseguita correttamente, è possibile risolvere gli errori in SSDT (o uno degli altri strumenti descritti in questo argomento). Una volta creato il progetto, è possibile pubblicarlo nuovamente come una copia del database di origine e quindi utilizzare la funzione di confronto di dati in SSDT per copiare i dati dal database di origine al database compatibile alla versione 12 di SQL Azure. È quindi possibile migrare il database aggiornato. Per utilizzare questa opzione, scaricare la [versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagramma di migrazione di VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se è necessario eseguire la migrazione solo dello schema, è possibile pubblicarlo direttamente da Visual Studio nel database SQL di Azure. Utilizzare questo metodo quando lo schema del database richiede altre modifiche che è possibile gestire solo attraverso la migrazione guidata.

## Rilevamento di problemi di compatibilità con SQL Server Data Tools per Visual Studio
   
1.	Aprire **Esplora oggetti di SQL Server** in Visual Studio. Utilizzare **Aggiungi SQL Server** per connettersi all'istanza di SQL Server contenente il database in corso di migrazione. Individuare il database in Esplora risorse, fare clic su di esso con il pulsante destro del mouse e selezionare **Crea nuovo progetto...**     
    
	![Nuovo progetto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)
   
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
    
## Correzione di problemi di compatibilità con SQL Server Data Tools per Visual Studio        
  
1.	Fare doppio clic sul primo script per aprire lo script in una finestra di query e impostare lo script come commento e quindi eseguire lo script. ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)    

2.	Ripetere questo processo per ogni script che contiene le incompatibilità fino a quando non rimangono errori. ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3.	Quando il database è privo di errori, fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica** per compilare e pubblicare il database in una copia del database di origine (si consiglia di utilizzare una copia, almeno inizialmente).     
 - Prima della pubblicazione, a seconda della versione di SQL Server di origine (precedenti a SQL Server 2014), potrebbe essere necessario reimpostare la piattaforma di destinazione del progetto per consentire la distribuzione.     
 - Se si esegue la migrazione di un database SQL Server precedente, non è necessario introdurre nel progetto alcuna funzionalità che non sia supportata nell’SQL Server di origine, a meno che il database non venga prima migrato a una versione più recente di SQL Server.     

    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
    
4.	In Esplora oggetti di SQL Server, fare clic con il tasto destro del mouse sul database di origine e fare clic su **Confronto dati** per confrontare il progetto con database originale per comprendere quali modifiche sono state apportate dalla procedura guidata. Selezionare la versione del database Azure SQL V12 e quindi fare clic su **Fine**.    
    
	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)
    
	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)
    
5.	Esaminare le differenze rilevate e quindi fare clic su **Aggiorna destinazione** per la migrazione dei dati dal database di origine al database di Azure SQL V12.
    
	![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)
    
6.	Scegliere un metodo di distribuzione. Vedere [Eseguire la migrazione di un database di SQL Server compatibile al database SQL](sql-database-cloud-migrate.md).

## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

## Altre informazioni

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0608_2016-->