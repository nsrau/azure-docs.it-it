<properties
   pageTitle="Procedura dettagliata per la creazione del connettore Generic SQL | Microsoft Azure"
   description="Questo articolo presenta la procedura dettagliata per la creazione di un semplice sistema HR mediante il connettore Generic SQL."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="andkjell"/>

# Procedura dettagliata per la creazione del connettore Generic SQL
Questo argomento è una guida dettagliata. Verrà creato un semplice database delle risorse umane di esempio che sarà usato per importare alcuni utenti con la relativa appartenenza ai gruppi.

## Preparare il database di esempio
In un server che esegue SQL Server avviare lo script SQL disponibile nell’[Appendice A](#appendix-a). Lo script crea un database di esempio con il nome GSQLDEMO. Il modello a oggetti per il database creato sarà simile a questa immagine: ![Modello a oggetti](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Creare anche un utente da usare per connettersi al database. In questa procedura dettagliata l'utente si chiama FABRIKAM\\SQLUser e si trova nel dominio.

## Creare il file di connessione ODBC
Il connettore Generic SQL usa ODBC per connettersi al server remoto. È necessario innanzitutto creare un file con le informazioni di connessione ODBC.

1. Avviare l'utilità di gestione di ODBC sul server: ![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Selezionare la scheda **DSN su file**. Fare clic su **Aggiungi**. ![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Il driver predefinito è adeguato allo scopo, quindi selezionarlo e fare clic su **Avanti>**. ![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Assegnare un nome al file, ad esempio **GenericSQL**. ![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Fare clic su **Fine**.![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. È ora necessario configurare la connessione. Assegnare una descrizione appropriata all'origine dati e specificare il nome del server che esegue SQL Server. ![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Selezionare la modalità di autenticazione con SQL. In questo caso si userà l'autenticazione di Windows. ![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Specificare il nome del database di esempio, **GSQLDEMO**. ![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. In questa schermata mantenere tutte le selezioni predefinite. Fare clic su **Fine**.![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Per verificare che tutto funzioni come previsto, fare clic su **Verifica origine dati**. ![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Assicurarsi che la verifica abbia esito positivo. ![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. Il file di configurazione ODBC dovrebbe essere ora visibile in DSN su file. ![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Il file necessario è ora disponibile e si può iniziare a creare il connettore.

## Creare il connettore Generic SQL

1. Nell'interfaccia utente di Synchronization Service Manager selezionare **Connectors** (Connettori) e **Create** (Crea). Selezionare **Generic SQL (Microsoft)** e assegnargli un nome descrittivo. ![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Trovare il file DSN creato nella sezione precedente e caricarlo nel server. Immettere le credenziali per connettersi al database. ![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. In questa procedura dettagliata si considera un caso semplificato in cui esistono due tipi di oggetti, **User** e **Group**. ![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Il connettore dovrà trovare gli attributi esaminando la tabella. Poiché **Users** è una parola riservata in SQL, occorre specificarla fra parentesi quadre [ ]. ![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. È ora necessario definire l'attributo di ancoraggio e l'attributo DN. Per **Users** si usa la combinazione dei due attributi Username ed EmployeeID. Per **Group** si usa GroupName, non molto plausibile in un caso reale, ma adeguato per questa procedura dettagliata. ![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Non tutti i tipi di attributo possono essere rilevati in un database SQL. In particolare, non può essere rilevato il tipo di attributo di riferimento. Per il tipo di oggetto Group è necessario cambiare OwnerID e MemberID a cui fare riferimento. ![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Gli attributi selezionati come attributi di riferimento nel passaggio precedente richiedono il tipo di oggetto cui questi valori fanno riferimento. In questo caso si tratta del tipo di oggetto User. ![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Nella pagina Global Parameters (Parametri globali) selezionare **Watermark** (Limite) come strategia differenziale. Digitare anche il formato di data/ora **yyyy-MM-dd HH:mm:ss** (aaaa-MM-gg HH:mm:ss). ![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Nella pagina **Configure Partitions and Hierarchies** (Configura partizioni e gerarchie) selezionare entrambi i tipi di oggetto. ![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. In **Select Object Types** (Seleziona tipi di oggetto) e **Select Attributes** (Seleziona attributi) selezionare entrambi i tipi di oggetto e tutti gli attributi. Nella pagina **Configure Anchors** (Configura ancoraggi) fare clic su **Finish** (Fine).

## Creare i profili di esecuzione

1. Nell'interfaccia utente di Synchronization Service Manager selezionare **Connectors** (Connettori) e **Configure Run Profiles** (Configura profili di esecuzione). Fare clic su **New Profile** (Nuovo profilo). Si inizierà con **Full Import** (Importazione completa). ![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Selezionare il tipo **Full Import (Stage Only)** (Importazione completa - solo staging). ![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Selezionare la partizione **OBJECT=User**. ![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Selezionare **Table** (Tabella) e digitare **[USERS]**. Scorrere fino alla sezione del tipo di oggetto multivalore e immettere i dati riportati di seguito. Selezionare **Finish** (Fine) per salvare il passaggio. ![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png) ![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)
5. Selezionare **New Step** (Nuovo passaggio). Questa volta selezionare **OBJECT=Group**. Nell'ultima pagina usare la configurazione illustrata nell'immagine. Fare clic su **Finish** (Fine). ![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)
6. Facoltativo: se si vuole, è possibile configurare altri profili di esecuzione. In questa procedura dettagliata si usa solo l'importazione completa.
7. Fare clic su **OK** per terminare la modifica dei profili di esecuzione.

## Aggiungere alcuni dati di prova e testare l'importazione
Immettere alcuni dati di prova nel database di esempio. Quando si è pronti, selezionare **Run** (Esegui) e **Full import** (Importazione completa).

In questo esempio si ottiene un utente con due numeri di telefono e un gruppo con alcuni membri. ![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png) ![cs2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)

## Appendice A
**Script SQL per la creazione del database di esempio**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
	[MemberID] [int] NOT NULL,
	[Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
	[GroupID] [int] NOT NULL,
	[GROUPNAME] [nvarchar](200) NOT NULL,
	[DESCRIPTION] [nvarchar](200) NULL,
	[WATERMARK] [datetime] NULL,
	[OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
	[USER_ID] [int] NULL,
	[Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
	[USERID] [int] NOT NULL,
	[USERNAME] [nvarchar](200) NOT NULL,
	[FirstName] [nvarchar](100) NULL,
	[LastName] [nvarchar](100) NULL,
	[DisplayName] [nvarchar](100) NULL,
	[ACCOUNTDISABLED] [bit] NULL,
	[EMPLOYEEID] [int] NOT NULL,
	[WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
	[USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```

<!---HONumber=AcomDC_0928_2016-->