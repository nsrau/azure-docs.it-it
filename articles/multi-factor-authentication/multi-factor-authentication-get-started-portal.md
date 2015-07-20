<properties 
	pageTitle="Distribuzione del portale utenti per il server Azure Multi-Factor Authentication" 
	description="Questa è la pagina di Azure Multi-Factor Authentication che descrive come iniziare a utilizzare Azure MFA e ADFS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Distribuzione del portale utenti per il server Azure Multi-Factor Authentication

Il portale utenti consente all'amministratore di installare e configurare il portale utenti Azure Multi-Factor Authentication. Il portale utenti è un sito Web IIS che consente agli utenti di registrarsi in Azure Multi-Factor Authentication e mantenere i propri account. L'utente può modificare il numero di telefono, modificare il PIN o ignorare la modalità Azure Multi-Factor Authentication all'accesso successivo.

Gli utenti potranno accedere al portale utenti utilizzando il proprio normale nome utente e password e completeranno una chiamata di Azure Multi-Factor Authentication oppure risponderanno a domande sicurezza per completare l'autenticazione. Se è consentita la registrazione, l'utente potrà configurare numero di telefono e PIN la prima volta che accederà al portale utenti.

È possibile che vengano impostati degli amministratori del portale utenti a cui venga concessa l'autorizzazione di aggiungere nuovi utenti e aggiornare gli utenti esistenti.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Distribuzione del portale utenti nello stesso server di Azure Multi-Factor Authentication

I seguenti prerequisiti sono necessari per l'installazione del portale utenti nello stesso server di Azure multi-Factor Authentication:

- È necessario installare IIS con ASP.NET e la compatibilità della metabase di IIS 6 (per IIS 7 o versione successiva) 
- L'utente connesso deve disporre dei diritti amministrativi per il computer e il dominio, se necessario. Ciò è dovuto al fatto che l'account deve disporre delle autorizzazioni per creare gruppi di sicurezza di Active Directory.

### Per distribuire il portale utenti per il server Azure Multi-Factor Authentication

<ol>
<li>All'interno del server Azure Multi-Factor Authentication: fare clic sull'icona del portale utenti nel menu a sinistra, quindi scegliere il pulsante il per installare il portale utenti. <li>Fare clic su Avanti. <li>Fare clic su Avanti. <li>Se il computer è aggiunto a un dominio e la configurazione di Active Directory per proteggere la comunicazione tra il portale utenti e il servizio Azure Multi-Factor Authentication è incompleta, verrà visualizzato il passaggio relativo alla configurazione di Active Directory. Fare clic sul pulsante Avanti per completare automaticamente questa configurazione. <li>Fare clic su Avanti. <li>Fare clic su Avanti. <li>Fare clic su Chiudi. <li>Aprire un browser Web da qualsiasi computer e passare all'URL in cui è stato installato il portale per gli utenti (ad esempio https://www.publicwebsite.com/MultiFactorAuth). Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Distribuzione del portale utenti del server di Azure Multi-Factor Authentication in un server separato

Per utilizzare l'app Azure Multi-Factor Authentication, è necessario quanto segue in modo che l'app possa comunicare correttamente con il portale utenti:

Per conoscere i requisiti hardware e software, vedere la relativa sezione.

- È necessario utilizzare la versione 6.0 o successiva del server Azure Multi-Factor Authentication.
- Il portale utenti deve essere installato in un server Web con connessione Internet su cui è in esecuzione Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x o versione successiva.
- Quando si utilizza IIS 6.x, controllare che ASP.NET v2.0.50727 sia installato, registrato e impostato su Consentito.
- I servizi ruolo richiesti quando si utilizza IIS 7.x o versione successiva includono ASP.NET e Compatibilità metabase IIS 6.
- Il portale utenti deve essere protetto con un certificato SSL.
- L'SDK del servizio Web Azure Multi-Factor Authentication deve essere installato in IIS 6.x, IIS 7.x o versione successiva sul server su cui è installato Azure Multi-Factor Authentication.
- L'SDK del servizio Web Azure Multi-Factor Authentication deve essere protetto con un certificato SSL.
- Il portal utenti deve essere in grado di connettersi all'SDK servizio Web Azure Multi-Factor Authentication tramite SSL.
- Il portal utenti deve essere in grado di eseguire l'autenticazione all'SDK servizio Web Azure Multi-Factor Authentication utilizzando le credenziali di un account di servizio membro di un gruppo di sicurezza denominato "Amministratori PhoneFactor". Questo account e gruppo del servizio esistono in Active Directory se il server Azure Multi-Factor Authentication è in esecuzione su un server appartenente a un dominio. Questo account e gruppo del servizio esistono in locale sul server Azure Multi-Factor Authentication se non è aggiunto a un dominio.

Per installare il portale utenti su un server diverso da Azure Multi-Factor Authentication, è necessario eseguire questi tre passaggi:

1. Installare l'SDK del servizio Web
2. Installare il portale utenti
3. Configurare le impostazioni del portale utenti nel server Azure Multi-Factor Authentication


### Installare l'SDK del servizio Web

Se l'SDK del servizio Web Azure Multi-Factor Authentication non è già installato nel server Azure Multi-Factor Authentication, accedere al server e aprire il server Azure Multi-Factor Authentication. Fare clic sull'icona SDK servizio Web, fare clic sul pulsante Installa SDK servizio Web... e seguire le istruzioni visualizzate. L'SDK servizio Web deve essere protetto con un certificato SSL. Un certificato autofirmato è accettabile per questo scopo, ma deve essere importato nell'archivio "Autorità di certificazione radice disponibile nell'elenco locale" dell'account del computer locale sul server Web del portale utenti per poter essere considerato attendibile all'avvio della connessione SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Installare il portale utenti

Prima di installare il portale utenti in un server separato, tenere presente quanto segue:

- È utile aprire un browser Web nel server Web connesso a Internet e andare all'URL dell'SDK servizio Web riportato nel file web.config. Se il browser è in grado di accedere al servizio Web, chiederà le credenziali. Immettere il nome utente e la password immessi nel file web.config esattamente come visualizzati nel file. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.
- Se un proxy inverso o un firewall si trova prima del server Web del portale utenti ed esegue l'offload SSL, è possibile modificare il file web.config del servizio del portale utenti e aggiungere la chiave seguente alla sezione <appSettings> in modo che il portale utenti possa utilizzare il protocollo http anziché https. <add key="SSL_REQUIRED" value="false"/>

#### Per installare il portale utenti

<ol>




<li>Aprire Esplora risorse sul server Azure Multi-Factor Authentication e andare alla cartella in cui è installato il server Azure Multi-Factor Authentication (ad esempio, C:\Programmi\Multi-Factor Authentication Server). Scegliere la versione a 32 bit o 64 bit del file di installazione MultiFactorAuthenticationUserPortalSetup in base alle esigenze del server in cui verrà installato il portale utenti. Copiare il file di installazione nel server connesso a Internet.</li>

<li>Sul server Web connesso a Internet, è necessario eseguire il file di installazione con diritti di amministratore. Il modo più semplice per eseguire questa operazione consiste nell'aprire un prompt dei comandi come amministratore e andare al percorso in cui è stato copiato il file di installazione.</li>

<li>Eseguire il file di installazione MultiFactorAuthenticationUserPortalSetup64; modificare il nome del sito e della directory virtuale, secondo necessità.</li>

<li>Dopo aver completato l'installazione del portale utenti andare a C:\inetpub\wwwroot\MultiFactorAuth (o alla directory appropriata in base al nome della directory virtuale) e modificare il file web.config.</li>

<li>Individuare la chiave USE_WEB_SERVICE_SDK e modificare il valore da false a true. Individuare le chiavi WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e impostare i valori per il nome utente e la password dell'account del servizio membro del gruppo di sicurezza Amministratori PhoneFactor (vedere la sezione Requisiti sopra). Assicurarsi di immettere il nome utente e la password tra virgolette alla fine della riga (value=””/>). Si consiglia di utilizzare un nome utente completo (ad esempio dominio\nome utente o computer\nome utente).</li>

<li>Individuare l'impostazione pfup_pfwssdk_PfWsSdk e sostituire il valore "http://localhost:4898/PfWsSdk.asmx" con l'URL dell'SDK servizio Web in esecuzione nel server Azure Multi-Factor Authentication (ad esempio, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Poiché viene utilizzato il protocollo SSL per la connessione, è necessario fare riferimento all'SDK servizio Web per nome del server e non all'indirizzo IP poiché il certificato SSL verrà rilasciato per il nome del server e l'URL utilizzato deve corrispondere al nome del certificato. Se il nome del server non viene risolto in un indirizzo IP dal server con connessione Internet, aggiungere una voce al file hosts sul server per il mapping del nome del server Azure Multi-Factor Authentication con il relativo indirizzo IP. Salvare il file web.config dopo che sono state apportate le modifiche.</li>

<li>Se il sito Web in cui è installato il portal utenti (ad esempio, il sito Web predefinito) non è già stato associato a un certificato firmato pubblicamente, installare il certificato sul server se non già installato, aprire Gestione IIS e associare il certificato al sito Web.</li>

<li>Aprire un browser Web da qualsiasi computer e andare all'URL in cui è stato installato il portale utenti (ad esempio, https://www.publicwebsite.com/MultiFactorAuth ). Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.</li>

## Configurare le impostazioni del portale utenti nel server Azure Multi-Factor Authentication
Il portale utenti è ora installato. È necessario configurare il server Azure Multi-Factor Authentication per l'utilizzo con il portale.

### Per configurare le impostazioni del portale utenti nel server Azure Multi-Factor Authentication




1. Nel server Azure Multi-Factor Authentication fare clic sull'icona del portale utenti. Nella scheda Impostazioni immettere l'URL del portale utenti nella casella di testo URL del portale utenti. Questo URL verrà inserito in messaggi di posta elettronica inviati agli utenti quando vengono importati nel server Azure Multi-Factor Authentication se è stata abilitata la funzionalità di posta elettronica.
2. Scegliere le impostazioni che si desidera utilizzare nel portale utenti. Ad esempio, se gli utenti sono autorizzati a controllare i relativi metodi di autenticazione, verificare che sia selezionata l'opzione Consenti agli utenti insieme ai metodi tra cui scegliere.
3. Fare clic sul collegamento relativo alla Guida nell'angolo superiore destro per comprendere le impostazioni visualizzate.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>

<!---HONumber=July15_HO2-->