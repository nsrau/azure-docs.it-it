<properties
   pageTitle="Migrazione di un database SQL Server nel database SQL di Azure"
   description="Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata"
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
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# Migrazione di un database SQL Server nel database SQL di Azure

La versione 12 del database SQL di Azure offre una compatibilità quasi completa con SQL Server 2014 e SQL Server 2016. Per i database compatibili, la migrazione al database SQL di Azure è un'operazione di spostamento semplice che richiede poche modifiche allo schema e ai dati, con una minima o addirittura nessuna attività di riprogettazione dello schema e delle applicazioni. Anche quando è necessario apportare modifiche ai database, l'ambito di tali modifiche è più limitato rispetto al database SQL di Azure V11.

Per impostazione predefinita, le funzionalità con ambito server di SQL Server non sono supportate dalla versione 12 del Database di SQL Azure. I database e le applicazioni basate su queste funzionalità dovranno essere riprogettate prima della migrazione.

>[AZURE.NOTE]Per eseguire la migrazione di altri tipi di database, inclusi Microsoft Access, Sybase, MySQL Oracle e DB2 nel database SQL di Azure, vedere il post di blog su [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/).

Ecco il flusso di lavoro per la migrazione di un database SQL Server nel database SQL di Azure:

 1. [Determinare se il database è compatibile.](#determine-if-your-database-is-compatible)
 2. [Se non è compatibile, risolvere i problemi di compatibilità del database.](#fix-database-compatibility-issues)
 3. [Eseguire la migrazione di un database compatibile.](#options-to-migrate-a-compatible-database-to-azure-sql-database)

## Determinare se il database è compatibile
È possibile usare due metodi principali per determinare se il database di origine è compatibile. - Esporta l'applicazione livello dati: questo metodo usa una procedura guidata in Management Studio e visualizza messaggi di errore nella console. - SQLPackage.exe: [sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) è un'utilità da riga di comando disponibile in Visual Studio e SQL Server. Questo metodo genererà un report.

> [AZURE.NOTE]È disponibile anche un terzo metodo, che userà anche i file di traccia per testar la compatibilità. Si tratta della [Migrazione guidata SQL Azure](http://sqlazuremw.codeplex.com/), uno strumento gratuito su Codeplex. È tuttavia possibile che questo strumento individui errori di compatibilità che costituiscono un problema per il database SQL di Azure V11 ma non vengono considerati invece errori per il database SQL di Azure V12.

Se vengono rilevate incompatibilità del database, sarà necessario correggere le incompatibilità prima di eseguire la migrazione del database al database SQL di Azure. Per indicazioni su come risolvere i problemi di compatibilità del database, passare a [Risolvere i problemi di compatibilità del database](#fix-database-compatibility-issues).

> [AZURE.IMPORTANT]Queste opzioni non rilevano tutti i problemi di compatibilità tra diversi livelli di database SQL Server, ad esempio di livello 90, 100 e 110. Se si esegue la migrazione da un database precedente (livello 80, 90, 100 e 110), è consigliabile eseguire prima di tutto il processo di aggiornamento, almeno nell'ambiente di sviluppo, e dopo il passaggio a SQL Server 2014 o versioni successive eseguire la migrazione al database SQL di Azure.

## Determinare se il database è compatibile mediante sqlpackage.exe

1. Aprire un prompt dei comandi e passare a una directory contenente la versione più recente di sqlpackage.exe. Questa utilità è disponibile sia in Visual Studio che in SQL Server.
2. Eseguire il comando seguente, sostituendo i valori degli argomenti seguenti: < server_name >, < database_name >, < target_file >, < schema_name.table_name > e < output_file >. L'argomento /p:TableName viene usato per indicare che si vuole testare la compatibilità del database solo per l'esportazione nel database SQL di Azure V12 , invece di esportare i dati da tutte le tabelle. Sfortunatamente, l'argomento di esportazione per sqlpackage.exe non consente di evitare l'esportazione di tabelle, quindi sarà necessario specificare una singola tabella di piccole dimensioni. < output_file > includerà il report relativo a eventuali errori.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Aprire il file di output ed esaminare gli eventuali errori di compatibilità. Per indicazioni su come risolvere i problemi di compatibilità del database, passare a [Risolvere i problemi di compatibilità del database](#fix-database-compatibility-issues).

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Determinare se il database è compatibile mediante Esporta l'applicazione livello dati

1. Verificare che la versione di SQL Server Management Studio disponibile sia 13.0.600.65 o successiva. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

 	 >[AZURE.IMPORTANT]Scaricare la versione [più recente](https://msdn.microsoft.com/library/mt238290.aspx) di SQL Server Management Studio. È consigliabile usare sempre la versione più aggiornata di Management Studio.

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività**, quindi fare clic su **Esporta l'applicazione livello dati**.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Nella scheda **Impostazioni** dell'esportazione guidata configurare l'esportazione per salvare il file BACPAC su un percorso disco locale o in un BLOB di Azure. Un file BACPAC verrà salvato solo se non sono presenti problemi di compatibilità del database. Eventuali problemi di compatibilità verranno visualizzati nella console.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Fare clic sulla scheda **Avanzate** e deselezionare la casella di controllo **Seleziona tutto** per evitare l'esportazione dei dati. In questa fase l'obiettivo principale consiste nella verifica della compatibilità.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Fare clic su **Avanti**, quindi su **Fine**. Eventuali problemi di compatibilità verranno visualizzati dopo la convalida dello schema da parte della procedura guidata.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se non vengono visualizzati errori, il database sarà compatibile e sarà possibile iniziare la migrazione. Se sono presenti errori, sarà necessario risolverli. Per visualizzare gli errori, fare clic su **Errore** per **Convalida schema**. Per informazioni su come risolvere questi errori, vedere [Risolvere i problemi di compatibilità del database](#fix-database-compatibility-issues).

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

## Opzioni per la migrazione di un database compatibile nel database SQL di Azure

- Per i database di piccole e medie dimensioni, la migrazione di database [compatibile](#determine-if-your-database-is-compatible) con SQL Server 2005 o versioni successive è semplice quanto l'esecuzione della [Migrazione di database nel database SQL di Microsoft Azure](#use-the-deploy-database-to-microsoft-azure-database-wizard) in SQL Server Management Studio. In caso di problemi di connettività, ovvero nessuna connettività, larghezza di banda ridotta o problemi di timeout, è possibile [usare un file BACPAC per la migrazione](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) di un database SQL Server al database SQL di Azure.
- Per database di medie e grandi dimensioni o in caso di problemi di connettività, [usare un file BACPAC per la migrazione](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) di un database SQL Server al database SQL di Azure. Questo metodo consente di usare SQL Server Management Studio per esportare i dati e lo schema in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (archiviato localmente o in un BLOB di Azure) e quindi di importare il file BACPAC nell'istanza del database SQL di Azure. Se si archivia il file BACPAC in un BLOB di Azure, è inoltre possibile importare il file BACPAC dal [portale di Azure](sql-database-import.md) o [mediante PowerShell](sql-database-import-powershell.md).
- Per i database di grandi dimensioni, si otterranno migliori prestazioni migrando lo schema e i dati separatamente. Questo metodo consente di creare lo schema mediante script usando SQL Server Management Studio oppure di creare un progetto database in Visual Studio e quindi distribuire lo schema nel database SQL di Azure. Dopo l'importazione dello schema nel database SQL di Azure, usare [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per estrarre i dati in file flat e quindi importare i file nel database SQL di Azure.

 ![Diagramma di migrazione di SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

## Usare Distribuisci il database nel database SQL di Microsoft Azure

La procedura guidata Distribuisci il database nel database SQL di Microsoft Azure in SQL Server Management Studio esegue la migrazione di un database [compatibile](#determine-if-your-database-is-compatible) di SQL Server 2005 o versioni successive direttamente nell'istanza del server logico del database SQL di Azure.

> [AZURE.NOTE]Il passaggio precedente presuppone che sia già stato effettuato il provisioning dell'istanza logica del database SQL di Azure e che le informazioni di connessione siano disponibili.

1. Verificare che la versione di SQL Server Management Studio disponibile sia 13.0.600.65 o successiva. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

	 >[AZURE.IMPORTANT]Scaricare la versione [più recente](https://msdn.microsoft.com/library/mt238290.aspx) di SQL Server Management Studio. È consigliabile usare sempre la versione più aggiornata di Management Studio.

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività**, quindi fare clic su **Distribuisci il database nel database SQL di Microsoft Azure**.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	Nella distribuzione guidata configurare la connessione al server del database SQL di Azure.
5.	Specificare il **Nome nuovo database** per il database nel database SQL di Azure, impostare l'**Edizione del database SQL di Microsoft Azure** (livello del servizio), **Dimensioni massime database**, **Obiettivo servizio** (livello di prestazioni) e **Nome file temporaneo** per il file BACPAC creato dalla procedura guidata durante il processo di migrazione. Per altre informazioni sui livelli di servizio e sui livelli di prestazioni, vedere [Livelli di servizio del database SQL](sql-database-service-tiers.md).

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Completare la procedura guidata per la migrazione del database. A seconda delle dimensioni e della complessità del database la distribuzione può richiedere da pochi minuti a diverse ore.
7.	Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.
8.	Usando il portale di Azure visualizzare il database e le relative proprietà.

## Usare un file BACPAC per eseguire la migrazione di un database SQL Server nel database SQL di Azure

Per database di medie e grandi dimensioni o in caso di problemi di connettività, è possibile suddividere il processo di migrazione in due passaggi distinti. È possibile esportare lo schema e i relativi dati in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando uno o due metodi.

- [Eseguire l'esportazione in un file BACPAC mediante SQL Server Management Studio](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [Eseguire l'esportazione in un file BACPAC mediante SqlPackage](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

È possibile archiviare questo file BACPAC localmente o in un BLOB di Azure. È quindi possibile importare il file BACPAC nel database SQL di Azure usando uno dei molti metodi disponibili.

- [Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SQL Server Management Studio](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SqlPackage](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante il portale di Azure](sql-database-import.md)
- [Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante PowerShell](sql-database-import-powershell.md)

## Esportare un database SQL Server compatibile in un file BACPAC mediante SQL Server Management Studio

Eseguire i passaggi seguenti per usare Management Studio per esportare un database SQL Server [compatibile](#determine-if-your-database-is-compatible) in un file BACPAC.

1. Verificare che la versione di SQL Server Management Studio disponibile sia 13.0.600.65 o successiva. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

	 >[AZURE.IMPORTANT]Scaricare la versione [più recente](https://msdn.microsoft.com/library/mt238290.aspx) di SQL Server Management Studio. È consigliabile usare sempre la versione più aggiornata di Management Studio.

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività**, quindi fare clic su **Esporta l'applicazione livello dati**.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Nell'esportazione guidata configurare l'esportazione per salvare il file BACPAC in un percorso sul disco locale o in un BLOB di Azure. Il file BACPAC esportato include sempre lo schema di database completo e, per impostazione predefinita, i dati di tutte le tabelle. Se si desidera escludere i dati da alcune o tutte le tabelle, utilizzare la scheda Avanzate. È ad esempio possibile scegliere di esportare solo i dati per le tabelle di riferimento, invece che da tutte le tabelle.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## Esportare un database SQL Server compatibile in un file BACPAC mediante SqlPackage

Eseguire i passaggi seguenti per usare l'utilità da riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per esportare un database [compatibile](#determine-if-your-database-is-compatible) in un file BACPAC.

> [AZURE.NOTE]I passaggi seguenti presuppongono che sia già stato effettuato il provisioning di un server del database SQL di Azure, le informazioni di connessione siano disponibili e sia stata verificati la compatibilità del database di origine.

1. Aprire un prompt dei comandi e passare a una directory contenente l'utilità da riga di comando sqlpackage.exe, disponibile in Visual Studio e SQL Server.
2. Eseguire il comando seguente, sostituendo i valori degli argomenti seguenti: < server_name >, < database_name > e < target_file >.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SQL Server Management Studio

Eseguire i passaggi seguenti per eseguire l'importazione da un file BACPAC nel database SQL di Azure.

> [AZURE.NOTE]Il passaggio precedente presuppone che sia già stato effettuato il provisioning dell'istanza logica del database SQL di Azure e che le informazioni di connessione siano disponibili.

1. Verificare che la versione di SQL Server Management Studio disponibile sia 13.0.600.65 o successiva. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

	 >[AZURE.IMPORTANT]Scaricare la versione [più recente](https://msdn.microsoft.com/library/mt238290.aspx) di SQL Server Management Studio. È consigliabile usare sempre la versione più aggiornata di Management Studio.

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

    Dopo avere creato il file BACPAC, connettersi al server del database SQL di Azure, fare clic con il pulsante destro del mouse sulla cartella **Database**, quindi scegliere **Importa applicazione livello dati**.

    ![Importare la voce di menu dell’applicazione livello dati](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	Nell’importazione guidata selezionare il file BACPAC appena esportato per creare il nuovo database in database SQL di Azure.

    ![Importare impostazioni](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

4.	Specificare il **Nome nuovo database** per il database nel database SQL di Azure, impostare l'**Edizione del database SQL di Microsoft Azure** (livello del servizio), **Dimensioni massime database** e **Obiettivo servizio** (livello di prestazioni).

    ![Impostazioni database](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	Fare clic su **Avanti** e quindi su **Fine** per importare il file BACPAC in un nuovo database nel server del database SQL di Azure.

6. Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.

7.	Usando il portale di Azure visualizzare il database e le relative proprietà.

## Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SqlPackage

Eseguire i passaggi seguenti per usare l'utilità da riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per importare un database SQL Server compatibile o un database SQL di Azure da un file BACPAC.

> [AZURE.NOTE]Il passaggio precedente presuppone che sia già stato effettuato il provisioning di un server del database SQL di Azure e che le informazioni di connessione siano disponibili.

1. Aprire un prompt dei comandi e passare a una directory contenente l'utilità da riga di comando sqlpackage.exe, disponibile in Visual Studio e SQL Server.
2. Eseguire il comando seguente, sostituendo i valori degli argomenti seguenti: < server_name >, < database_name >, < user_name >, < password > e < source_file >.

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< target_file >

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## Risolvere i problemi di compatibilità del database

Se si determina che il database SQL Server di origine non è compatibile, sarà possibile risolvere in diversi modi i problemi di compatibilità [identificati in precedenza](#determine-if-your-database-is-compatible).

- Usare la [migrazione guidata del database SQL di Azure](http://sqlazuremw.codeplex.com/). È possibile usare questo strumento Codeplex per generare uno script T-SQL da un database di origine non compatibile che viene quindi trasformato dalla procedura guidata per renderlo compatibile con il database SQL e quindi effettuare la connessione al database SQL di Azure per eseguire lo script. Questo strumento analizzerà anche i file di traccia per determinare i problemi di compatibilità. Lo script può essere generato solo con lo schema o può includere dati in formato BCP. Per documentazione aggiuntiva, incluse indicazioni dettagliate, vedere la [migrazione guidata del database SQL di Azure](http://sqlazuremw.codeplex.com/) su Codeplex.  

 ![Diagramma di migrazione di SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]Si noti che non tutti gli schemi incompatibili che possono essere rilevati dalla procedura guidata, possono essere elaborati dalla relativa funzionalità di trasformazione incorporata. Script incompatibili che non possono essere corretti verranno segnalati come errori con i commenti inseriti nello script generato. Se vengono rilevati molti errori, usare Visual Studio o SQL Server Management Studio per esaminare e risolvere ogni errore che non è stato risolto mediante la migrazione guidata di SQL Server.

- Usare Visual Studio. È possibile usare Visual Studio per importare lo schema del database in un progetto database di Visual Studio per l'analisi. Per eseguire l’analisi, specificare Database SQL versione 12 come piattaforma di destinazione per il progetto, quindi compilare il progetto. Se la compilazione ha esito positivo, il database è compatibile. Se la compilazione ha esito negativo, è possibile risolvere gli errori con SQL Server Data Tools per Visual Studio ("SSDT"). Una volta creato il progetto, è possibile pubblicarlo nuovamente come una copia del database di origine e quindi utilizzare la funzione di confronto di dati in SSDT per copiare i dati dal database di origine al database compatibile alla versione 12 di SQL Azure. Il database aggiornato viene quindi distribuito nel database SQL di Azure mediante le opzioni [illustrate in precedenza](#options-to-migrate-a-compatible-database-to-azure-sql-database).

 ![Diagramma di migrazione di VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]Se è necessario eseguire la migrazione solo dello schema, è possibile pubblicarlo direttamente da Visual Studio nel database SQL di Azure. Utilizzare questo metodo quando lo schema del database richiede altre modifiche che è possibile gestire solo attraverso la migrazione guidata.

- SQL Server Management Studio. È possibile risolvere i problemi in Management Studio usando alcuni comandi Transact-SQL, ad esempio **ALTER DATABASE**.

<!---HONumber=Oct15_HO3-->