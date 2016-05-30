<properties
	pageTitle="Azure AD Connect: risolvere i problemi di connettività | Microsoft Azure"
	description="Descrive come risolvere i problemi di connettività con Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="andkjell"/>

# Risolvere i problemi di connettività con Azure AD Connect
Questo articolo illustra il funzionamento della connettività tra Azure AD Connect e Azure AD e come risolverne i problemi. Questi problemi si verificano con maggiore probabilità in un ambiente con un server proxy.

## Risolvere i problemi di connettività nell'Installazione guidata
Azure AD Connect usa l'autenticazione moderna con la libreria ADAL per l'autenticazione. L'Installazione guidata e lo stesso motore di sincronizzazione richiedono machine.config per una corretta configurazione, essendo applicazioni .NET.

Questo articolo illustra in che modo Fabrikam si connette ad Azure AD tramite il proxy. Il server proxy è denominato fabrikamproxy e usa la porta 8080.

Prima di tutto è necessario verificare che [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) sia configurato correttamente. ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
In alcuni blog non Microsoft è invece documentato che le modifiche devono essere apportate a miiserver.exe.config. Questo file viene però sovrascritto a ogni aggiornamento, quindi anche se funziona durante l'installazione iniziale, il sistema smetterà di funzionare dopo il primo aggiornamento. Per questo motivo è consigliabile aggiornare invece machine.config.

Per il server proxy devono essere aperti anche gli URL necessari. L'elenco ufficiale è documentato in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Quelle riportate nella tabella seguente sono le impostazioni minime assolutamente indispensabili per potersi connettere ad Azure AD. L'elenco non include le funzionalità facoltative, ad esempio il writeback delle password o Azure AD Connect Health. Le impostazioni documentate di seguito sono finalizzate alla risoluzione dei problemi relativi alla configurazione iniziale.

| URL | Porta | Descrizione |
| ---- | ---- | ---- |
| mscrl.microsoft.com | HTTP/80 | Usate per scaricare gli elenchi di CRL. |
| **.verisign.com | HTTP/80 | Usata per scaricare gli elenchi di CRL. |
| *.entrust.com | HTTP/80 | Usata per scaricare gli elenchi di CRL per MFA. |
| *.windows.net | HTTPS/443 | Usata per accedere ad Azure AD. |
| secure.aadcdn.microsoftonline-p.com | HTTPS/443 | Usata per MFA. |
| *.microsoftonline.com | HTTPS/443 | Usata per configurare la directory di Azure AD e importare/esportare i dati. |

## Errori nella procedura guidata
L'Installazione guidata usa due diversi contesti di sicurezza. Nella pagina **Connessione ad Azure AD** usa l'utente attualmente connesso. Nella pagina **Configura** passa all'[account che esegue il servizio per il motore di sincronizzazione](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Le configurazioni che si apportano al proxy vengono riflesse globalmente nel computer, quindi se si verifica un problema, questo si manifesterà probabilmente già nella pagina **Connessione ad Azure AD** della procedura guidata.

Ecco gli errori più comuni che vengono visualizzati nell'Installazione guidata.

### L'Installazione guidata non è stata configurata correttamente
Questo errore viene visualizzato quando la procedura guidata non riesce a raggiungere il proxy. ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Se viene visualizzato questo errore, verificare che [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) sia stato configurato correttamente.
- Se il file è corretto, seguire i passaggi in [Verificare la connettività proxy](#verify-proxy-connectivity) per vedere se il problema è presente anche all'esterno della procedura guidata.

### L'endpoint MFA non è raggiungibile
Questo errore viene visualizzato se l'endpoint **https://secure.aadcdn.microsoftonline-p.com** non è raggiungibile e l'amministratore globale ha abilitato il servizio MFA. ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Se viene visualizzato questo errore, verificare che l'endpoint secure.aadcdn.microsoftonline-p.com sia stato aggiunto al proxy.

### La password non può essere verificata
Se l'Installazione guidata riesce a connettersi ad Azure AD, ma non è possibile verificare la password, viene visualizzato questo messaggio: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- È una password temporanea e deve essere modificata? È effettivamente la password corretta? Provare ad accedere a https://login.microsoftonline.com da un computer diverso dal server Azure AD Connect e verificare che l'account sia utilizzabile.

### Verificare la connettività del proxy
Per verificare se il server Azure AD Connect può effettivamente connettersi al proxy e a Internet, si useranno alcuni comandi di PowerShell per vedere se il proxy consente o meno le richieste Web. A un prompt dei comandi di PowerShell eseguire `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. Tecnicamente la prima chiamata è per https://login.microsoftonline.com e funziona anch'essa, ma la risposta dell'altro URI è più veloce.

PowerShell userà la configurazione presente in machine.config per contattare il proxy. Le impostazioni in winhttp/netsh non dovranno influire su questi cmdlet.

Se il proxy è configurato correttamente, viene visualizzato uno stato operazione riuscita: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Se viene visualizzato **Impossibile connettersi al server remoto**, PowerShell sta provando a effettuare una chiamata diretta senza usare il proxy oppure il DNS non è configurato correttamente. Verificare che il file **machine.config** sia configurato correttamente. ![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Se il proxy non è configurato correttamente, verrà visualizzato un errore: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Errore | Testo dell'errore | Commento |
| ---- | ---- | ---- |
| 403 | Accesso negato | Il proxy non è stato aperto per l'URL richiesto. Rivedere la configurazione del proxy e assicurarsi che gli [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) siano stati aperti. |
| 407 | Autenticazione proxy obbligatoria | Il server proxy ha richiesto l'accesso, che non è stato fornito. Se il server proxy richiede l'autenticazione, assicurarsi di averla configurata in machine.config. Verificare anche di usare account di dominio sia per l'utente che esegue la procedura guidata sia per l'account del servizio. |

## Modello di comunicazione tra Azure AD Connect e Azure AD
Se sono stati eseguiti tutti i passaggi precedenti e ancora non è possibile connettersi, si può iniziare a esaminare i log di rete. Questa sezione documenta un normale modello di connettività riuscita. Sono elencati anche alcuni diversivi comuni che possono essere ignorati durante la lettura dei log di rete.

- Saranno eseguite chiamate a https://dc.services.visualstudio.com. Non è necessario che questo URL sia aperto nel proxy perché l'installazione riesca e le chiamate possono essere ignorate.
- Si noterà che la risoluzione DNS elenca gli host effettivi per lo spazio dei nomi DNS nsatc.net e altri spazi dei nomi non in microsoftonline.com. Tuttavia, non saranno eseguite richieste di servizi Web su nomi di server effettivi e non sarà necessario aggiungerli al proxy.
- Gli endpoint adminwebservice e provisioningapi, riportati di seguito nei log, sono endpoint di individuazione, quindi servono per trovare l'endpoint effettivo da usare e sono diversi a seconda dell'area.

### Log del proxy di riferimento
Ecco il dump del log di un proxy effettivo e la pagina dell'Installazione guidata dalla quale è stato rilevato. Le voci duplicate per lo stesso endpoint sono state rimosse. Può essere usato come riferimento per i propri log del proxy e di rete. Gli endpoint effettivi possono essere diversi nel proprio ambiente, in particolare quelli riportati in *corsivo*.

**Connessione ad Azure AD**

Time | URL
--- | ---
1/11/2016 8:31 | connect://login.microsoftonline.com:443
1/11/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:32 | connect://login.microsoftonline.com:443
1/11/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**Configura**

Time | URL
--- | ---
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**Sincronizzazione iniziale**

Time | URL
--- | ---
1/11/2016 8:48 | connect://login.windows.net:443
1/11/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

## Errori di autenticazione
Questa sezione illustra gli errori che possono essere restituiti da ADAL (la libreria di autenticazione usata da Azure AD Connect) e PowerShell. La spiegazione dell'errore può essere utile per comprendere i passaggi successivi.

### Concessione non valida
Nome utente o password non validi. Vedere [La password non può essere verificata](#the-password-cannot-be-verified) per altre informazioni.

### Tipo di utente sconosciuto
Non è possibile trovare o risolvere la directory di Azure AD. Si tenta di accedere con un nome utente in un dominio non verificato?

### Individuazione dell'area di autenticazione utente non riuscita
Problemi di configurazione di rete o del proxy. Non è possibile raggiungere la rete, vedere [Risolvere i problemi di connettività nell'Installazione guidata](#troubleshoot-connectivity-issues-in-the-installation-wizard)

### Password utente scaduta
Le credenziali sono scadute. Modificare la password.

### Errore di autorizzazione
Problema sconosciuto.

### Autenticazione annullata
La richiesta di autenticazione a più fattori (MFA) è stata annullata.

### Connessione a MS Online
L'autenticazione ha avuto esito positivo, ma Azure AD PowerShell ha un problema di autenticazione.

### Ruolo di Azure mancante
L'autenticazione ha avuto esito positivo. L'utente non è un amministratore globale.

### Privileged Identity Management
L'autenticazione ha avuto esito positivo. Privileged Identity Management è abilitata e l'utente attualmente non è un amministratore globale. Per altre informazioni, vedere [Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

### Informazioni aziendali non disponibili
L'autenticazione ha avuto esito positivo. Impossibile recuperare le informazioni aziendali da Azure AD.

### Recupero domini
L'autenticazione ha avuto esito positivo. Impossibile recuperare le informazioni sul dominio da Azure AD.

## Procedure di risoluzione dei problemi per le versioni precedenti.
L'Assistente per l'accesso è stato ritirato a partire dalle versioni con numero di build 1.1.105.0, rilasciata nel mese di febbraio 2016. Questa sezione e la configurazione non dovrebbero essere più necessarie, ma vengono conservate come riferimento.

Per consentire il funzionamento dell'Assistente per l'accesso, è necessario configurare winhttp Questa operazione può essere eseguita con [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity). ![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### L'Assistente per l'accesso non è stato configurato correttamente
Questo errore viene visualizzato quando l'Assistente per l'accesso non riesce a raggiungere il proxy o il proxy non consente la richiesta. ![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Se viene visualizzato questo errore, esaminare la configurazione del proxy in [netsh](active-directory-aadconnect-prerequisites.md#connectivity) e verificare che sia corretta. ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Se è corretta, seguire la procedura descritta in [Verificare la connettività proxy](#verify-proxy-connectivity) per vedere se il problema è presente anche all'esterno della procedura guidata.

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0518_2016-->