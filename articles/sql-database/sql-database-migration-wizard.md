<properties 
   pageTitle="Utilizzare la migrazione guidata di SQL Azure | Microsoft" 
   description="Database SQL di Microsoft Azure, migrazione del database, importazione del database, esportazione del database, migrazione guidata" 
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
   ms.date="07/01/2015"
   ms.author="pehteh"/>


# Procedura: utilizzare la migrazione guidata di SQL Azure


![testo alternativo](./media/sql-database-migration-wizard/01SAMWDiagram.png)


Questa opzione consente di utilizzare la migrazione guidata di SQL Azure per generare uno script T-SQL da un database di origine; quest'ultimo viene quindi trasformato in modo da essere compatibile con il database SQL e quindi connettersi al database SQL Azure per eseguire lo script in un database SQL Azure vuoto. Lo script può essere generato solo con lo schema o può includere dati in formato BCP. La procedura guidata consente inoltre di includere o escludere oggetti specifici dal. database nello script.


Si noti che non tutti gli schemi incompatibili che possono essere rilevati dalla procedura guidata, possono essere elaborati dalla relativa funzionalità di trasformazione incorporata. Script incompatibili che non possono essere corretti verranno segnalati come errori con i commenti inseriti nello script generato. In tal caso, lo script deve essere salvato e modificato manualmente prima di poter essere inviato al database SQL Azure. Se le modifiche sono necessarie lo script può essere salvato e modificato con SQL Server Management Studio o gli strumenti di SQL Server in Visual Studio e una volta compatibile, eseguita fuori banda sul database di SQL Azure.


> **Nota**: un'estensione di questa opzione, se molti errori vengono rilevati e la correzione non è semplice è possibile importare il file script generato in un progetto di database in Visual Studio. Se si imposta la destinazione del progetto per il database SQL v12, è possibile compilare il progetto e progressivamente correggere gli errori utilizzando gli strumenti di SQL Server in Visual Studio. Una volta che lo schema non presenta errori, è possibile pubblicare lo schema in una copia del database di origine, quindi utilizzare SSMS per distribuire o esportare/importare il database nel database SQL Azure come descritto nell'Opzione 1.


## Scaricare SQLAzureMW.exe


È possibile scaricare la migrazione guidata di SQL Azure da CodePlex:


[Scaricare SQLAzureMW.exe](http://sqlazuremw.codeplex.com/)


## Passaggi della migrazione


&nbsp;1. Eseguire il provisioning di un nuovo database in un nuovo server nel database SQL o in un server esistente aggiornati come descritto in precedenza. Si eseguirà lo script di migrazione creato in questa opzione per questo database come passaggio finale.


&nbsp;2. Aprire la migrazione guidata e selezionare l'opzione per **analizzare o migrare un database**, quindi impostare il **server di destinazione** sul database SQL di Azure versione 12 e fare clic su **Avanti**.


![testo alternativo](./media/sql-database-migration-wizard/02MigrationWizard.png)


&nbsp;3. Nella pagina successiva fare clic su **Connetti al server** e fornire le informazioni di connessione per il server di origine. Specificare il database di origine nella finestra di dialogo connessione oppure connettersi al server, quindi selezionare il database di origine dall'elenco dei database.


![testo alternativo](./media/sql-database-migration-wizard/03MigrationWizard.png)


&nbsp;4. Nella pagina successiva denominata Seleziona oggetti, è possibile scegliere se si desidera creare uno script di tutti gli oggetti di database (impostazione predefinita) o selezionare oggetti di database specifici da inserire nello script. Potrebbe rivelarsi più comodo iniziare inserendo tutti gli oggetti in uno script in un singolo passaggio, quindi potenzialmente tornare a questo passaggio ed escludere oggetti se vengono segnalati errori di script o trasformazione. La procedura guidata funziona inserendo come prima operazione gli oggetti di database in uno script (tramite SMO), quindi elabora lo script generato per applicare una serie di regole di trasformazione e convalida basata su espressioni regolari.


![testo alternativo](./media/sql-database-migration-wizard/04MigrationWizard.png)


&nbsp;5. Scegliere Avanzate e controllare le opzioni di configurazione utilizzate dalla procedura guidata. In un primo passaggio, in particolare per un database di grandi dimensioni è necessario impostare **la tabella e i dati degli script** sull'opzione relativa all'utilizzo di un solo schema della tabella, assicurarsi che il **server di destinazione** sia impostato su Database SQL di Microsoft Azure versione 12 e che le **verifiche di compatibilità** siano impostate in modo che vengano effettuate tutte le verifiche di compatibilità.


![testo alternativo](./media/sql-database-migration-wizard/05MigrationWizard.png)


&nbsp;6. Fare clic su **Avanti** per rivedere le opzioni, quindi **Avanti** per generare e trasformare lo script. È possibile rivedere lo script nella scheda Script SQL.


&nbsp;7. Durante la generazione di script verranno segnalati errori se lo schema non è compatibile con il database SQL e non può essere trasformato dalla procedura guidata. Nella schermata riportata di seguito durante il processo di trasformazione sono stati rilevati problemi con una stored procedure. A tale scopo, è stata creata una procedura per l'utilizzo della ricerca full-text, funzionalità non ancora supportata nella versione 12 (ma sarà supportata nella versione successiva).


![testo alternativo](./media/sql-database-migration-wizard/06MigrationWizard.png)


- A seconda della valutazione degli errori è possibile scegliere di tornare indietro nella procedura guidata ed escludere gli oggetti che causano problemi e rigenerare lo script. Quando si valuta come risolvere gli errori, prendere in considerazione l'impatto su altri oggetti di database, nonché sulle applicazioni che utilizzano il database.
- Se lo script contiene errori che devono essere corretti, è possibile salvare un file di script con solo schema dalla scheda Script SQL e aprire e modificare lo script in un editor per correggere gli errori prima dell'esecuzione all'esterno di SAMW nel nuovo database creato nel passaggio 1. Se lo script è di grandi dimensioni o contiene un numero elevato di errori è consigliabile utilizzare l'opzione 3. Si noti che mentre è possibile importare il file di script generato in Visual Studio, l'importazione da un file SQL file richiedere molto più dell'importazione da un database come descritto nell'opzione 3. 


&nbsp;8. Se non sono presenti errori o se sono stati eliminati gli errori all'origine derivanti dal processo di generazione, è possibile fare clic **Avanti**, quindi nella pagina successiva fare clic su **Connetti al Server** per connettersi al server di database SQL di Azure creato nel passaggio 1.


![testo alternativo](./media/sql-database-migration-wizard/07MigrationWizard.png)


&nbsp;9. Il passaggio successivo consiste nel selezionare il database nel quale verrà eseguito lo script. Verranno elencati tutti i database esistenti nel server. Selezionare il database creato nel passaggio 1. Il database deve essere vuoto e disporre di un livello di prezzo appropriato per questa attività. - A questo punto, se si preferisce, è possibile creare un database utilizzando la procedura guidata. A tale scopo, fare clic su Crea database per configurarlo e crearlo. Vedere la sezione Guida introduttiva per indicazioni su come scegliere il livello di prestazioni da utilizzare durante il processo di migrazione. Una volta creato, selezionare il database e continuare.


&nbsp;10. Con un database vuoto selezionato fare clic su **Avanti** e confermare che si desidera eseguire lo script per completare la migrazione.

 

<!---HONumber=July15_HO2-->