<properties
   pageTitle="Autenticazione e autorizzazione per database SQL: concessione dell'accesso | Microsoft Azure"
   description="Informazioni sulla gestione della sicurezza del database SQL, in particolare la modalità di gestione dell'accesso al database e la sicurezza degli account di accesso tramite l'account delle entità di sicurezza a livello di server."
   keywords="sicurezza del database sql, gestione della sicurezza del database, sicurezza degli account di accesso, sicurezza del database, accesso al database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/06/2016"
   ms.author="rickbyh"/>

# Autenticazione e autorizzazione per database SQL: concessione dell'accesso 


> [AZURE.SELECTOR]
- [Esercitazione introduttiva](sql-database-get-started-security.md)
- [Concedere l'accesso](sql-database-manage-logins.md)


Di seguito è disponibile una panoramica dei concetti dell'accesso al database SQL per amministratori, utenti non amministratori e ruoli.

## Account amministrativi senza restrizioni

Esistono due possibili account amministrativi con autorizzazioni senza restrizioni per l'accesso al database master virtuale e a tutti i database utente. Tali account sono denominati account di entità di livello server.

### Account sottoscrittore del database SQL di Azure 

Quando viene creata un'istanza logica di SQL, viene creato un singolo account di accesso, denominato account sottoscrittore del database SQL. Tale account, che effettua la connessione con l'autenticazione di SQL Server (nome utente e password), è un amministratore nell'istanza logica del server e in tutti i database utente collegati a tale istanza. Le autorizzazioni dell'account sottoscrittore non possono essere soggette a restrizioni. Può esistere un solo account di questo tipo.

### Amministratore di Azure Active Directory
È possibile configurare come amministratore anche un account di Azure Active Directory. Tale account può essere un singolo utente di Azure AD oppure un gruppo di Azure AD contenente diversi utenti di Azure AD. La configurazione di un amministratore di Azure AD è facoltativa, ma è necessaria se si vuole usare l'autenticazione di Windows per gli account di Azure AD per la connessione al database SQL. Per altre informazioni sulla configurazione dell'accesso con Azure Active Directory, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

### Configurazione del firewall
Quando è configurato un firewall a livello di server, l'account sottoscrittore del database SQL di Azure e l'account di Azure Active Directory possono connettersi al database master virtuale e a tutti i database utente. Il firewall a livello di server può essere configurato con il portale. Dopo che è stata stabilita una connessione, è possibile configurare anche regole aggiuntive del firewall a livello di server con l'istruzione Transact-SQL [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx). Per altre informazioni sulla configurazione del firewall, vedere [Procedura: Configurare un firewall del database SQL di Azure con il portale di Azure](sql-database-configure-firewall-settings.md).

### Percorso di accesso degli amministratori

Quando il firewall a livello di server è configurato correttamente, l'account sottoscrittore del database SQL e gli amministratori di SQL Server di Azure Active Directory possono connettersi usando strumenti client come SQL Server Management Studio o SQL Server Data Tools. Solo gli strumenti più recenti offrono tutte le caratteristiche e le funzionalità. Il diagramma seguente illustra una configurazione tipica per i due account amministratore. ![Percorso di accesso degli amministratori](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Quando viene usata una porta aperta nel firewall a livello di server, gli amministratori possono connettersi a qualsiasi database SQL.

### Connettersi a un database con SQL Server Management Studio
Per una procedura dettagliata per la connessione con SQL Server Management Studio, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md).


> [AZURE.IMPORTANT] È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Account speciali aggiuntivi
Il database SQL offre due ruoli amministrativi con restrizioni nel database master virtuale a cui possono essere aggiunti account utente.

### Autori di database
Gli account amministrativi possono creare nuovi database. Per creare un account aggiuntivo che possa creare database, è necessario creare un utente nel master e aggiungere l'utente al ruolo speciale del database **dbmanager**. L'utente può essere un utente di database indipendente oppure un utente basato su un account di accesso di SQL Server nel database master virtuale.

1.	Connettersi al database master virtuale usando un account amministratore.
2.	Passaggio facoltativo: creare un account di accesso con autenticazione di SQL Server con l'istruzione [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx). Istruzione di esempio:

     ```
     CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
     ```

     > [AZURE.NOTE] Quando si crea un account di accesso o un utente di database indipendente, è necessario usare una password complessa. Per ulteriori informazioni, vedere [Password complesse](https://msdn.microsoft.com/library/ms161962.aspx).

3.	Nel database master virtuale creare un utente con l'istruzione [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx). L'utente può essere un utente di database indipendente con autenticazione di Azure Active Directory (se l'ambiente è stato configurato per l'autenticazione di Azure AD), un utente di database indipendente con autenticazione di SQL Server oppure un utente con autenticazione di SQL Server basato su un account di accesso con autenticazione di SQL Server (creato nel passaggio precedente). Istruzioni di esempio:

     ```
     CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
     CREATE USER Tran WITH PASSWORD = '<strong_password>';
     CREATE USER Mary FROM LOGIN Mary; 
     ```

4.	Aggiungere il nuovo utente al ruolo del database **dbmanager** con l'istruzione [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx). Istruzioni di esempio:

     ```
     ALTER ROLE dbmanager ADD MEMBER Mary; 
     ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
     ```

     > [AZURE.NOTE] Poiché dbmanager è un ruolo del database nel database master virtuale, è possibile aggiungere solo un utente al ruolo dbmanager. Non si può aggiungere un account di accesso a livello di server a un ruolo a livello di database.

5.	Se necessario, configurare il firewall a livello di server in modo da consentire la connessione del nuovo utente.

L'utente potrà così connettersi al database master virtuale e creare nuovi database. L'account che crea il database ne diventa il proprietario.

### Gestione degli account di accesso

Se si vuole, è possibile completare la stessa procedura (ovvero creare un account di accesso e aggiungere un utente al ruolo **loginmanager**) per consentire a un utente di creare nuovi account di accesso nel master virtuale. Nella maggior parte dei casi non è necessario, perché è consigliabile usare utenti di database indipendente che eseguono l'autenticazione a livello di database anziché utenti basati su account di accesso. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

## Utenti non amministratori

In genere, per gli account non amministratore non è necessario l'accesso al database master virtuale. Creare utenti di database indipendente a livello di database con l'istruzione [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). L'utente può essere un utente di database indipendente con autenticazione di Azure Active Directory (se l'ambiente è stato configurato per l'autenticazione di Azure AD), un utente di database indipendente con autenticazione di SQL Server oppure un utente con autenticazione di SQL Server basato su un account di accesso con autenticazione di SQL Server (creato nel passaggio precedente). Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

Per creare utenti, connettersi al database ed eseguire istruzioni simili alle seguenti:

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Inizialmente, solo gli amministratori o il proprietario del database possono creare utenti. Per autorizzare utenti aggiuntivi a creare nuovi utenti, concedere all'utente selezionato l'autorizzazione `ALTER ANY USER` con un'istruzione come la seguente:

```
GRANT ALTER ANY USER TO Mary;
```

Per concedere a utenti aggiuntivi il controllo completo del database, rendere tali utenti membri del ruolo predefinito del database **db\_owner** con l'istruzione `ALTER ROLE`.

> [AZURE.NOTE] Il motivo principale per creare utenti di database basati su account di accesso è la presenza di utenti con autenticazione di SQL Server che devono accedere a più database. Gli utenti basati su account di accesso sono associati all'account di accesso, per il quale viene gestita una sola password. Gli utenti di database indipendente in singoli database sono ognuno una singola entità che gestisce una propria password. Questo può creare confusione se gli utenti di database indipendente non usano password identiche.

### Configurazione del firewall a livello di database

Come procedura consigliata, gli utenti non amministratori dovrebbero avere accesso tramite il firewall solo ai database usati. Invece di autorizzarne gli indirizzi IP tramite il firewall a livello di server e concedere loro l'accesso a tutti i database, usare l'istruzione [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) per configurare il firewall a livello di database. Il firewall a livello di database non può essere configurato usando il portale.

### Percorso di accesso degli utenti non amministratori

Quando il firewall a livello di database è configurato correttamente, gli utenti di database possono connettersi usando strumenti client come SQL Server Management Studio o SQL Server Data Tools. Solo gli strumenti più recenti offrono tutte le caratteristiche e le funzionalità. Il diagramma seguente illustra un percorso di accesso tipico degli utenti non amministratori. ![Percorso di accesso degli utenti non amministratori](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)
 
## Gruppi e ruoli
In una gestione efficiente degli accessi vengono usate autorizzazioni assegnate a gruppi e ruoli anziché singoli utenti. Se viene usata l'autenticazione di Azure Active Directory, ad esempio:

- Includere gli utenti di Azure Active Directory in un gruppo di Azure Active Directory. Creare un utente di database indipendente per il gruppo. Inserire uno o più utenti di database in un ruolo del database. Assegnare quindi le autorizzazioni al ruolo del database.

Se viene usata l'autenticazione di SQL Server:

- Creare utenti di database indipendente nel database. Inserire uno o più utenti di database in un ruolo del database. Assegnare quindi le autorizzazioni al ruolo del database.

I ruoli del database possono essere ruoli predefiniti come **db\_owner**, **db\_ddladmin**, **db\_datawriter**, **db\_datareader**, **db\_denydatawriter** e **db\_denydatareader**. Per concedere autorizzazioni complete a un numero limitato di utenti viene usato comunemente **db\_owner**. Gli altri ruoli predefiniti del database sono utili per ottenere rapidamente database semplici nello sviluppo, ma non sono consigliabili per la maggior parte dei database di produzione. Il ruolo predefinito del database **db\_datareader**, ad esempio, concede l'accesso in lettura a tutte le tabelle del database, che in genere è più di quanto strettamente necessario. È preferibile usare l'istruzione [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) per creare ruoli del database definiti dall'utente e concedere con attenzione a ogni ruolo le autorizzazioni minime necessarie per le esigenze aziendali. Quando un utente è membro di più ruoli, vengono aggregate le autorizzazioni di tutti.

## Autorizzazioni

Nel database SQL possono essere concesse o negate singolarmente oltre 100 autorizzazioni. Molte di queste autorizzazioni sono annidate. L'autorizzazione `UPDATE` per uno schema, ad esempio, include l'autorizzazione `UPDATE` per ogni tabella all'interno di tale schema. Come nella maggior parte dei sistemi di autorizzazioni, la negazione di un'autorizzazione determina l'override di una concessione. A causa dell'annidamento e del numero delle autorizzazioni, progettare un sistema di autorizzazioni appropriato per proteggere correttamente il database può richiedere un attento studio. Per iniziare, vedere l'elenco di autorizzazioni in [Autorizzazioni (Motore di database)](https://msdn.microsoft.com/library/ms191291.aspx) e la [grafica in formato di poster](http://go.microsoft.com/fwlink/?LinkId=229142) relativa alle autorizzazioni.


## Passaggi successivi

[Protezione del Database SQL](sql-database-security.md)

[Esercitazione per la creazione di una tabella](https://msdn.microsoft.com/library/ms365315.aspx)

[Esercitazione per l'inserimento e l'aggiornamento dei dati in una tabella](https://msdn.microsoft.com/library/ms365309.aspx)

[Esercitazione per la lettura dei dati di una tabella](https://msdn.microsoft.com/library/ms365310.aspx)

[Creazione di viste e stored procedure](https://msdn.microsoft.com/library/ms365311.aspx)

[Concessione dell'accesso a un oggetto di database](https://msdn.microsoft.com/library/ms365327.aspx)


## Risorse aggiuntive

[Protezione del Database SQL](sql-database-security.md)

[Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589.aspx)

<!---HONumber=AcomDC_0706_2016-->