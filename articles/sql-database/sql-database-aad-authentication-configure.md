---
title: Configurare Azure Active Directory Authentication Library - SQL | Documentazione Microsoft
description: Informazioni su come eseguire la connessione a un database SQL e SQL Data Warehouse con l&quot;autenticazione di Azure Active Directory.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2c13daf84727a500a2ea6a3dc1d4968c9824e223
ms.openlocfilehash: d7eef61b28d63b794235d82fdbbabb13b4cd3372
ms.lasthandoff: 02/16/2017


---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse

Questo articolo illustra come creare e popolare Azure Active Directory e quindi usare Azure Active Directory con il database SQL di Azure o con SQL Data Warehouse. Per una panoramica vedere [Autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

## <a name="create-and-populate-an-azure-ad"></a>Creare e popolare un'istanza di Azure AD
Creare un'istanza di Azure AD e popolarla con utenti e gruppi. Azure AD può essere il dominio gestito di Azure AD iniziale. Azure AD può anche essere un set di servizi di dominio Active Directory locali federato con Azure AD.

Per altre informazioni, consultare [Integrazione delle identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-add-domain.md), [Windows Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Windows Azure), [Amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) e [Gestire Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](../active-directory/active-directory-aadconnect-ports.md).

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
Per associare il database alla directory di Azure AD dell'organizzazione, impostare la directory come directory attendibile per la sottoscrizione di Azure che ospita il database. Per altre informazioni, vedere [Associazione delle sottoscrizioni di Azure ad Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Altre informazioni:** ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. È possibile vedere quale directory è considerata attendibile dalla propria sottoscrizione nella scheda **Impostazioni** all'indirizzo [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Questa relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione tra la sottoscrizione e tutte le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere a tutte queste altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire gli utenti della directory. Per altre informazioni sulle risorse, vedere [Informazioni sull'accesso alle risorse di Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Le procedure seguenti illustrano come modificare la directory associata a una determinata sottoscrizione.
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

   > [!NOTE]
   > Quando si modifica la directory, l'accesso per tutti i coamministratori, gli utenti e i gruppi di Azure AD e gli utenti delle risorse supportate dalla directory vengono rimossi e l'accesso a questa sottoscrizione o alle relative risorse non è più possibile. Solo l'amministratore del servizio può configurare l'accesso per le entità in base alla nuova directory. La propagazione di questa modifica a tutte le risorse potrebbe richiedere molto tempo. La modifica della directory modifica anche l'amministratore di Azure AD per il database SQL e per SQL Data Warehouse e disabilita l'accesso al database per tutti gli utenti di Azure AD esistenti. L'amministratore di Azure AD deve essere reimpostato, come descritto di seguito, e dovranno essere creati nuovi utenti di Azure AD.
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Creare un amministratore di Azure Active Directory per il server SQL di Azure
Ogni server di Azure SQL, che ospita un database SQL o SQL Data Warehouse, viene avviato con un singolo account amministratore del server, che è l'amministratore dell'intera istanza del server di Azure SQL. È necessario creare un secondo amministratore del server SQL, cioè un account Azure AD. Questa entità viene creata come un utente di database indipendente nel database master. Per quando riguarda gli amministratori, gli account amministratore del server sono membri del ruolo **db_owner** in ogni database utente e ogni database utente viene immesso come utente **dbo**. Per altre informazioni sugli account amministratore del server, vedere l'articolo relativo alla [gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).

Quando si usa Azure Active Directory con la replica geografica, l'amministratore di Azure Active Directory deve essere configurato sia per i server primari che per quelli secondari. Se un server non ha un amministratore di Azure Active Directory, gli utenti e gli account di accesso di Azure Active Directory ricevono un messaggio di errore "Impossibile connettersi al server".

> [!NOTE]
> Gli utenti che non hanno un account Azure Active Directory, incluso l'account amministratore di Azure SQL Server, non possono creare utenti di Azure Active Directory, perché non hanno l'autorizzazione per convalidare con Azure Active Directory gli utenti di database proposti.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>Eseguire il provisioning di un amministratore di Azure Active Directory per Azure SQL Server

Le due procedure seguenti illustrano come eseguire il provisioning di un amministratore di Azure Active Directory per il server SQL Azure usando il portale di Azure e PowerShell.

### <a name="azure-portal"></a>Portale di Azure
1. Nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com/)fare clic sulla connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory. Scegliere la directory corretta come directory predefinita di Azure AD. Questo passaggio elenca l'associazione della sottoscrizione di Active Directory con il server SQL di Azure, assicurando che la stessa sottoscrizione venga usata per Azure AD e per il server SQL. Il server SQL di Azure può ospitare il database SQL di Azure o Azure SQL Data Warehouse.
   
    ![choose-ad][8]
2. Nel banner a sinistra selezionare **SQL Server**, selezionare l'istanza di **SQL Server** in uso e quindi nel pannello **SQL Server** in alto fare clic su **Impostazioni**.
   
    ![impostazioni di AD][9]
3. Nel pannello **Impostazioni** fare clic su **Amministratore di Active Directory**.
4. Nel pannello **Amministratore di Active Directory** fare clic su **Amministratore di Active Directory**, quindi fare clic su **Imposta amministratore** in alto.
5. Nel pannello **Aggiungi amministratore** cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi fare clic su **Seleziona**. Il pannello Amministratore di Active Directory visualizza tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati nella sezione precedente **Funzionalità e limitazioni di Azure AD** . Il controllo di accesso basata sui ruoli (RBAC) si applica solo al portale e non viene propagato a SQL Server.
6. Nel pannello **Amministratore di Active Directory** in alto fare clic su **SALVA**.

    ![scegliere l'amministratore][10]
   
    Il processo di modifica dell'amministratore può richiedere alcuni minuti. Il nuovo amministratore è quindi visualizzato nella casella **Amministratore di Active Directory** .

   > [!NOTE]
   > Quando si configura l'amministratore di Azure AD il nuovo nome dell'amministratore, utente o gruppo, non può essere già presente nel database master virtuale come un utente dell'autenticazione del server SQL. Se presente, l’impostazione dell’amministratore di Microsoft Azure avrà esito negativo; eseguire il rollback della creazione e indicare che tale (nome) di amministratore già esiste. Poiché tale utente dell'autenticazione del server SQL non è parte di Azure AD, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure AD ha esito negativo.
   > 


Per rimuovere un amministratore in un secondo momento, nella parte superiore del pannello **Amministratore di Active Directory** fare clic su **Rimuovi amministratore**, quindi su **Salva**.

### <a name="powershell"></a>PowerShell
Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

Per eseguire il provisioning di un amministratore di Azure AD, eseguire i comandi di Azure PowerShell seguenti:

* Add-AzureRmAccount
* Select-AzureRmSubscription

Cmdlet usati per il provisioning e la gestione dell'amministratore di Azure AD:

| Nome del cmdlet | Descrizione |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) |Esegue il provisioning di un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. Deve far parte della sottoscrizione corrente. |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) |Rimuove un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) |Restituisce informazioni sull'amministratore di Azure Active Directory attualmente configurato per il server di Azure SQL o Azure SQL Data Warehouse. |

Per visualizzare altri dettagli per ogni comando, usare il comando di PowerShell get-help, ad esempio ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Lo script seguente effettua il provisioning di un gruppo di amministratori di Azure AD denominato **DBA_Group** (ID oggetto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) per il server **demo_server** in un gruppo di risorse denominato **Group-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Il parametro di input **DisplayName** accetta il nome visualizzato di Azure AD o il nome dell'entità utente. Ad esempio, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Per i gruppi di Azure AD è supportato solo il nome visualizzato di Azure AD.

> [!NOTE]
> Il comando di Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` non impedisce di eseguire il provisioning degli amministratori di Azure AD per gli utenti non supportati. È possibile eseguire il provisioning di un utente non supportato il quale non può tuttavia connettersi a un database. 
> 

L'esempio seguente usa il valore **ObjectID**opzionale:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Il valore **ObjectID** di Azure AD è obbligatorio quando **DisplayName** non è univoco. Per recuperare i valori **ObjectID** e **DisplayName**, usare la sezione Active Directory del portale di Azure classico e visualizzare le proprietà di un utente o di un gruppo.
> 

L'esempio seguente restituisce informazioni sull'amministratore di Azure AD corrente per Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

L'esempio seguente illustra come rimuovere un amministratore di Azure AD:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

È anche possibile effettuare il provisioning di un amministratore di Azure Active Directory usando le API REST. Per altre informazioni [informazioni di riferimento sull'API REST di gestione dei servizi e le operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="configure-your-client-computers"></a>Configurare i computer client
È necessario installare il software seguente in tutti i computer client da cui le applicazioni o gli utenti si connettono al database SQL di Azure o ad Azure SQL Data Warehouse con le identità di Azure AD:

* .NET Framework 4.6 o versione successiva dalla pagina [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Azure Active Directory Authentication Library per SQL Server (**ADALSQL.DLL**) è disponibile in più lingue (sia per x86 che per amd64) dall'Area download in [Microsoft Active Directory Authentication Library per Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

È possibile soddisfare questi requisiti tramite:

* L'installazione di [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o [SQL Server Data Tools per Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) soddisfa i requisiti di .NET Framework 4.6.
* SSMS installa la versione x86 di **ADALSQL.DLL**.
* SSDT installa la versione amd64 di **ADALSQL.DLL**.
* L'ultima versione di Visual Studio disponibile nella pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) soddisfa i requisiti di .NET Framework 4.6, ma non installa la versione amd64 di **ADALSQL.DLL**richiesta.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD

L'autenticazione di Azure Active Directory richiede la creazione di utenti del database come utenti di database indipendente. Un utente di database indipendente basato su un'identità di Azure AD è un utente di database che non può accedere al database master e del quale viene eseguito il mapping a un'identità nella directory di Azure AD associata al database. L'identità di Azure AD può essere un singolo account utente o un gruppo. Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Gli utenti del database, tranne gli amministratori, non possono essere creati tramite il portale. I ruoli RBAC non vengono propagati a SQL Server, al database SQL o a SQL Data Warehouse. I ruoli Controllo degli accessi in base al ruolo di Azure vengono usati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni di database. Ad esempio, il ruolo **Collaboratore SQL Server** non consente l'accesso per connettersi al database SQL oppure a SQL Data Warehouse. L'autorizzazione di accesso deve essere concessa direttamente nel database tramite istruzioni Transact-SQL.
>

Per creare un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD come utente con almeno l'autorizzazione **ALTER ANY USER** . Usare quindi la sintassi Transact-SQL seguente:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* può essere il nome dell'entità utente di un utente di Azure AD o il nome visualizzato di un gruppo di Azure AD.

**Esempi:** per creare un utente di database indipendente che rappresenta un utente di dominio gestito o federato di Azure AD:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un gruppo di dominio federato o di Azure AD, specificare il nome visualizzato di un gruppo di sicurezza:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un'applicazione che si connette usando un token di Azure AD:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  È possibile creare un utente direttamente da Azure Active Directory solo se si usa l'Azure Active Directory associata alla sottoscrizione di Azure. Tuttavia, i membri di altre directory di Active Directory che sono utenti importati nell'Active Directory associata (chiamati utenti esterni) possono essere aggiunti a un gruppo Active Directory nell'Active Directory tenant. Tramite la creazione di un utente del database indipendente per il gruppo AD, gli utenti dell'Active Directory esterna possono ottenere l'accesso al database SQL.   

Per altre informazioni sulla creazione di utenti di database indipendente basati su identità di Azure Active Directory, vedere [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> La rimozione dell'amministratore di Azure Active Directory per il server di Azure SQL impedisce agli utenti dell'autenticazione di Azure AD di connettersi al server. Se necessario, un amministratore del database SQL può eliminare manualmente gli utenti di Azure AD inutilizzabili.   

>  [!NOTE]
>  Se si riceve un messaggio di **timeout della connessione scaduto**, potrebbe essere necessario impostare il parametro `TransparentNetworkIPResolution` della stringa di connessione su false. Per altre informazioni, vedere [Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Problema di timeout della connessione con .NET Framework 4.6.1 - TransparentNetworkIPResolution).   

   
Quando si crea un database utente, questo riceve l'autorizzazione **CONNECT** e può connettersi al database come membro del ruolo **PUBLIC**. Inizialmente le sole autorizzazioni disponibili per l'utente sono quelle concesse al ruolo **PUBLIC** o quelle concesse a qualsiasi gruppo di Windows di cui è membro. Dopo avere effettuato il provisioning di un utente di database indipendente basato su Azure AD, è possibile concedere all'utente altre autorizzazioni, esattamente come si concede un'autorizzazione a qualsiasi altro tipo di utente. In genere si concedono autorizzazioni ai ruoli del database e si aggiungono gli utenti ai ruoli. Per altre informazioni, vedere l'articolo relativo alle [nozioni di base delle autorizzazioni per il motore di database](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Per altre informazioni sui ruoli speciali del database SQL, vedere [Gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).
Un utente di dominio federato importato in un dominio gestito deve usare l'identità del dominio gestito.

> [!NOTE]
> Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo E (EXTERNAL_USER). I gruppi sono contrassegnati con il tipo X (EXTERNAL_GROUPS). Per altre informazioni, vedere [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Connettersi al data warehouse o al database dell'utente SQL Server Management Studio o SQL Server Data Tools
Per verificare che l'amministratore di Azure AD sia configurato correttamente, connettersi al database **master** con l'account amministratore di Azure AD.
Per effettuare il provisioning di un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD che abbia accesso al database.

> [!IMPORTANT]
> Il supporto per l'autenticazione di Azure Active Directory è disponibile con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. La versione di agosto 2016 di SQL Server Management Studio include anche il supporto per l'autenticazione universale di Active Directory, che consente agli amministratori di richiedere la Multi-Factor Authentication mediante chiamata telefonica, SMS, smart card con pin, o notifica dell'app per dispositivi mobili.
 
## <a name="using-an-azure-ad-identity-to-connect-using-sql-server-management-studio-or-sql-server-database-tools"></a>Utilizzo di un'identità di Azure Active Directory per connettersi usando SQL Server Management Studio o SQL Server Database Tools

Le procedure seguenti mostrano come connettersi a un database SQL con un'identità di Azure Active Directory usando SQL Server Management Studio o SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Usare questo metodo se si è connessi a Windows con le credenziali di Azure Active Directory da un dominio federato.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti al motore di database**) selezionare **Autenticazione integrata di Active Directory** nella casella **Autenticazione**. La password non è necessaria e non può essere immessa, perché per la connessione vengono presentate le credenziali esistenti.   

    ![Selezionare Autenticazione integrata di Active Directory][11]
2. Fare clic sul pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connetti al database**.   

    ![Selezionare il nome del database][13]

## <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Usare questo metodo per connettersi con il nome dell'entità di Azure AD tramite il dominio gestito di Azure AD. Si può anche usarlo per un account federato senza accesso al dominio, ad esempio quando si lavora in modalità remota.

Usare questo metodo se si è connessi a Windows con le credenziali di un dominio non federato con Azure o quando si usa l'autenticazione di Azure AD con Azure AD basato sul dominio iniziale o client.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti al motore di database**) selezionare **Autenticazione della password Active Directory** nella casella **Autenticazione**.
2. Nella casella **Nome utente** digitare il nome utente di Azure Active Directory nel formato **username@domain.com**. Deve essere un account di Azure Active Directory o un account di un dominio federato con Azure Active Directory.
3. Nella casella **Password** digitare la password utente dell'account Azure Active Directory o dell'account di dominio federato.

    ![Selezionare Autenticazione della password di Active Directory][12]
4. Fare clic sul pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connetti al database**. Vedere il grafico nell'opzione precedente.

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilizzo di un'identità di Azure Active Directory per connettersi da un'applicazione client

Le procedure seguenti mostrano come connettersi a un database SQL con un'identità di Azure Active Directory da un'applicazione client.

###  <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Per usare l'autenticazione integrata di Windows, l'Active Directory del dominio deve essere federato con Azure Active Directory. L'applicazione client (o un servizio) che si connette al database deve essere in esecuzione in un computer aggiunto al dominio con le credenziali di dominio dell'utente.

Per connettersi a un database usando l'autenticazione integrata e un'identità di Azure AD, la parola chiave Authentication nella stringa di connessione al database deve essere impostata su Active Directory Integrated. L'esempio di codice C# seguente usa ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

La parola chiave ``Integrated Security=True`` della stringa di connessione non è supportata per la connessione al database SQL di Azure. Quando si esegue una connessione ODBC, è necessario rimuovere gli spazi e impostare l'autenticazione su 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Per connettersi a un database usando l'autenticazione integrata e un'identità di Azure AD, la parola chiave Authentication deve essere impostata su Active Directory Password. La stringa di connessione deve contenere le parole chiave e i valori User ID/UID e Password/PWD. L'esempio di codice C# seguente usa ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Per altre informazioni sui metodi di autenticazione di Azure AD, usare gli esempi di codice dimostrativo disponibili in [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)(Dimostrazione GitHub dell'autenticazione di Azure AD).

## <a name="azure-ad-token"></a>Token Azure AD
Questo tipo di autenticazione consente ai servizi di livello intermedio di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse ottenendo un token da Azure Active Directory (AAD). Consente scenari complessi, tra cui l'autenticazione basata su certificato. Per usare l'autenticazione tramite token di Azure AD, è necessario completare quattro passaggi fondamentali:

1. Registrare l'applicazione in Azure Active Directory e ottenere l'ID client per il codice. 
2. Creare un utente del database che rappresenta l'applicazione. (Completato in precedenza al passaggio 6).
3. Creare un certificato nel computer client che esegue l'applicazione.
4. Aggiungere il certificato come chiave per l'applicazione.

Stringa di connessione di esempio:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Per altre informazioni, vedere [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)(Blog sulla sicurezza del server SQL).

### <a name="sqlcmd"></a>sqlcmd

Le istruzioni seguenti eseguono la connessione usando la versione 13.1 di sqlcmd disponibile in [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica dell'accesso e del controllo nel database SQL, vedere l'articolo relativo al [controllo dell'accesso al database SQL](sql-database-control-access.md).
- Per una panoramica degli account di accesso, degli utenti e dei ruoli del database nel database SQL, vedere l'articolo relativo ad [account di accesso, utenti e ruoli del database](sql-database-manage-logins.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](sql-database-firewall-configure.md).
- Per un'esercitazione sull'uso dell'autenticazione di SQL Server, vedere [Autenticazione e autorizzazione di SQL](sql-database-control-access-sql-authentication-get-started.md).
- Per un'esercitazione sull'uso dell'autenticazione di Azure Active Directory, vedere [Autenticazione e autorizzazione di Azure Active Directory](sql-database-control-access-aad-authentication-get-started.md).

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


