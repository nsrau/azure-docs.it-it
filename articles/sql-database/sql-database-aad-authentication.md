<properties
   pageTitle="Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory | Microsoft Azure"
   description="Informazioni su come connettersi database SQL con l'autenticazione di Azure Active Directory."
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
   ms.date="08/04/2016"
   ms.author="rick.byham@microsoft.com"/>  

# Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory

L'autenticazione di Azure Active Directory è un meccanismo di connessione al database SQL di Microsoft Azure e a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite le identità di Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

- Offre un'alternativa all'autenticazione di SQL Server.
- Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
- Consente la rotazione delle password in un'unica posizione.
- I clienti possono gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
- Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
- L'autenticazione di Azure Active Directory usa gli utenti di database indipendente per autenticare le identità a livello di database.
- Azure Active Directory supporta l'autenticazione basata su token per le applicazioni che si connettono al database SQL.
- L'autenticazione di Azure Active Directory supporta la federazione dei domini di AD FS o l'autenticazione utente/password nativa per un'istanza locale di Azure Active Directory senza la sincronizzazione del dominio.

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory.
2. Assicurarsi che il database sia in un database SQL di Azure versione 12. Questo non è necessario per SQL Data Warehouse.
3. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
4. Creare un amministratore di Azure Active Directory per il server di Azure SQL o per [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configurare i computer client.
6. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
7. Connettersi al database usando le identità di Azure AD.


## Architettura di attendibilità

Il diagramma generale seguente riepiloga l'architettura della soluzione relativa all'uso dell'autenticazione di Azure AD con il database SQL di Azure SQL. Gli stessi concetti sono validi per SQL Data Warehouse. Per supportare la password utente nativa di Azure Active Directory, viene considerata solo la parte cloud e Azure AD o il database SQL di Azure. Per supportare l'autenticazione federata o l'autenticazione utente/password per le credenziali di Windows, è necessaria la comunicazione con il blocco AD FS. Le frecce indicano i percorsi di comunicazione.

![diagramma di autenticazione di aad][1]  

Il diagramma seguente indica le relazioni federative, di trust e di hosting che consentono a un client di connettersi a un database inviando un token autenticato da un'istanza di Azure AD e considerato attendibile dal database. Il cliente 1 può rappresentare un'istanza di Azure Active Directory con utenti nativi o con utenti federati. Il cliente 2 rappresenta una possibile soluzione che include utenti importati. In questo esempio provengono da un'istanza federata di Azure Active Directory con AD FS sincronizzato con Azure Active Directory. È importante comprendere che l'accesso a un database con l'autenticazione di Azure AD richiede che la sottoscrizione di hosting sia associata ad Azure Active Directory. La stessa sottoscrizione deve essere usata per creare il server di database SQL di Azure o SQL Data Warehouse.

![relazione di sottoscrizione][2]  

## Struttura dell'account amministratore

Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server di database SQL, l'amministratore del server SQL originale e l'amministratore di Azure AD. Gli stessi concetti sono validi per SQL Data Warehouse. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando quindi più amministratori di Azure AD per l'istanza di SQL Server. L'uso dell'account di gruppo come amministratore migliora la gestibilità, perché consente di aggiungere e rimuovere a livello centrale i membri del gruppo in Azure AD senza apportare modifiche a utenti o autorizzazioni nel database SQL. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][3]

## Autorizzazioni

Per creare nuovi utenti, è necessario avere l'autorizzazione **ALTER ANY USER** sul database. L'autorizzazione **ALTER ANY USER** può esser concessa a qualsiasi utente di database. L'autorizzazione **ALTER ANY USER** è assegnata anche agli account amministratore del server, agli utenti di database con l'autorizzazione **CONTROL ON DATABASE** o **ALTER ON DATABASE** per tale database e ai membri del ruolo del database **db\_owner**.

Per creare un utente di database indipendente nel database SQL di Azure o in SQL Data Warehouse, è necessario connettesi al database con un'identità di Azure AD. Per creare il primo utente di database indipendente, è necessario connettersi al database tramite un amministratore di Azure Active Directory che sia proprietario del database. Questa operazione è illustrata nei passaggi 4 e 5 di seguito. L'autenticazione di Azure Active Directory è possibile unicamente se l'amministratore di Azure Active Directory è stato creato per il server di database SQL di Azure o SQL Data Warehouse. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza all'interno di SQL Server non possono più accedere al database con le credenziali di Azure Active Directory correnti.

## Funzionalità e limitazioni di Azure AD

È possibile effettuare il provisioning dei membri di Azure Active Directory seguenti nel server di Azure SQL o in SQL Data Warehouse:

- Membri nativi: un membro creato in Azure AD nel dominio gestito o in un dominio del cliente. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-add-domain.md).

- Membri del dominio federato: un membro creato in Azure AD con un dominio federato. Per altre informazioni, vedere il post di blog relativo al [nuovo supporto per la federazione in Microsoft Azure con Active Directory di Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Membri importati da altre istanze di Azure Active Directory che sono membri nativi o del dominio federato.

- Gruppi di Active Directory creati come gruppi di sicurezza.

Gli account Microsoft, ad esempio outlook.com, hotmail.com, live.com, oppure altri account guest, ad esempio gmail.com, yahoo.com, non sono supportati. Se è possibile accedere a [https://login.live.com](https://login.live.com) con l'account e la password, si sta usando un account Microsoft che non è supportato per l'autenticazione di Azure AD per il database SQL di Azure o per Azure SQL Data Warehouse.

### Considerazioni aggiuntive

- Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure Active Directory dedicato come amministratore.
- È possibile configurare un solo amministratore di Azure AD, utente o gruppo, per un server di Azure SQL o Azure SQL Data Warehouse in qualsiasi momento.
- Inizialmente, solo un amministratore di Azure Active Directory per server SQL può connettersi al server di Azure SQL o ad Azure SQL Data Warehouse con un account Azure Active Directory. L'amministratore di Active Directory può configurare gli utenti del database di Azure Active Directory successivi.
- È consigliabile impostare il timeout di connessione su 30 secondi.
- SQL Server 2016 Management Studio e SQL Server Data Tools per Visual Studio 2015, versione 14.0.60311.1 di aprile 2016 o successiva, supportano l'autenticazione di Azure Active Directory. L'autenticazione di Azure Active Directory è supportata dal **provider di dati .NET Framework per server SQL** a partire da .NET Framework versione 4.6. Di conseguenza, le versioni più recenti di questi strumenti e applicazioni livello dati (DAC e file con estensione bacpac) possono usare l'autenticazione di Azure Active Directory.
- [ODBC versione 13.1](https://www.microsoft.com/download/details.aspx?id=53339) supporta l'autenticazione di Azure Active Directory, tuttavia `sqlcmd.exe` e `bcp.exe` non possono connettersi mediante l'autenticazione di Azure Active Directory perché usano un provider ODBC meno recente.
- SQL Server Data Tools per Visual Studio 2015 richiede almeno la versione 14.0.60311.1 di aprile 2016. Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC Driver 6.0 per server SQL](https://www.microsoft.com/it-IT/download/details.aspx?id=11774) supporta l'autenticazione di Azure Active Directory. Vedere anche l'argomento su come [impostare le proprietà della connessione](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase non può eseguire l'autenticazione di Azure Active Directory.
- Alcuni strumenti, come BI ed Excel, non sono supportati.
- Multi-Factor Authentication (MFA/2FA) o altre forme di autenticazione interattiva non sono supportate.
- L'autenticazione di Azure Active Directory è supportata per database SQL nei pannelli **Importa database** ed **Esporta database** del portale di Azure. L'importazione e l'esportazione tramite l'autenticazione di Azure Active Directory è supportata anche dal comando PowerShell.


## 1\. Creare e popolare un'istanza di Azure AD

Creare un'istanza di Azure Active Directory e popolarla con utenti e gruppi. Sono inclusi:

- Creare il dominio gestito di Azure AD iniziale.
- Attuare la federazione di un'istanza di Servizi di dominio Active Directory locale con Azure Active Directory.

Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](../active-directory/active-directory-add-domain.md), le informazioni sul [nuovo supporto per la federazione con Active Directory di Windows Server in Microsoft Azure](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) e [Gestire Azure AD tramite Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## 2\. Assicurarsi di usare la versione 12 del database SQL

L'autenticazione di Azure Active Directory è supportata nell'ultima versione 12 del database SQL. Per informazioni sul database SQL versione 12 e per sapere se è disponibile nella propria area geografica, vedere [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md). Questo passaggio non è necessario per Azure SQL Data Warehouse dato che SQL Data Warehouse è disponibile solo nella versione 12.

Se si ha già un database, verificare che sia ospitato nella versione 12 del database SQL. A questo scopo, connettersi al database (usando ad esempio SQL Server Management Studio) ed eseguire `SELECT @@VERSION;` L'output previsto per un database nella versione 12 del database SQL è almeno **Microsoft SQL Azure (RTM) - 12.0**.

Se il database non è ospitato nella versione 12 del database SQL, vedere [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md) e quindi visitare il portale di Azure classico per eseguire la migrazione del database alla versione 12 del database SQL.

In alternativa è possibile creare un nuovo database nella versione 12 del database SQL eseguendo i passaggi descritti in [Creare il primo database SQL di Azure](sql-database-get-started.md). **Suggerimento**: leggere il passaggio successivo prima di selezionare una sottoscrizione per il nuovo database.

## 3\. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure

Per associare il database alla directory di Azure AD dell'organizzazione, impostare la directory come directory attendibile per la sottoscrizione di Azure che ospita il database. Per altre informazioni, vedere [Associazione delle sottoscrizioni di Azure ad Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Altre informazioni:** ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. È possibile vedere quale directory è considerata attendibile dalla propria sottoscrizione nella scheda **Impostazioni** all'indirizzo [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Questa relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione tra la sottoscrizione e tutte le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere a tutte queste altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire gli utenti della directory. Per altre informazioni sulle risorse, vedere [Informazioni sull'accesso alle risorse di Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Le procedure seguenti forniscono istruzioni dettagliate sulla modifica della directory associata per una determinata sottoscrizione.

1. Connettersi al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione di Azure.
2. Nel banner a sinistra selezionare **IMPOSTAZIONI**.
3. Le sottoscrizioni sono visualizzate nella schermata delle impostazioni. Se la sottoscrizione desiderata non è visualizzata, fare clic su **Sottoscrizioni** in alto, fare clic sulla casella di riepilogo a discesa **FILTRA PER DIRECTORY** e selezionare la directory che contiene le sottoscrizioni, quindi fare clic su **APPLICA**.

	![selezionare la sottoscrizione][4]
4. Nell'area**impostazioni** fare clic sulla sottoscrizione e quindi su **MODIFICA DIRECTORY** in basso nella pagina.

	![ad-settings-portal][5]  
5. Nella casella **MODIFICA DIRECTORY** selezionare l'istanza di Azure Active Directory associata al server SQL o SQL Data Warehouse, quindi fare clic sulla freccia per continuare.

	![edit-directory-select][6]
6. Nella finestra di dialogo **CONFERMA MAPPING DIRECTORY** confermare l'avviso "**Tutti i coamministratori verranno rimossi**".

	![edit-directory-confirm][7]  
7. Fare clic sul segno di spunta per ricaricare il portale.

> [AZURE.NOTE] Quando si modifica la directory, l'accesso per tutti i coamministratori, gli utenti e i gruppi di Azure AD e gli utenti delle risorse supportate dalla directory saranno rimossi e l'accesso a questa sottoscrizione o alle relative risorse non sarà più possibile. Solo l'amministratore del servizio potrà configurare l'accesso per le entità in base alla nuova directory. La propagazione di questa modifica a tutte le risorse potrebbe richiedere molto tempo. La modifica della directory modifica anche l'amministratore di Azure AD per il database SQL e per SQL Data Warehouse e disabilita l'accesso al database per tutti gli utenti di Azure AD esistenti. L'amministratore di Azure AD deve essere reimpostato, come descritto di seguito, e dovranno essere creati nuovi utenti di Azure AD.

## 4\. Creare un amministratore di Azure AD per il server SQL di Azure

Ogni server di Azure SQL (che ospita un database SQL o SQL Data Warehouse) viene avviato con un singolo account amministratore del server, che è l'amministratore dell'intera istanza del server di Azure SQL. È necessario creare un secondo amministratore del server SQL, cioè un account Azure AD. Questa entità viene creata come un utente di database indipendente nel database master. Per quando riguarda gli amministratori, gli account amministratore del server sono membri del ruolo **db\_owner** in ogni database utente e ogni database utente viene immesso come utente **dbo**. Per altre informazioni sugli account amministratore del server, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md) e la sezione **Account di accesso e utenti** di [Linee guida e limitazioni per la sicurezza per il database SQL di Azure](sql-database-security-guidelines.md).

Quando si usa Azure Active Directory con la replica geografica, l'amministratore di Azure Active Directory deve essere configurato sia per i server primari che per quelli secondari. Se un server non ha un amministratore di Azure Active Directory, gli utenti e gli account di accesso di Azure Active Directory ricevono un messaggio di errore "Impossibile connettersi al server".

> [AZURE.NOTE] Gli utenti che non hanno un account Azure AD (incluso l'account amministratore di Azure SQL Server) non possono creare utenti di Azure AD, perché non hanno l'autorizzazione per convalidare con Azure AD gli utenti di database proposti.

### Effettuare il provisioning di un amministratore di Azure Active Directory per Azure SQL Server tramite il portale di Azure

1. Nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com/) fare clic sulla connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory. Scegliere la directory corretta come directory predefinita di Azure AD. Questo passaggio elenca l'associazione della sottoscrizione di Active Directory con il server SQL di Azure, assicurando che la stessa sottoscrizione venga usata per Azure AD e per il server SQL. Il server SQL di Azure può ospitare il database SQL di Azure o Azure SQL Data Warehouse.

	![choose-ad][8]  
2. Nel banner a sinistra selezionare **SQL Server**, selezionare l'istanza di **SQL Server** in uso e quindi nel pannello **SQL Server** in alto fare clic su **Impostazioni**.

	![impostazioni di AD][9]
3. Nel pannello **Impostazioni** fare clic su **Amministratore di Active Directory.
4. Nel pannello **Amministratore di Active Directory** fare clic su **Amministratore di Active Directory**, quindi fare clic su **Imposta amministratore** in alto.
5. Nel pannello **Aggiungi amministratore** cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi fare clic su **Seleziona**. Nel pannello Amministratore di Active Directory saranno visualizzati tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati nella sezione precedente **Funzionalità e limitazioni di Azure AD**. Il controllo di accesso basata sui ruoli (RBAC) si applica solo al portale e non viene propagato a SQL Server.
6. Nel pannello **Amministratore di Active Directory** in alto fare clic su **SALVA**. ![scegliere l'amministratore][10]

	Il processo di modifica dell'amministratore può richiedere alcuni minuti. Il nuovo amministratore sarà quindi visualizzato nella casella **Amministratore di Active Directory**.

> [AZURE.NOTE] Quando si configura l'amministratore di Azure AD il nuovo nome dell'amministratore (utente o gruppo) non può essere già presente nel database master virtuale come un utente dell'autenticazione del server SQL. Se presente, l’impostazione dell’amministratore di Microsoft Azure avrà esito negativo; eseguire il rollback della creazione e indicare che tale (nome) di amministratore già esiste. Poiché tale utente dell'autenticazione del server SQL non è parte di Azure AD, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure AD avrà esito negativo.

Per rimuovere un amministratore in un secondo momento, nella parte superiore del pannello **Amministratore di Active Directory** fare clic su **Rimuovi amministratore**, quindi su **Salva**.

### Effettuare il provisioning di un amministratore di Azure AD per Azure SQL Server tramite PowerShell

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Per effettuare il provisioning di un amministratore di Azure AD, è necessario eseguire i comandi di Azure PowerShell seguenti:

- Add-AzureRmAccount
- Select-AzureRmSubscription


Cmdlet usati per il provisioning e la gestione dell'amministratore di Azure AD:

| Nome del cmdlet | Descrizione |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) | Effettua il provisioning di un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. Deve far parte della sottoscrizione corrente. |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Rimuove un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) | Restituisce informazioni sull'amministratore di Azure Active Directory attualmente configurato per il server di Azure SQL o Azure SQL Data Warehouse. |

Per visualizzare altri dettagli per ogni comando, usare il comando di PowerShell get-help, ad esempio ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Lo script seguente effettua il provisioning di un gruppo di amministratori di Azure AD denominato **DBA\_Group** (ID oggetto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) per il server **demo\_server** in un gruppo di risorse denominato **Group-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Il parametro di input **DisplayName** accetta il nome visualizzato di Azure AD o il nome dell'entità utente. Ad esempio, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Per i gruppi di Azure AD è supportato solo il nome visualizzato di Azure AD.

> [AZURE.NOTE] Il comando di Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` non impedisce di effettuare il provisioning degli amministratori di Azure AD per gli utenti non supportati. È possibile effettuare il provisioning di un utente non supportato, il quale non potrà tuttavia connettersi a un database. Vedere l'elenco degli amministratori supportati nella sezione precedente **Funzionalità e limitazioni di Azure AD**.

L'esempio seguente usa il valore **ObjectID** opzionale:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Il valore **ObjectID** di Azure AD è obbligatorio quando **DisplayName** non è univoco. Per recuperare i valori **ObjectID** e **DisplayName**, usare la sezione Active Directory del portale di Azure classico e visualizzare le proprietà di un utente o di un gruppo.

L'esempio seguente restituisce informazioni sull'amministratore di Azure AD corrente per Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

L'esempio seguente illustra come rimuovere un amministratore di Azure AD:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

È anche possibile effettuare il provisioning di un amministratore di Azure Active Directory usando le API REST. Per altre informazioni, vedere le [informazioni di riferimento sull'API REST di gestione dei servizi e le operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## 5\. Configurare i computer client

È necessario installare il software seguente in tutti i computer client da cui le applicazioni o gli utenti si connettono al database SQL di Azure o ad Azure SQL Data Warehouse con le identità di Azure AD:

- .NET Framework 4.6 o versione successiva dalla pagina [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library per SQL Server (**ADALSQL.DLL**) è disponibile in più lingue (sia per x86 che per amd64) dall'Area download in [Microsoft Active Directory Authentication Library per Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### Strumenti

- L'installazione di [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o [SQL Server Data Tools per Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) soddisfa i requisiti di .NET Framework 4.6.
- SSMS installa la versione x86 di **ADALSQL.DLL**.
- SSDT installa la versione amd64 di **ADALSQL.DLL**.
- L'ultima versione di Visual Studio disponibile nella pagina [Download di Visual Studio ](https://www.visualstudio.com/downloads/download-visual-studio-vs) soddisfa i requisiti di .NET Framework 4.6, ma non installa la versione amd64 di **ADALSQL.DLL** richiesta.

## 6\. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD

### Informazioni sugli utenti di database indipendente

L'autenticazione di Azure Active Directory richiede la creazione di utenti del database come utenti di database indipendente. Un utente di database indipendente basato su un'identità di Azure AD è un utente di database che non può accedere al database master e del quale viene eseguito il mapping a un'identità nella directory di Azure AD associata al database. L'identità di Azure AD può essere un singolo account utente o un gruppo. Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Fatta eccezione per gli amministratori, non è possibile creare utenti del database usando ruoli Portale e Controllo degli accessi in base al ruolo, perché non vengono propagati al server SQL, al database SQL o a SQL Data Warehouse. I ruoli Controllo degli accessi in base al ruolo di Azure vengono usati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni di database. Ad esempio, il ruolo **Collaboratore SQL Server** non consente l'accesso per connettersi al database SQL oppure a SQL Data Warehouse. L'autorizzazione di accesso deve essere concessa direttamente nel database tramite istruzioni Transact-SQL.

### Connettersi al data warehouse o al database dell'utente SQL Server Management Studio o SQL Server Data Tools

Per verificare che l'amministratore di Azure AD sia configurato correttamente, connettersi al database **master** con l'account amministratore di Azure AD. Per effettuare il provisioning di un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD che abbia accesso al database.

> [AZURE.IMPORTANT] Il supporto per l'autenticazione di Azure Active Directory è disponibile con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015.

#### Connettesi usando l'autenticazione integrata di Active Directory

Usare questo metodo se si è connessi a Windows con le credenziali di Azure Active Directory da un dominio federato.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti al motore di database**) selezionare **Autenticazione integrata di Active Directory** nella casella **Autenticazione**. La password non è necessaria e non può essere immessa, perché per la connessione vengono presentate le credenziali esistenti. ![Selezionare Autenticazione integrata di Active Directory][11]

2. Fare clic sul pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connetti al database**.
![Selezionare il nome del database][13]


#### Connettersi usando l'autenticazione della password di Active Directory

Usare questo metodo per connettersi con il nome dell'entità di Azure AD tramite il dominio gestito di Azure AD. Si può anche usarlo per un account federato senza accesso al dominio, ad esempio quando si lavora in modalità remota.

Usare questo metodo se si è connessi a Windows con le credenziali di un dominio non federato con Azure o quando si usa l'autenticazione di Azure AD con Azure AD basato sul dominio iniziale o client.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti al motore di database**) selezionare **Autenticazione della password Active Directory** nella casella **Autenticazione**.
2. Nella casella **Nome utente** digitare il nome utente di Azure Active Directory nel formato **username@domain.com**. Deve essere un account di Azure Active Directory o un account di un dominio federato con Azure Active Directory.
3. Nella casella **Password** digitare la password utente dell'account Azure Active Directory o dell'account di dominio federato. 
![Selezionare Autenticazione della password di Active Directory][12]

4. Fare clic sul pulsante **Opzioni** e quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connetti al database**. Vedere il grafico nell'opzione precedente.


### Creare un utente di database indipendente di Azure AD in un database utente

Per creare un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD (come descritto nella procedura precedente) come utente con almeno l'autorizzazione **ALTER ANY USER**. Usare quindi la sintassi Transact-SQL seguente:

	CREATE USER <Azure_AD_principal_name>
	FROM EXTERNAL PROVIDER;


*Azure\_AD\_principal\_name* può essere il nome dell'entità utente di un utente di Azure AD o il nome visualizzato di un gruppo di Azure AD.

**Esempi:** per creare un utente di database indipendente che rappresenta un utente di dominio gestito o federato di Azure AD:

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Per creare un utente di database indipendente che rappresenta un gruppo di dominio federato o di Azure AD, specificare il nome visualizzato di un gruppo di sicurezza:

	CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Per creare un utente di database indipendente che rappresenta un'applicazione che si connette usando un token di Azure AD:

	CREATE USER [appName] FROM EXTERNAL PROVIDER;

Per altre informazioni sulla creazione di utenti di database indipendente basati su identità di Azure Active Directory, vedere [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] La rimozione dell'amministratore di Azure Active Directory per il server di Azure SQL impedisce agli utenti dell'autenticazione di Azure AD di connettersi al server. Se necessario, un amministratore del database SQL può eliminare manualmente gli utenti di Azure AD inutilizzabili.

Quando si crea un database utente, questo riceve l'autorizzazione **CONNECT** e può connettersi al database come membro del ruolo **PUBLIC**. Inizialmente le sole autorizzazioni disponibili per l'utente sono quelle concesse al ruolo **PUBLIC** o quelle concesse a qualsiasi gruppo di Windows di cui è membro. Dopo avere effettuato il provisioning di un utente di database indipendente basato su Azure AD, è possibile concedere all'utente altre autorizzazioni, esattamente come si concede un'autorizzazione a qualsiasi altro tipo di utente. In genere si concedono autorizzazioni ai ruoli del database e si aggiungono gli utenti ai ruoli. Per altre informazioni, vedere l'articolo relativo alle [nozioni di base delle autorizzazioni per il motore di database](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Per altre informazioni sui ruoli speciali del database SQL, vedere [Gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md). Un utente di dominio federato importato in un dominio gestito deve usare l'identità del dominio gestito.

> [AZURE.NOTE] Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo E (EXTERNAL\_USER). I gruppi sono contrassegnati con il tipo X (EXTERNAL\_GROUPS). Per altre informazioni, vedere [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## 7\. Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

- Con l'autenticazione integrata di Windows
- Con un nome di entità e una password di Azure AD
- Con l'autenticazione del token dell'applicazione

### 7\.1. Connessione con l'autenticazione integrata (Windows)

Per usare l'autenticazione integrata di Windows, l'istanza di Active Directory del dominio deve essere federata con Azure Active Directory e l'applicazione client (o un servizio) che si connette al database deve essere in esecuzione in un computer aggiunto a un dominio con le credenziali di dominio di un utente.

Per connettersi a un database usando l'autenticazione integrata e un'identità di Azure AD, la parola chiave Authentication nella stringa di connessione al database deve essere impostata su Active Directory Integrated. L'esempio di codice C# seguente usa ADO .NET.

	string ConnectionString =
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Si noti che la parola chiave ``Integrated Security=True`` della stringa di connessione non è supportata per la connessione al database SQL di Azure.

### 7\.2. Connessione con un nome di entità e una password di Azure AD
Per connettersi a un database usando l'autenticazione integrata e un'identità di Azure AD, la parola chiave Authentication deve essere impostata su Active Directory Password e la stringa di connessione deve contenere le parole chiave e i valori User ID/UID e Password/PWD. L'esempio di codice C# seguente usa ADO .NET.

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Per altre informazioni sui metodi di autenticazione di Azure AD, usare gli esempi di codice dimostrativo disponibili in [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth) (Dimostrazione GitHub dell'autenticazione di Azure AD).


### 7\.3 Connessione con un token di Azure AD
Questo tipo di autenticazione consente ai servizi di livello intermedio di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse ottenendo un token da Azure Active Directory (AAD). Consente scenari complessi, tra cui l'autenticazione basata su certificato. Per usare l'autenticazione tramite token di Azure AD, è necessario completare quattro passaggi fondamentali:

1. Registrare l'applicazione in Azure Active Directory e ottenere l'ID client per il codice.
2. Creare un utente del database che rappresenta l'applicazione. (Completato in precedenza al passaggio 6).
3. Creare un certificato nel computer client che eseguirà l'applicazione.
4. Aggiungere il certificato come chiave per l'applicazione.

Stringa di connessione di esempio:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Per altre informazioni, vedere [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/) (Blog sulla sicurezza del server SQL).

## Vedere anche

[Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md)

[Database indipendenti](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->  

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

<!---HONumber=AcomDC_0810_2016-->