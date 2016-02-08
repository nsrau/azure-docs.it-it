<properties
   pageTitle="Account e autorizzazioni di Azure AD Connect | Microsoft Azure"
   description="Questo argomento descrive gli account usati e creati nonché le autorizzazioni necessarie."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell;billmath"/>


# Autorizzazioni e account necessari per Azure AD Connect

L'installazione guidata di Azure AD Connect offre due percorsi diversi:

- Mediante le impostazioni rapide sono necessari più privilegi per semplificare la definizione della configurazione, senza richiedere agli utenti di creare o configurare separatamente le autorizzazioni.

- Mediante le impostazioni personalizzate viene offerto un maggior numero di scelte e opzioni, ma esistono alcune situazioni in cui è necessario assicurarsi manualmente di disporre delle autorizzazioni corrette.

## Documentazione correlata
Se non è stata letta la documentazione in [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md), la tabella seguente fornisce collegamenti ad argomenti correlati.

| Argomento | |
| --------- | --------- |
| Eseguire l'installazione mediante le impostazioni rapide | [Installazione rapida di Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Eseguire l'installazione mediante le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Aggiornamento da DirSync | [Aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |


## Installazione mediante le impostazioni rapide
Nell'installazione guidata mediante le impostazioni rapide vengono richieste le credenziali di amministratore dell'organizzazione, in modo che l'istanza locale di Active Directory possa essere configurata con le autorizzazioni richieste per Azure AD Connect. Se si esegue l'aggiornamento da DirSync, le credenziali di amministratore dell'organizzazione vengono usate per reimpostare la password per l'account usato da DirSync.

Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie| Utilizzo
------------- | ------------- |------------- |------------- |
N/D|Utente che esegue l'installazione guidata| Amministratore del server locale| <li>Creazione dell'account locale che verrà usato come [account del servizio del motore di sincronizzazione](#azure-ad-connect-sync-service-account).
Connessione ad Azure AD| Credenziali di directory di Azure AD | Ruolo di amministratore in Azure AD | <li>Abilitazione della sincronizzazione nella directory di Azure AD.</li> <li>Creazione dell'[account Azure AD](#azure-ad-service-account) che verrà usato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li>
Connessione ad AD DS | Credenziali Active Directory locali | Membro del gruppo Enterprise Admins (EA) in Active Directory| <li>Creazione di un [account](#active-directory-account) in Active Directory e concessione delle relative autorizzazioni. L'account creato viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione.</li>

### Credenziali di amministratore dell'organizzazione
Queste credenziali vengono usate solo durante l'installazione e non dopo il completamento di quest'ultima. È l'amministratore dell'organizzazione, non l'amministratore di dominio, a garantire che le autorizzazioni in Active Directory possano essere impostate in tutti i domini.

### Credenziali di amministratore globale
Queste credenziali vengono usate solo durante l'installazione e non dopo il completamento di quest'ultima. Vengono usate per creare l'[account Azure AD](#azure-ad-service-account) utile per sincronizzare le modifiche apportate ad Azure AD e per abilitare la sincronizzazione come funzionalità in Azure AD. Nell'account in uso non è possibile abilitare l'autenticazione a più fattori.

## Installazione mediante le impostazioni personalizzate
Quando si usano le impostazioni personalizzate, l'account usato per connettersi ad Active Directory deve essere creato prima dell'installazione.

Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie| Utilizzo
------------- | ------------- |------------- |-------------
N/D|Utente che esegue l'installazione guidata|<li>Amministratore del server locale</li><li>Se si usa una versione completa di SQL Server, l'utente deve essere un amministratore di sistema in SQL</li>| Per impostazione predefinita, crea l'account locale che verrà usato come [account del servizio del motore di sincronizzazione](#azure-ad-connect-sync-service-account). L'account viene creato solo se l'amministratore non specifica un account determinato.
Installazione dei servizi di sincronizzazione, opzione Account di servizio | Active Directory o credenziali dell'account utente locale | Autorizzazioni utente garantite dall'installazione guidata|Se l'amministratore specifica un account, quest'ultimo viene usato come account del servizio di sincronizzazione.
Connessione ad Azure AD|Credenziali di directory di Azure AD| Ruolo di amministratore in Azure AD| <li>Abilitazione della sincronizzazione nella directory di Azure AD.</li> <li>Creazione dell'[account Azure AD](#azure-ad-service-account) che verrà usato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li>
Connessione delle directory|Credenziali Active Directory locali per ogni foresta connessa ad Azure AD | Le autorizzazioni variano in base alle funzionalità attivate e sono disponibili in [Creare l'account di Servizi di dominio Active Directory](#create-the-ad-ds-account) |L'account viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione.
Server ADFS|Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali, se le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione|Amministratore di dominio|Installazione e configurazione del ruolo del server ADFS.
Server Proxy applicazione Web |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali, se le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione|Amministratore locale nel computer di destinazione|Installazione e configurazione del ruolo del server WAP.
Credenziali di attendibilità del proxy |Credenziali di attendibilità del servizio federativo (le credenziali che saranno utilizzate dal proxy per richiedere un certificato di attendibilità da FS) |Account di dominio che è un amministratore locale del server ADFS|Registrazione iniziale del certificato di attendibilità di FS-WAP
Pagina Account del servizio ADFS, "Utilizzare un'opzione account utente di dominio"|Credenziali dell'account utente di Active Directory|Utente di dominio|L'account utente di Active Directory di cui vengono fornite le credenziali verrà usato come account di accesso del servizio AD FS.

### Creare l'account del Servizio di dominio di Active Directory
Quando si installa Azure AD Connect, l'account specificato nella pagina **Connessione delle directory** deve essere presente in Active Directory e deve disporre delle autorizzazioni concesse necessarie. L'installazione guidata non verificherà che le autorizzazioni e gli eventuali problemi vengano rilevati solo durante la sincronizzazione.

Le autorizzazioni necessarie dipendono dalle funzionalità facoltative abilitate. Se si dispone di più domini, le autorizzazioni devono essere concesse per tutti i domini nella foresta. Se queste funzionalità non vengono abilitate, saranno sufficienti le autorizzazioni dell'**utente di dominio** predefinite.

| Funzionalità | Autorizzazioni |
| ------ | ------ |
| Sincronizzazione delle password | <li>Replica delle modifiche della directory</li> <li>Replica di tutte le modifiche della directory |
| Distribuzione ibrida di Exchange | Autorizzazioni di scrittura per gli attributi documentati in [Writeback della distribuzione ibrida Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) per utenti, gruppi e contatti. |
| Writeback delle password | Autorizzazioni di scrittura per gli attributi documentati in [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) per gli utenti. |
| Writeback dei dispositivi | Autorizzazioni concesse con uno script di PowerShell come descritto in [Writeback dei dispositivi](active-directory-aadconnect-get-started-custom-device-writeback.md).|
| Writeback dei gruppi | Lettura, creazione, aggiornamento ed eliminazione di oggetti di gruppo nell'unità organizzativa in cui devono trovarsi i gruppi di distribuzione.|

## Aggiornare
Quando si aggiorna da una versione di Azure AD Connect a una nuova versione, è necessario avere le autorizzazioni seguenti:

| Entità | Autorizzazioni necessarie | Usato per |
| ---- | ---- | ---- |
| Utente che esegue l'installazione guidata | Amministratore del server locale | Aggiornare i file binari. |
| Utente che esegue l'installazione guidata | Membro di ADSyncAdmins | Apportare modifiche alle regole di sincronizzazione e ad altre configurazioni. |
| Utente che esegue l'installazione guidata | Se si utilizza un server SQL completo: DBO (o simile) del database del motore di sincronizzazione | Apportare modifiche a livello di database, ad esempio l'aggiornamento di tabelle con nuove colonne. |

## Altre informazioni sugli account creati

### Account Active Directory

Se si usano le impostazioni rapide, verrà creato un account in Active Directory che verrà usato per la sincronizzazione. L'account creato si troverà nel dominio radice della foresta nel contenitore degli utenti e il relativo nome con prefisso sarà **MSOL\_**. L'account viene creato con una password lunga e complessa priva di scadenza. Se nel dominio sono presenti criteri di password, assicurarsi che per tale account siano consentite password lunghe e complesse.

![Account AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Account del servizio di sincronizzazione Azure AD Connect
Mediante l'installazione guidata vengono creati due account di servizio locale (a meno che non si specifichi l'account da usare nelle impostazioni personalizzate). L'account preceduto da **AAD\_** viene usato per l'esecuzione come servizio di sincronizzazione effettiva. Se si installa Azure AD Connect in un Controller di dominio, gli account vengono creati nel dominio. Se si usa un server SQL su un server remoto, l'account del servizio **AAD\_** deve trovarsi nel dominio. L'account preceduto da **AADSyncSched\_** viene usato per l'attività pianificata che esegue il motore di sincronizzazione.

![Account del servizio di sincronizzazione](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Gli account vengono creati con una password lunga e complessa priva di scadenza.

Per l’account del servizio del motore di sincronizzazione, tale account verrà usato da Windows per archiviare le chiavi di crittografia in modo che le password dell'account non vengano reimpostate né modificate.

Se si usa una versione completa di SQL Server, l'account del servizio sarà il DBO del database creato per il motore di sincronizzazione. Il servizio non funzionerà come previsto con tutte le altre autorizzazioni. Viene inoltre creato un account di accesso SQL.

L'account concede inoltre le autorizzazioni a file, chiavi del Registro di sistema e altri oggetti correlati al motore di sincronizzazione.

### Account del servizio Azure AD
In Azure AD verrà creato un account per l'uso con il servizio di sincronizzazione. Questo account può essere identificato con il relativo nome visualizzato.

![Account AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Il nome del server in cui viene usato l'account può essere identificato nella seconda parte del nome utente. Nell'immagine precedente il nome del server è FABRIKAMCON. Se si dispone di server di gestione temporanea, ogni server disporrà del proprio account. In Azure AD esiste un limite di 10 account del servizio di sincronizzazione.

L'account del servizio viene creato con una password lunga e complessa priva di scadenza. Viene concesso un ruolo speciale per gli **account di sincronizzazione della directory** che dispone solo delle autorizzazioni per eseguire attività di sincronizzazione della directory. Questo specifico ruolo integrato non può essere concesso all'esterno della procedura di Azure AD Connect e il portale di Azure visualizzerà questo account solo con il ruolo **utente**.

![Ruolo dell'account AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Passaggi successivi

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->