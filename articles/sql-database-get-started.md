<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create and provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="loclar"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="loclar"></tags>

# Introduzione al database SQL di Microsoft Azure

Questa esercitazione illustra le nozioni fondamentali relative all'amministrazione del database SQL di Microsoft Azure tramite il portale di gestione di Azure. Se non si è esperti di amministrazione di database, seguendo queste lezioni si potranno apprendere le competenze essenziali in circa 30 minuti.

Per questa esercitazione non è infatti necessario conoscere già SQL Server o il database SQL di Azure. Al termine dell'esercitazione si disporrà di un database di esempio su Azure e si avranno le informazioni necessarie per eseguire attività di amministrazione di base tramite il portale di gestione.

L'esercitazione prevede la creazione e il provisioning di un database di esempio sulla piattaforma di Azure e l'esecuzione di query su dati utente e di sistema mediante Excel.

## Sommario

-   [Passaggio 1: Creare un account Microsoft Azure](#Subscribe)
-   [Passaggio 2: Connettersi ad Azure e creare un database](#Subscribe)
-   [Passaggio 3: Configurare il firewall](#ConfigFirewall)
-   [Passaggio 4: Aggiungere dati e uno schema tramite lo script Transact-SQL](#AddData)
-   [Passaggio 5: Creare lo schema](#createschema)
-   [Passaggio 6: Inserire dati](#insertData)
-   [Passaggio 7: Eseguire query su dati di esempio e di sistema nel portale di gestione per il database SQL](#QueryDBSysData)
-   [Passaggio 8: Creare un account di accesso al database e assegnare le autorizzazioni](#DBLogin)
-   [Passaggio 9: Connettersi da altre applicazioni](#ClientConnection)

## Passaggio 1: Creare un account Microsoft Azure

1.  Aprire un Web browser e passare al sito [http://azure.microsoft.com](http://azure.microsoft.com)
    Per iniziare usando un account gratuito, fare clic su Versione di valutazione gratuita nell'angolo in alto a destra e seguire le istruzioni visualizzate.

2.  Verrà creato l'account e sarà possibile iniziare.

## Passaggio 2: Connettersi ad Azure e creare un database

1.  Accedere al [portale di gestione](http://manage.windowsazure.com). Verrà visualizzato un pannello di navigazione simile al seguente.

    ![Pannello di navigazione][Image1]

2.  Fare clic su **New** nella parte inferiore della pagina. Quando si fa clic su **New**, viene visualizzato un elenco degli elementi che è possibile creare.

3.  Fare clic su **SQL Database** e quindi su **Custom Create**.

    ![Pannello di navigazione][Image2]

Questa opzione consente di creare contemporaneamente un nuovo server e un database SQL di cui si è amministratore. In qualità di amministratore di sistema, è possibile eseguire altre attività, tra cui la connessione al portale di gestione per il database SQL, che verranno eseguite più avanti nel corso di questa esercitazione.

1.  Quando si fa clic su **Custom Create**, viene visualizzata la pagina Database Settings, in cui specificare informazioni di base per la creazione di un database SQL vuoto nel server. L'aggiunta di tabelle e dati verrà effettuata più avanti.

    Completare la pagina Database Settings come illustrato nella figura seguente:

    ![Pannello di navigazione][Image3]

-   Immettere **School** come nome del database.

-   Utilizzare le impostazioni predefinite per edizione, dimensione massima e regole di confronto.

-   Scegliere **New SQL Database Server**. Selezionando un nuovo server, verrà aggiunta una nuova pagina in cui impostare l'account e l'area dell'amministratore.

-   Dopo aver inserito i dati, fare clic sulla freccia per passare alla pagina successiva.

1.  Completare la pagina Server Settings come illustrato nella figura seguente:

    ![Pannello di navigazione][Image4]

-   Immettere un nome di amministratore composto da una sola parola senza spazi. Per convalidare l'identità degli utenti nel database SQL viene utilizzata l'autenticazione SQL tramite una connessione crittografata. Con il nome specificato verrà creato un nuovo account di accesso con autenticazione di SQL Server che dispone di autorizzazioni di amministratore. Il nome dell'amministratore non può essere né un utente Windows né un Windows Live ID. L'autenticazione di Windows non è infatti supportata nel database SQL.

-   Impostare una password complessa composta da più di otto caratteri, utilizzando una combinazione di caratteri in minuscolo e maiuscolo e da un numero o un simbolo. Per informazioni sulla complessità della password usare la finestra della Guida.

-   Scegliere un'area, che determina la posizione geografica del server. Poiché le aree non possono essere cambiate facilmente, sceglierne una significativa per questo server. Selezionare la posizione più vicina. Per ridurre i costi relativi alla larghezza di banda in uscita e la latenza dei dati, è consigliabile mantenere il database e l'applicazione Azure nella stessa area.

-   Assicurarsi che la casella di controllo **Allow Azure Services to access this server** sia selezionata in modo da consentire la connessione al database tramite il portale di gestione per il database SQL, Excel in Office 365 o la funzionalità di report SQL di Azure.

-   Al termine, fare clic sul segno di spunta nella parte inferiore della pagina.

Si noti che non è stato specificato un nome di server. Poiché il server di database SQL deve essere accessibile in tutto il mondo, le voci DNS appropriate vengono configurate durante la creazione del server. Il nome generato garantisce l'assenza di conflitti tra nomi con altre voci DNS. Il nome del server di database SQL non è modificabile.

Per visualizzare il nome del server che ospita il database **School** appena creato, fare clic su **Database SQL** nel pannello di navigazione sinistro, quindi fare clic sul database **School** nella visualizzazione elenco **Database SQL**. Nella pagina **Avvio rapido** scorrere verso il basso per visualizzare il nome del server.

Nel passaggio successivo si configurerà il firewall in modo da consentire alle connessioni da applicazioni in esecuzione nel computer di accedere ai database sul server di database SQL.

## Passaggio 3: Configurare il firewall

Per configurare il firewall in modo da consentire l'accesso alle connessioni, sarà necessario immettere informazioni nella pagina del server.

**Nota:** il servizio del database SQL è disponibile solo con la porta TCP 1433 utilizzata dal protocollo TDS, pertanto assicurarsi che il firewall nella rete e nel computer locale consenta le comunicazioni TCP in uscita sulla porta 1433. Per ulteriori informazioni, vedere la pagina relativa al [firewall del database SQL](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-it-it.aspx).

1.  Nel pannello di navigazione a sinistra fare clic su **SQL Databases**.

2.  Fare clic su **Servers** nella parte superiore della pagina. Fare quindi clic sul server appena creato per aprire la pagina del server.

3.  Nella pagina del server fare clic su **Configura** per aprire le impostazioni di **Indirizzi IP consentiti**, quindi fare clic sul collegamento **Aggiungere agli indirizzi IP consentiti**. Verrà così creata una nuova regola del firewall per consentire le richieste di connessione inviate dal router o dal server proxy sul quale è in ascolto il dispositivo.

4.  È possibile creare altre regole del firewall specificando un nome di regola e i valori iniziale e finale dell'intervallo di indirizzi IP.

5.  Per consentire le interazioni tra questo server e altri servizi di Azure, fare clic su **Sì** per l'opzione **Microsoft Azure Services**.

6.  Per salvare le modifiche, fare clic su **SALVA** nella parte inferiore della pagina.

7.  Dopo aver salvato la regola, verrà visualizzata una pagina simile a quella della figura seguente.

    ![Pannello di navigazione][Image7]

A questo punto si dispone di un server di database SQL in Azure, di una regola firewall che abilita l'accesso al server, di un oggetto di database e di un account di accesso amministratore, ma non è disponibile un database di lavoro sul quale eseguire query. A tale fine, il database deve avere uno schema e i dati effettivi.

Poiché questa esercitazione prevede l'uso degli strumenti più comuni, si userà la finestra Query nel portale di gestione per consentire al database SQL di eseguire lo script Transact-SQL per la creazione di un database predefinito.

Andando avanti può essere opportuno valutare modi alternativi per creare un database, tra cui l'approccio a livello di codice o la finestra di progettazione in SQL Server Data Tools. Se si dispone già di un database SQL Server che viene eseguito in un server locale, è possibile eseguirne facilmente la migrazione al server Azure appena configurato. Per informazioni, utilizzare i collegamenti alla fine di questa esercitazione.

## Passaggio 4: Aggiungere dati e uno schema tramite lo script Transact-SQL

In questo passaggio vengono eseguiti due script. Il primo consente di creare uno schema per definire tabelle, colonne e relazioni, mentre il secondo consente di aggiungere i dati. Ogni passaggio viene eseguito in modo indipendente utilizzando una diversa connessione. Se si sono già creati database in SQL Server, una delle differenze che si noteranno è che nel database SQL i comandi CREATE e INSERT devono essere eseguiti in batch separati. Questo requisito imposto dal database SQL consente di ridurre al minimo gli attacchi ai dati mentre sono in transito.

**Nota:** lo schema e i valori dei dati sono tratti da questo [articolo di MSDN](http://msdn.microsoft.com/it-it/library/windowsazure/ee621790.aspx "articolo di MSDN") e sono stati modificati per l'utilizzo con il database SQL.

1.  Passare alla home page. Nel [portale di gestione](http://manage.windowsazure.com) il database **School** è incluso nell'elenco degli elementi della home page.

    ![Pannello di navigazione][Image8]

2.  Fare clic su **School** per selezionarlo, quindi fare clic su **Gestisci** nella parte inferiore della pagina. Verrà aperto il portale di gestione per il database SQL. Tale portale è diverso rispetto al portale di gestione di Azure e verrà utilizzato per eseguire comandi e query Transact-SQL.

3.  Immettere il nome di accesso e la password dell'amministratore per accedere al database **School**. Si tratta dell'account di accesso dell'amministratore specificato durante la creazione del server.

4.  Nel portale di gestione per il database SQL fare clic su **Nuova query** sulla barra multifunzione. Nell'area di lavoro verrà aperta una finestra Query vuota. Nel passaggio successivo si utilizzerà questa finestra per copiare una serie di script predefiniti che consentiranno di aggiungere la struttura e i dati al database vuoto.

## Passaggio 5: Creare lo schema

In questo passaggio si creerà lo schema utilizzando lo script seguente. Lo script verifica innanzitutto l'esistenza di una tabella con lo stesso nome per evitare che non si verifichino conflitti di nomi, quindi crea la tabella utilizzando l'istruzione [CREATE TABLE](http://msdn.microsoft.com/it-it/library/windowsazure/ee336258.aspx). Lo script utilizza più avanti l'istruzione [ALTER TABLE](http://msdn.microsoft.com/it-it/library/windowsazure/ee336286.aspx) per specificare le relazioni tra chiavi primarie e tabelle.

Copiare lo script e incollarlo nella finestra Query. Fare clic su **Run** nella parte superiore della finestra per eseguire lo script.

<div style="width:auto; height:600px; overflow:auto"><pre>
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

## Passaggio 6: Inserire dati

Aprire una nuova finestra Query e quindi incollare lo script seguente. Eseguire lo script per inserire dati. Per aggiungere i valori delle singole colonne, in questo script viene utilizzata l'istruzione [INSERT](http://msdn.microsoft.com/it-it/library/windowsazure/ee336284.aspx).

<div style="width:auto; height:600px; overflow:auto"><pre>
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
    
</pre></div>

## Passaggio 7: Eseguire query su dati di esempio e di sistema nel portale di gestione per il database SQL

A scopo di verifica, eseguire una query che restituisca i dati appena immessi. È inoltre possibile eseguire viste di gestione dati e stored procedure incorporate che forniscono informazioni sui database in esecuzione sul server di database SQL.

#### Eseguire query sui dati di esempio

In una nuova finestra Query copiare ed eseguire il seguente script Transact-SQL per recuperare alcuni dei dati appena aggiunti.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECT * From Person
</pre></div>

Verrà visualizzato un set di risultati con 34 righe provenienti dalla tabella person, inclusi PersonID, LastName, FirstName, HireDate e EnrollmentDate.

#### Eseguire query sui dati di sistema

È anche possibile utilizzare viste di sistema e stored procedure incorporate per ottenere informazioni dal server. Ai fini di questa esercitazione, si proverà ad eseguire solo alcuni comandi.

Eseguire il comando seguente per individuare i database disponibili sul server.

    SELECT * FROM sys.databases  

Eseguire questo comando per restituire un elenco degli utenti attualmente connessi al server.

    SELECT user_name(),suser_sname()

Eseguire questa stored procedure per restituire un elenco di tutti gli oggetti del database **School**.

    EXEC SP_help

Non chiudere la connessione del portale al database **School** perché sarà nuovamente necessaria tra qualche minuto.

## Passaggio 8: Creare un account di accesso al database e assegnare le autorizzazioni

Nel database SQL è possibile utilizzare Transact-SQL per creare account di accesso e concedere autorizzazioni. In questa lezione si utilizzerà Transact-SQL per eseguire tre operazioni,

1.  Creare un account di accesso con autenticazione di SQL Server
2.  Creare un utente del database e
3.  Concedere autorizzazioni tramite l'appartenenza al ruolo.

Per le connessioni server viene utilizzato un account di accesso con autenticazione di SQL Server. Per accedere a un database su un server di database SQL, tutti gli utenti devono fornire nome e password di un account di accesso con autenticazione di SQL Server.

Per creare un account di accesso, è prima necessario connettersi al database **master**.

#### Creare un account di accesso con autenticazione di SQL Server

1.  Nel [portale di gestione](http://manage.windowsazure.com) selezionare **Database SQL**, fare clic su **Server**, scegliere il server e quindi fare clic sulla freccia bianca per aprire
    la pagina del server.

2.  Nella pagina Avvio rapido fare clic su **Gestisci server** per aprire una nuova connessione al portale di gestione per il database SQL.

3.  Specificare **master** per il database con il quale stabilire la connessione, quindi accedere con nome utente e password. Si tratta dell'account di accesso dell'amministratore specificato durante la creazione del server.

4.  Il portale di gestione del database SQL verrà aperto in una nuova finestra del browser e verrà stabilita la connessione a **master**.

5.  Ignorare un eventuale errore nella pagina simile a quello della figura seguente. Fare clic su **New Query** per aprire una finestra Query che consente di eseguire comandi Transact-SQL sul database **master**.

    ![Pannello di navigazione][Image15]

6.  Copiare e incollare il seguente comando nella finestra Query.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Eseguire il comando per creare un nuovo account di accesso di SQL Server denominato 'SQLDBLogin'.

#### Creare un utente del database e assegnare le autorizzazioni

Dopo aver creato un account di accesso con autenticazione di SQL Server, il passaggio successivo consiste nell'assegnare il database e i livelli di autorizzazione associati all'account. A tale scopo, è necessario creare un **utente database** per ogni database.

1.  Tornare alla pagina del portale di gestione database SQL per la connessione al database **School**. Se la finestra del browser è stata chiusa, eseguire la procedura della lezione precedente "Aggiungere dati e uno schema tramite lo script Transact-SQL" per avviare una nuova connessione al database **School**.

    Nella pagina del portale di gestione database SQL il nome del database **School** è visibile nell'angolo in alto a sinistra.

    ![Pannello di navigazione][Image12]

2.  Fare clic su **New Query** per aprire una nuova finestra Query e copiarvi l'istruzione seguente.

        CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Eseguire lo script. Questo script consente di creare un utente database in base all'account di accesso.

   In seguito si assegneranno le autorizzazioni utilizzando il ruolo db\_datareader. Gli utenti database assegnati a questo ruolo possono leggere tutti i dati da tutte le tabelle utente del database.

1.  Aprire una nuova finestra Query e quindi eseguire l'istruzione successiva. Tale istruzione consente di eseguire una stored procedure incorporata che assegna il ruolo db\_datareader al nuovo utente appena creato.

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Si dispone ora di un nuovo account di accesso con autenticazione di SQL Server che dispone di autorizzazione di sola lettura per il database **School**. Utilizzare questa procedura per creare altri account di accesso con autenticazione di SQL Server e consentire livelli di accesso diversi ai dati.

## Passaggio 9: Connettersi da altre applicazioni

Una volta creato un database operativo, è possibile connettersi ad esso da una cartella di lavoro di Excel.

#### Connettersi da Excel

Se nel computer è installato Microsoft Excel, è possibile usare la procedura seguente per connettersi al database di esempio.

1.  In Excel fare clic su **Da altre origini** nella scheda Dati e quindi scegliere **Da SQL Server**.

2.  Nella Connessione guidata dati immettere il nome di dominio completo del server di database SQL, seguito da un account di accesso con autenticazione di SQL Server che dispone dell'autorizzazione per accedere al database.

   È possibile trovare il nome del server nel portale di gestione di Azure, nel database SQL, nella pagina Server, nel dashboard, in **Gestisci URL**. Il nome del server è costituito da una serie di lettere e numeri, seguiti da '.database.windows.net'. Specificare tale nome nella Connessione guidata dati senza includere http:// o https://.

   Immettere un account di accesso con autenticazione di SQL Server. A scopo di test, è possibile utilizzare l'account di accesso dell'amministratore creato durante la configurazione del server. Per il normale accesso ai dati utilizzare un account di accesso utente database simile a quello appena creato.

![Pannello di navigazione][Image16]

1.  Nella pagina successiva selezionare il database **School**, quindi scegliere **Person**. Fare clic su **Fine**. Se vengono richiesti i dati di accesso, digitarli e quindi fare clic su **OK**.

2.  Verrà visualizzata la finestra di dialogo Importa dati in cui si chiede di selezionare la modalità e la destinazione di importazione dei dati. Non modificare le opzioni predefinite selezionate e fare clic su **OK**.

    ![Pannello di navigazione][Image19]

3.  Nel foglio di lavoro verrà visualizzata una tabella con un set di risultati 34 righe provenienti dalla tabella person, inclusi PersonID, LastName, FirstName, HireDate e EnrollmentDate, proprio come i risultati della query indicati nel passaggio 7.

Se si utilizza solo Excel, è possibile importare una sola tabella alla volta. È quindi preferibile utilizzare il componente aggiuntivo PowerPivot per Excel, che consente di importare e lavorare con più tabelle come se fossero un singolo set di dati. In questa esercitazione non è previsto l'uso di PowerPivot, ma è possibile ottenere altre informazioni al riguardo in questo argomento relativo a [PowerPivot per Excel](http://go.microsoft.com/fwlink/?LinkId=396969).

## Passaggi successivi

A questo punto, dopo aver acquisito familiarità con il database SQL e i portali di gestione, è possibile sperimentare altri strumenti e tecniche utilizzati dagli amministratori di database di SQL Server.

Per gestire attivamente il nuovo database, provare a installare e utilizzare SQL Server Management Studio, il principale strumento di amministrazione dei database per gestire database SQL Server, inclusi quelli in esecuzione in Azure. Management Studio consente di salvare le query per poterle riutilizzare in futuro, aggiungere nuove tabelle e stored procedure, nonché affinare le competenze relative a Transact-SQL in un ambiente di scripting complesso che include correttore sintattico, IntelliSense e modelli. Per iniziare, attenersi alle istruzioni nella pagina relativa alla [gestione di database SQL con SQL Server Management Studio](http://www.azure.microsoft.com/it-it/documentation/articles/sql-database-manage-azure-ssms/).

La padronanza del linguaggio di definizione dati e di query Transact-SQL è fondamentale per gli amministratori di database. Se non si conosce Transact-SQL, è possibile iniziare con [Esercitazione: Scrittura di istruzioni Transact-SQL](http://msdn.microsoft.com/it-it/library/ms365303.aspx) per ulteriori informazioni su alcune competenze di base.

Sono disponibili altri metodi per spostare un database locale nel database SQL. Se si dispone di database esistenti o sono stati scaricati database di esempio per fare pratica, provare con gli approcci alternativi seguenti:

-   [Migrazione di database nel database SQL](http://msdn.microsoft.com/it-it/library/windowsazure/ee730904.aspx)
-   [Copia di database nel database SQL](http://msdn.microsoft.com/it-it/library/windowsazure/ff951624.aspx)
-   [Distribuire un database di SQL Server a una macchina virtuale di Azure](http://msdn.microsoft.com/it-it/library/dn195938(v=sql.120).aspx)


[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG