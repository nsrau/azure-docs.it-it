<properties
	pageTitle="Esercitazione: App Web con database multi-tenant che usa Entity Framework e la sicurezza a livello di riga"
	description="Informazioni su come sviluppare un'app Web ASP.NET MVC 5 con un back-end del database SQL multi-tenant che usa Entity Framework e la sicurezza a livello di riga."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
	services="app-service\web"
	documentationCenter=".net"
	manager="jeffreyg"
  authors="tmullaney"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="thmullan"/>

# Esercitazione: App Web con database multi-tenant che usa Entity Framework e la sicurezza a livello di riga

Questa esercitazione illustra come sviluppare un'app Web multi-tenant con un modello di tenancy di tipo "[database condiviso, schema condiviso](https://msdn.microsoft.com/library/aa479086.aspx)" usando Entity Framework e la [sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131.aspx). In questo modello un database singolo contiene dati per molti tenant e ogni riga in ogni tabella è associata a un "ID tenant". La sicurezza a livello di riga, una nuova funzionalità del database SQL di Azure, consente di impedire ai tenant di accedere ai dati degli altri tenant. È necessaria solo una piccola modifica all'applicazione. Centralizzando la logica di accesso al tenant entro il database stesso, la sicurezza a livello di riga semplifica il codice dell'applicazione e riduce il rischio di diffusione accidentale dei dati tra i tenant.

È possibile iniziare con la semplice applicazione Contact Manager disponibile in [Creare un'app ASP.NET MVC con autenticazione e database SQL e distribuirla nel servizio app di Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). L'applicazione consente attualmente a tutti gli utenti (tenant) di visualizzare tutti i contatti:

![Applicazione Contact Manager prima dell'abilitazione della sicurezza a livello di riga](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Con qualche modifica è possibile aggiungere il supporto per il multi-tenancy, in modo che gli utenti possano visualizzare solo i propri contatti.

## Passaggio 1: Aggiungere una classe Interceptor nell'applicazione per configurare SESSION\_CONTEXT

È necessario apportare una modifica all'applicazione. Poiché tutti gli utenti dell'applicazione si connettono al database usando la stessa stringa di connessione, ovvero lo stesso accesso SQL, non è attualmente possibile per un criterio di sicurezza a livello di riga conoscere l'utente in base a cui applicare il filtro. Questo approccio è molto comune in applicazioni Web perché consente il pooling efficiente delle connessioni, ma richiede un altro modo per identificare l'utente attuale dell'applicazione Web nel database. La soluzione consiste nel fare in modo che l'applicazione configuri una coppia chiave-valore per l'UserId corrente in [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806) immediatamente dopo l'apertura della connessione e prima dell'esecuzione di query. SESSION\_CONTEXT è un archivio di coppie chiave-valore con ambito sessione e il criterio della sicurezza a livello di riga userà il valore UserId archiviato per identificare l'utente corrente.

Verrà aggiunto un [intercettore](https://msdn.microsoft.com/data/dn469464.aspx), in particolare [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor), una nuova funzionalità di Entity Framework (EF) 6, per configurare automaticamente il valore UserId attuale in SESSION\_CONTEXT eseguendo un'istruzione T-SQL ogni volta che EF apre una connessione.

1.	Aprire il progetto ContactManager in Visual Studio.
2.	Fare clic con il pulsante destro del mouse sulla cartella Modelli in Esplora soluzioni, quindi scegliere Aggiungi > Classe.
3.	Assegnare alla nuova classe il nome "SessionContextInterceptor.cs" e fare clic su Aggiungi.
4.	Sostituire i contenuti di SessionContextInterceptor.cs con il codice seguente.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        	// Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

È necessaria solo una modifica all'applicazione. Sviluppare e pubblicare l'applicazione.

## Passaggio 2: Aggiungere una colonna UserId allo schema del database

È ora necessario aggiungere una colonna UserId alla tabella Contacts per associare ogni riga a un utente (tenant). Lo schema verrà modificato direttamente nel database, quindi non è necessario includere questo campo nel modello di dati EF.

Connettersi direttamente al database, usando SQL Server Management Studio o Visual Studio, quindi eseguire l'istruzione T-SQL seguente:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Una colonna UserId verrà aggiunta alla tabella Contacts. Usare il tipo di dati nvarchar(128) per la corrispondenza con i valori UserId archiviati nella tabella AspNetUsers, quindi creare un vincolo DEFAULT che configurerà automaticamente l'UserId per le righe appena inserite in modo che corrisponda all'UserId attualmente archiviato in SESSION\_CONTEXT.

La tabella avrà un aspetto analogo al seguente:

![Tabella Contacts SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Quando vengono creati, ai nuovi contatti viene assegnato automaticamente il valore UserId corretto. Per finalità di demo, assegnare tuttavia alcuni dei contatti esistenti a un utente esistente.

Se nell'applicazione sono già stati creati utenti, ad esempio tramite account locali, Google o Facebook, sarà possibile visualizzarli nella tabella AspNetUsers. Nella schermata seguente è attualmente presente solo un utente.

![Tabella AspNetUsers SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Copiare l'ID per user1@contoso.com e incollarlo nell'istruzione T-SQL seguente. Eseguire questa istruzione per associare tre contatti con l'UserId.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## Passaggio 3: Creare un criterio di sicurezza a livello di riga nel database

Il passaggio finale consiste nel creare un criterio di sicurezza che usa il valore UserId in SESSION\_CONTEXT per filtrare automaticamente i risultati restituiti dalle query.

Mentre si è connessi al database, eseguire l'istruzione T-SQL seguente:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
	RETURNS TABLE
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS accessResult
	WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
	ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
	ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Questo codice esegue tre operazioni. Prima di tutto crea un nuovo schema, come procedura consigliata per centralizzare e limitare l'accesso agli oggetti del criterio di sicurezza a livello di riga. Quindi crea una funzione predicato che restituirà '1' quando il valore UserId di una riga corrisponde all'UserId in SESSION\_CONTEXT. Crea infine un criterio di sicurezza che aggiunge questa funzione come filtro e come predicato di blocco nella tabella Contacts. Il predicato di filtro provoca la restituzione da parte delle query solo delle righe appartenenti all'utente corrente e il predicato di blocco funge da protezione per evitare che l'applicazione inserisca accidentalmente una riga per l'utente errato.

Eseguire l'applicazione e accedere come user1@contoso.com. L'utente vede solo i contatti assegnati a questo UserId in precedenza:

![Applicazione Contact Manager prima dell'abilitazione della sicurezza a livello di riga](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Per una convalida aggiuntiva, provare a registrare un nuovo utente. Il nuovo utente non vedrà alcun contatto, perché nessun contatto è stato assegnato a questo utente. Se l'utente crea un nuovo contatto, questo contatto gli verrà assegnato e potrà visualizzarlo.

## Passaggi successivi

L'operazione è terminata. La semplice app Web Contact Manager è stata convertita in un multi-tenant in cui ogni utente ha il proprio elenco contatti. Usando la sicurezza a livello di riga si evita la complessità derivante dall'applicare la logica di accesso al tenant nel codice dell'applicazione. Questa trasparenza consente all'applicazione di concentrarsi sul problema aziendale effettivo e riduce anche il rischio di divulgazione accidentale dei dati con la crescita della codebase dell'applicazione.

Questa esercitazione offre solo un'idea delle possibilità della sicurezza a livello di riga. È ad esempio possibile avere una logica di accesso più avanzata o granulare e archiviare più del solo valore UserId corrente in SESSION\_CONTEXT. È anche possibile [integrare la sicurezza a livello di riga con le librerie client del database elastico](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) per supportare partizioni multi-tenant in un livello dati con scalabilità orizzontale.

Microsoft si impegna anche a migliorare continuamente la sicurezza a livello di riga. In caso di domande, idee o suggerimenti, inviare commenti. I commenti e suggerimenti degli utenti sono molto apprezzati.

<!---HONumber=AcomDC_0427_2016-->