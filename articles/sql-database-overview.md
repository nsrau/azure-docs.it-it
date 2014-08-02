

Come gestire database SQL in Azure
==================================

In questa guida viene illustrato come eseguire attività amministrative per server logici e istanze di database nel database SQL di Azure.

Informazioni sul database SQL
-----------------------------

Il database SQL offre servizi di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con il database SQL, è possibile eseguire facilmente il provisioning e la distribuzione di istanze di database e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.

Sommario
--------

-   [Accesso ad Azure](#PreReq1)
-   [Configurazione di un database SQL](#PreReq2)
-   [Connessione mediante Management Studio](#PreReq3)
-   [Distribuzione di un database in Azure](#HowTo1)
-   [Aggiunta di account di accesso e di utenti](#HowTo2)
-   [Scalabilità di una soluzione di database SQL](#HowTo4)
-   [Monitoraggio di server logici e di istanze di database](#HowTo3)
-   [Passaggi successivi](#NextSteps)

Accesso ad Azure
----------------

Database SQL offre servizi di archiviazione di dati relazionali, di accesso e di gestione in Azure. Per utilizzarlo, è necessario disporre di una sottoscrizione di Azure.

1.  Aprire un Web browser e passare al sito <http://www.windowsazure.com>. Per iniziare usando un account gratuito, fare clic su Versione di valutazione gratuita nell'angolo in alto a destra e seguire le istruzioni visualizzate.

2.  Verrà creato l'account e sarà possibile iniziare.

Creazione e configurazione di un database SQL
---------------------------------------------

Verranno quindi illustrate le procedure per la creazione e la configurazione di un server logico. Nel nuovo portale di gestione di Azure (anteprima) i flussi di lavoro revisionati consentono di creare prima un database e quindi un server.

In questa guida verrà creato prima il server. Questo approccio può essere preferibile se si dispone già di alcuni database di SQL Server da caricare.

### Creare un server logico

1.  Effettuare l'accesso in <http://www.windowsazure.com>,

2.  Fare clic su **SQL Database** e quindi, nella pagina iniziale SQL Database, su **SERVERS**.

3.  Fare clic su **Add** nella parte inferiore della pagina.

4.  In Server Settings immettere un nome di amministratore composto da una sola parola senza spazi.

    Nel database SQL viene utilizzata l'autenticazione SQL tramite una connessione crittografata. Con il nome specificato verrà creato un nuovo account di accesso con autenticazione di SQL Server assegnato al ruolo predefinito del server sysadmin.

    L'account di accesso non può essere un indirizzo di posta elettronica, un account utente Windows o un Windows Live ID. In un database SQL non sono supportate né l'autenticazione basata sulle attestazioni né l'autenticazione di Windows.

5.  Impostare una password complessa composta da più di otto caratteri, utilizzando una combinazione di caratteri in minuscolo e maiuscolo e da un numero o un simbolo.

6.  Scegliere un'area, che determina la posizione geografica del server. Poiché le aree non possono essere cambiate facilmente, sceglierne una significativa per questo server. Selezionare la posizione più vicina. Per ridurre i costi relativi alla larghezza di banda in uscita e la latenza dei dati, è consigliabile mantenere il database e l'applicazione Azure nella stessa area.

7.  Assicurarsi che la casella di controllo **Allow Services** sia selezionata in modo da consentire la connessione al database tramite il portale di gestione per il database SQL, i servizi di archiviazione e altri servizi di Azure.

8.  Al termine, fare clic sul segno di spunta nella parte inferiore della pagina.

Si noti che non è stato specificato un nome di server. Il database SQL genera automaticamente il nome del server per garantire che non vi siano voci DNS duplicate. Il nome del server è una stringa alfanumerica di dieci caratteri. Il nome del server di database SQL non è modificabile.

Nel passaggio successivo si configurerà il firewall in modo da consentire l'accesso alle connessioni da applicazioni in esecuzione nella rete.

### Configurare il firewall per il server logico

1.  Fare clic su **SQL Databases**, su **Servers** e quindi sul server appena creato.

2.  Fare clic su **Configure**.

3.  Copiare l'indirizzo IP del client corrente, ovvero l'indirizzo IP su cui è in ascolto il router o il server proxy, se ci si connette da una rete. Il database SQL rileva l'indirizzo IP utilizzato dalla connessione corrente, pertanto è possibile creare una regola firewall per accettare le richieste di connessione da questo dispositivo.

4.  Incollare l'indirizzo IP nell'intervallo di inizio e di fine. In seguito, se si verificano errori di connessione in cui si informa che l'intervallo è troppo ristretto, è possibile modificare la regola per ampliarlo.

    Se i computer client utilizzano indirizzi IP assegnati dinamicamente, è necessario specificare un intervallo sufficientemente ampio da includere gli indirizzi IP assegnati ai computer della rete. Iniziare con un intervallo ristretto, quindi ampliarlo solo se è necessario.

5.  Immettere un nome per la regola firewall, ad esempio il nome del computer o della società.

6.  Fare clic sul segno di spunta per salvare la regola.

7.  Per completare il passaggio, fare clic su **Save** nella parte inferiore della pagina. Se il pulsante **Save** non è visualizzato, aggiornare la pagina del browser.

A questo punto sono stati configurati un server logico, una regola del firewall che consente le connessioni in ingresso dall'indirizzo IP e un account di accesso dell'amministratore. Nel passaggio successivo verranno completate le rimanenti procedure di configurazione nel computer locale.

**Nota:** il server logico appena creato è temporaneo e verrà ospitato dinamicamente nei server fisici di un data center. Se si elimina il server, tenere presente che si tratta di un'azione irreversibile. Assicurarsi di eseguire il backup di tutti i database caricati in seguito nel server.

Connessione mediante Management Studio
--------------------------------------

Management Studio è uno strumento di amministrazione che consente di gestire più istanze e server di SQL Server in un'unica area di lavoro. Se si dispone già di un'istanza locale di SQL Server, sarà possibile aprire una connessione sia all'istanza locale che a un server logico su Azure per eseguire le attività affiancate.

Management Studio include funzionalità non attualmente disponibili nel portale di gestione, ad esempio un correttore sintattico e la possibilità di salvare script e query denominate in modo da poterli riutilizzare. Il database SQL è semplicemente un endpoint di flusso TDS (Tabular Data Stream). Tutti gli strumenti utilizzabili con il flusso TDS, incluso Management Studio, possono essere utilizzati anche per operazioni del database SQL. Gli script sviluppati per il server locale verranno eseguiti in un server logico di database SQL.

Nel passaggio seguente verrà utilizzato Management Studio per la connessione a un server logico su Azure. Per questo passaggio è necessario disporre di SQL Server Management Studio versione 2008 R2 o 2012. Per informazioni sul download o sulla connessione a Management Studio, vedere [Gestione del database SQL mediante Management Studio](http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/) in questo sito.

Prima di potere effettuare la connessione, è a volte necessario creare un'eccezione del firewall per consentire richieste in uscita sulla porta 1433 nel sistema locale. La porta 1433 non è in genere aperta nei computer protetti per impostazione predefinita.

### Configurare il firewall per un server locale

1.  In Windows Firewall con protezione avanzata creare una nuova regola in uscita.

2.  Scegliere **Porta**, specificare TCP 1433, quindi **Consenti la connessione** e verificare che sia stato selezionato il profilo **Pubblico**.

3.  Specificare un nome significativo, ad esempio *WindowsAzureSQLDatabase (tcp-out) porta 1433*.

### Effettuare la connessione a un server logico

1.  In Management Studio verificare in Connetti al server che Motore di database sia selezionato, quindi immettere il nome del server logico con il formato seguente: *nomeserver*.database.widnows.net

    È inoltre possibile ottenere il nome completo del server nel portale di gestione, nel dashboard del server e in MANAGE URL.

2.  In Autenticazione scegliere **Autenticazione di SQL Server** e quindi immettere l'account di accesso di amministratore creato durante la configurazione del server logico.

3.  Fare clic su **Opzioni**.

4.  In Connetti al database specificare **master**.

### Effettuare la connessione a un server locale

1.  In Management Studio verificare in Connetti al server che Motore di database sia selezionato, quindi immettere il nome di un'istanza locale con il formato seguente: *nomeserver*\\*nomeistanza*. Se il server è locale e l'istanza è predefinita, immettere *localhost*.

2.  In Autenticazione scegliere **Autenticazione di Windows** e quindi immettere un account Windows appartenente al ruolo sysadmin.

Distribuzione di un database in Azure
-------------------------------------

Sono disponibili diversi approcci per lo spostamento di un database locale di SQL Server in Azure. In questa attività, si utilizzerà la procedura guidata di distribuzione del database al database SQL per il caricamento di un database di esempio.

Il database di esempio School è convenientemente semplice. Tutti i relativi oggetti sono compatibili con il database SQL, eliminando la necessità di modificare o preparare un database per la migrazione. In qualità di nuovo amministratore, prima di iniziare a utilizzare i propri database provare innanzitutto a distribuire un database semplice in modo da apprendere la procedura corretta.

**Nota:** per istruzioni dettagliate su come preparare un database locale per la migrazione in Azure, rivedere la guida alla migrazione dei database SQL. Si prenda inoltre in considerazione il download del Kit di formazione di Azure, che include un lab in cui è illustrato un approccio alternativo alla migrazione di un database locale.

### Creare il database school su un server locale

Gli script per la creazione di questo database sono reperibili nella [guida introduttiva all'amministrazione dei database SQL](http://www.windowsazure.com/en-us/manage/tutorials/sql-azure-management/). In questa guida gli script verranno eseguiti in Management Studio allo scopo di creare una versione locale del database school.

1.  In Management Studio connettersi a un server locale. Fare clic con il pulsante destro del mouse su **Databases** e quindi fare clic su **New Database**, infine immettere *school*.

2.  Fare clic con il pulsante destro del mouse su *school* e quindi fare clic su **New Query**.

3.  Copiare e quindi eseguire lo script Create Schema dall'esercitazione.

<div style="width:auto; height:300px; overflow:auto"><pre>
	-- Create the Department table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
	   	WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
	  	AND type in (N'U'))
   	BEGIN
    CREATE TABLE [dbo].[Department](
		[DepartmentID] [int] NOT NULL,
		[Name] [nvarchar](50) NOT NULL,
		[Budget] [money] NOT NULL,
		[StartDate] [datetime] NOT NULL,
		[Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
	[DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
	GO

	-- Create the Person table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Person](
		[PersonID] [int] IDENTITY(1,1) NOT NULL,
		[LastName] [nvarchar](50) NOT NULL,
		[FirstName] [nvarchar](50) NOT NULL,
		[HireDate] [datetime] NULL,
		[EnrollmentDate] [datetime] NULL,
 	 CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED 	
	(
	[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnsiteCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnsiteCourse](
		[CourseID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Days] [nvarchar](50) NOT NULL,
		[Time] [smalldatetime] NOT NULL,
 	 CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnlineCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnlineCourse](
		[CourseID] [int] NOT NULL,
		[URL] [nvarchar](100) NOT NULL,
 	 CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	--Create the StudentGrade table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[StudentGrade](
		[EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
		[CourseID] [int] NOT NULL,
		[StudentID] [int] NOT NULL,
		[Grade] [decimal](3, 2) NULL,
	 CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
	(
		[EnrollmentID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the CourseInstructor table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[CourseInstructor](
		[CourseID] [int] NOT NULL,
		[PersonID] [int] NOT NULL,
	 CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC,
		[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the Course table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Course](
		[CourseID] [int] NOT NULL,
		[Title] [nvarchar](100) NOT NULL,
		[Credits] [int] NOT NULL,
		[DepartmentID] [int] NOT NULL,
 	 CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Create the OfficeAssignment table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OfficeAssignment](
		[InstructorID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Timestamp] [timestamp] NOT NULL,
 	 CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
	(
		[InstructorID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Define the relationship between OnsiteCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
	ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
	GO

	-- Define the relationship between OnlineCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
	ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
	GO
	-- Define the relationship between StudentGrade and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
	GO

	--Define the relationship between StudentGrade and Student.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))	
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
	GO

	-- Define the relationship between CourseInstructor and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
  	 WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
  	 AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
  	 CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
 	  CONSTRAINT [FK_CourseInstructor_Course];
	GO

	-- Define the relationship between CourseInstructor and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
 	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
	   AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
 	  CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
  	 CONSTRAINT [FK_CourseInstructor_Person];
	GO

	-- Define the relationship between Course and Department.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--Define the relationship between OfficeAssignment and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
 	  AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
	ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
 	  CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[OfficeAssignment] CHECK 
   	 CONSTRAINT [FK_OfficeAssignment_Person];
	GO
</pre></div>

Quindi, copiare ed eseguire lo script Insert Data.

<div style="width:auto; height:300px; overflow:auto"><pre>
	-- Insert data into the Person table.
	SET IDENTITY_INSERT dbo.Person ON;
	GO
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (6, 'Li', 'Yan', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (28, 'White', 'Anthony', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
	GO
	SET IDENTITY_INSERT dbo.Person OFF;
	GO
	-- Insert data into the Department table.
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- Insert data into the Course table.
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1050, 'Chemistry', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1061, 'Physics', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1045, 'Calculus', 4, 7);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2030, 'Poetry', 2, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2021, 'Composition', 3, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2042, 'Literature', 4, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4022, 'Microeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4041, 'Macroeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4061, 'Quantitative', 2, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (3141, 'Trigonometry', 4, 7);
	GO
	-- Insert data into the OnlineCourse table.
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	--Insert data into OnsiteCourse table.
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1050, '123 Smith', 'MTWH', '11:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1061, '234 Smith', 'TWHF', '13:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1045, '121 Smith','MWHF', '15:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4061, '22 Williams', 'TH', '11:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (2042, '225 Adams', 'MTWH', '11:00');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4022, '23 Williams', 'MWF', '9:00');
	-- Insert data into the CourseInstructor table.
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1050, 1);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1061, 31);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1045, 5);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2030, 4);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2021, 27);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2042, 25);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4022, 18);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4041, 32);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4061, 34);
	GO
	--Insert data into the OfficeAssignment table.
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (1, '17 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (4, '29 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (5, '37 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (18, '143 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (25, '57 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (27, '271 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (31, '131 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (32, '203 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (34, '213 Smith');
	-- Insert data into the StudentGrade table.
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 2, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 2, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 3, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 3, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 6, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 6, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 7, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 7, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 9, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 10, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 11, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 15, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 16, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 17, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 19, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 20, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 21, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 22, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 22, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 22, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 23, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 23, 1.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 24, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 25, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 26, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 26, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 27, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 28, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 28, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 29, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 30, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 30, 4);
	GO
</pre></div>


Il database locale appena creato può essere esportato in Azure. In seguito, una procedura guidata consentirà di creare un file .bacpac, caricarlo in Azure e importarlo nel database SQL.

### Distribuire nel database SQL

1.  In Management Studio connettersi a un'istanza di SQL Server locale che dispone di un database di cui si desidera eseguire la migrazione.

2.  Fare clic con il pulsante destro del mouse sul database school appena creato, selezionare **Tasks** e quindi fare clic su **Deploy Database to SQL Database**.

3.  In Deployment Settings immettere un nome per il database, ad esempio *school*.

4.  Fare clic su **Connect**.

5.  In Server name immettere il nome del server composto da 10 caratteri, seguito da .databases.windows.net.

6.  In Authentication selezionare **SQL Server Authentication**.

7.  Immettere il nome di accesso e la password dell'amministratore forniti durante la creazione del server logico del database SQL.

8.  Fare clic su **Opzioni**.

9.  Nella sezione Connection Properties digitare **master** in Connect to database.

10. Fare clic su **Connect**. Con questo passaggio si concludono le specifiche di connessione e l'utente viene riportato alla procedura guidata.

11. Per eseguire la procedura guidata, fare clic su **Next** e quindi fare clic su **Finish**.

### Verificare la distribuzione del database

1.  In Management Studio connettersi al server logico. Se è già aperta una connessione, è possibile chiuderla e aprirne una nuova. La connessione esistente mostra solo i database in esecuzione nel momento in cui è stata stabilita la connessione.

    Per istruzioni sulla connessione a un server logico, vedere [Connessione mediante Management Studio](#PreReq3) in questo documento.

2.  Espandere la cartella Databases. Verrà visualizzato il database school nell'elenco.

3.  Fare clic con il pulsante destro del mouse sul database school e quindi fare clic su **New Query**.

4.  Eseguire la query seguente per verificare l'accessibilità dei dati.

``` {}
    SELECT
        Course.Title as "Course Title"
        ,Department.Name as "Department"
        ,Person.LastName as "Instructor"
        ,OnsiteCourse.Location as "Location"
        ,OnsiteCourse.Days as "Days"
        ,OnsiteCourse.Time as "Time"
    FROM
     Course
     INNER JOIN Department
      ON Course.DepartmentID = Department.DepartmentID
     INNER JOIN CourseInstructor
       ON Course.CourseID = CourseInstructor.CourseID
     INNER JOIN Person
       ON CourseInstructor.PersonID = Person.PersonID
     INNER JOIN OnsiteCourse
        ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
```

Aggiunta di account di accesso e di utenti
------------------------------------------

Dopo la distribuzione di un database, sarà necessario configurare gli account di accesso e assegnare le autorizzazioni. Nel passaggio successivo verranno eseguiti due script.

Per il primo script verrà effettuata la connessione al database master e verrà eseguito uno script per la creazione di account di accesso. Tali account verranno utilizzati per supportare operazioni di lettura e scrittura e per delegare attività operative, ad esempio la possibilità di eseguire query di sistema senza autorizzazioni â€˜saâ€™.

È necessario che gli account di accesso creati siano account di accesso con autenticazione di SQL Server. Se sono già disponibili script predefiniti che utilizzano identità utente di Windows o identità basate sulle attestazioni, tale script non funzionerà nel database SQL.

Il secondo script consente di assegnare autorizzazioni utente database. Per questo script verrà effettuata la connessione a un database già caricato in Azure.

### Creare account di accesso

1.  In Management Studio connettersi a un server logico in Azure, espandere la cartella Databases, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

2.  Utilizzare le istruzioni Transact-SQL seguenti per creare account di accesso. Sostituire il valore di password con una password valida. Selezionare ogni account di accesso individualmente e quindi fare clic su **Esegui**. Ripetere per gli account di accesso rimanenti.

``` {}
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
```

### Creare utenti database

1.  Espandere la cartella Databases, fare clic con il pulsante destro del mouse su **school** e quindi scegliere **Nuova query**.

2.  Utilizzare le istruzioni Transact-SQL seguenti per aggiungere utenti database. Sostituire il valore di password con una password valida.

``` {}
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
```

### Visualizzare e testare gli account di accesso

1.  In una nuova finestra Query effettuare la connessione a **master** ed eseguire l'istruzione seguente:

         SELECT * from sys.sql_logins;

2.  In Management Studio fare clic con il pulsante destro del mouse sul database **school** e quindi scegliere **Nuova query**.

3.  Dal menu Query scegliere **Connessione** e quindi fare clic su **Cambia connessione**.

4.  Accedere come *sqlreader*.

5.  Copiare l'istruzione seguente e provare a eseguirla. Dovrebbe essere visualizzato un errore che indica che l'oggetto non esiste.

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
         VALUES (1061, 30, 9);

6.  Aprire una seconda finestra Query e cambiare il contesto di connessione in *sqlwriter*. Dovrebbe essere ora possibile eseguire correttamente la stessa query.

Sono stati ora creati e testati diversi account di accesso. Per ulteriori informazioni, vedere [Gestione di database e account di accesso in database SQL](http://msdn.microsoft.com/en-us/library/windowsazure/ee336235.aspx) e [Monitoraggio di database SQL mediante le viste a gestione dinamica](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx).

Monitoraggio di server logici e di istanze di database
------------------------------------------------------

Gli strumenti e le tecniche di monitoraggio che si utilizzerebbero abitualmente su un server locale, quali il controllo degli accessi, l'esecuzione di tracce e i contatori di prestazioni, non sono disponibili per il database SQL. In Azure vengono impiegate le viste di gestione dati (DMV, Data Management Views) per monitorare la capacità dei dati, i problemi correlati alle query e le connessioni attuali.

Per ulteriori informazioni, vedere [Monitoraggio di database SQL mediante le viste a gestione dinamica](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx).

Scalabilità di una soluzione di database SQL
--------------------------------------------

In Azure la scalabilità dei database è sinonimo di scalabilità orizzontale, in cui un carico di lavoro viene ridistribuito tra più commodity server in un data center. La scalabilità orizzontale rientra in una strategia tesa ad affrontare i problemi di capacità dei dati o prestazioni, che si rende necessaria per database di dimensioni molto grandi e per cui è prevista una crescita elevata, indipendentemente dal numero di utenti che vi accederanno.

In Azure la soluzione ottimale per implementare la scalabilità orizzontale è rappresentata dalla federazione. La federazione del database SQL è basata sul partizionamento orizzontale, in cui una o più tabelle vengono divise per riga e distribuite tra più membri della federazione.

La federazione non è però l'unica risposta a tutti i problemi di scalabilità. A volte le caratteristiche dei dati o i requisiti delle applicazioni consentono anche approcci più semplici. Nell'elenco seguente sono presentate le possibili soluzioni in ordine di complessità.

**Aumentare le dimensioni del database**

I database vengono creati con dimensioni fisse soggette a un limite massimo imposto da ogni edizione. Per l'edizione Web, è possibile aumentarle fino a un massimo di 5 gigabyte. Per l'edizione Business, le dimensioni massime del database sono di 150 gigabyte. Il modo più ovvio per incrementare la capacità dei dati consiste nel cambiare l'edizione e le dimensioni massime:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**Utilizzare più database e allocare gli utenti**

In scenari limitati è possibile creare copie di un database e quindi allocare account di accesso e utenti per ognuna di esse. Prima che venisse resa disponibile la federazione, era questo l'approccio comune per ridistribuire un carico di lavoro. Questo approccio è fattibile per i database utilizzati per brevi periodi di tempo e in seguito uniti a un database primario mantenuto in locale, oltre che per soluzioni che forniscono dati in sola lettura.

**Utilizzare le federazioni**

Nel database SQL le federazioni vengono utilizzate per raggiungere livelli superiori di scalabilità e prestazioni. Una o più tabelle all'interno di un database vengono divise per riga e distribuite tra più database, ovvero i membri della federazione. Si tratta in sostanza di un partizionamento orizzontale. I principali scenari in cui questo approccio risulta utile sono quelli in cui è necessario ottenere scalabilità e prestazioni oppure gestire la capacità.

Le federazioni sono supportate nell'edizione Business. Per ulteriori informazioni, vedere [Federazioni in database SQL di Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) e [Esercitazione sulle federazioni del database SQL - DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx).

**Valutare altri tipi di archiviazione**

Tenere presente che Azure supporta più tipi di archiviazione dati, tra cui tabelle e BLOB. Se non sono necessarie caratteristiche relazionali, l'archiviazione tabelle o BLOB può risultare più economicamente vantaggiosa.

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'amministrazione di database SQL, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di amministrazione più complesse.

-   Vedere [Database SQL](http://msdn.microsoft.com/en-us/library/windowsazure/gg619386) in MSDN
-   Vedere la pagina relativa al [database SQL in TechNet WIKI](http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx)

