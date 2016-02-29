<properties 
	pageTitle="Introduzione al servizio Web app per dispositivi mobili del server MFA:" 
	description="L'app Azure Multi-Factor Authentication offre un'opzione di autenticazione fuori banda aggiuntiva. Consente al server MFA di inviare notifiche push agli utenti." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Introduzione al servizio Web app per dispositivi mobili del server MFA:

L'app Azure Multi-Factor Authentication offre un'opzione di autenticazione fuori banda aggiuntiva. Invece di effettuare una chiamata automatizzata o di inviare un SMS all'utente durante l'accesso, Azure Multi-Factor Authentication inoltra una notifica push all'app Azure Multi-Factor Authentication sul tablet o sullo smartphone dell'utente. L'utente tocca semplicemente "Esegui autenticazione" (o immette un PIN e tocca "Esegui autenticazione") nell'applicazione per eseguire l'accesso.

Per utilizzare l'app Azure Multi-Factor Authentication, è necessario quanto segue in modo che l'app possa comunicare correttamente con il servizio Web app per dispositivi mobili:

- Vedere Requisiti hardware e software per conoscere i requisiti hardware e software
- È necessario usare la versione 6.0 o successiva del server Azure Multi-Factor Authentication
- Il servizio Web app per dispositivi mobili deve essere installato in un server Web con connessione Internet che esegue Microsoft® Internet Information Services (IIS) 6.x o IIS 7.x
- Quando si utilizza IIS 6.x, controllare che ASP.NET v2.0.50727 sia installato, registrato e impostato su Consentito
- I servizi ruolo richiesti quando si utilizza IIS 7.x includono ASP.NET e Compatibilità metabase IIS 6
- Il servizio Web app per dispositivi mobili deve essere accessibile tramite un URL pubblico
- Il servizio Web app per dispositivi mobili deve essere protetto con un certificato SSL.
- L'SDK del servizio Web Azure Multi-Factor Authentication deve essere installato in IIS 6.x o IIS 7.x sul server Azure Multi-Factor Authentication
- Il servizio Web Azure Multi-Factor Authentication deve essere protetto con un certificato SSL.
- Il servizio Web app per dispositivi mobili deve essere in grado di connettersi all'SDK servizio Web Azure Multi-Factor Authentication tramite SSL
- Il servizio Web app per dispositivi mobili deve essere in grado di eseguire l'autenticazione all'SDK servizio Web Azure Multi-Factor Authentication utilizzando le credenziali di un account di servizio membro di un gruppo di sicurezza denominato "Amministratori PhoneFactor". Questo account e gruppo del servizio esistono in Active Directory se il server Azure Multi-Factor Authentication è in esecuzione su un server appartenente a un dominio. Questo account e gruppo del servizio esistono in locale sul server Azure Multi-Factor Authentication se non è aggiunto a un dominio.


Per installare il portale utenti su un server diverso da Azure Multi-Factor Authentication, è necessario eseguire questi tre passaggi:

1. Installare SDK servizio Web
2. Installare il servizio Web app per dispositivi mobili
3. Configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication
4. Attivare l'app Azure Multi-Factor Authentication per gli utenti finali

## Installare SDK servizio Web

Se l'SDK del servizio Web Azure Multi-Factor Authentication non è già installato nel server Azure Multi-Factor Authentication, accedere al server e aprire il server Azure Multi-Factor Authentication. Fare clic sull'icona SDK servizio Web, fare clic sul pulsante Installa SDK servizio Web... e seguire le istruzioni visualizzate. L'SDK servizio Web deve essere protetto con un certificato SSL. Un certificato autofirmato è accettabile per questo scopo, ma deve essere importato nell'archivio "Autorità di certificazione radice disponibile nell'elenco locale" dell'account del computer locale sul server Web del portale utenti per poter essere considerato attendibile all'avvio della connessione SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Installare il servizio Web app per dispositivi mobili
Prima di installare il servizio Web app per dispositivi mobili, tenere presente quanto segue:

- Se il portale utenti Azure Multi-Factor Authentication è già installato nel server con connessione Internet, il nome utente, la password e l'URL per SDK servizio Web possono essere copiati dal file web.config del portale utenti. 
- È utile aprire un Web browser sul server Web con connessione Internet e andare all'URL di SDK servizio Web immesso nel file web.config. Se il browser è in grado di accedere al servizio Web, chiederà le credenziali. Immettere il nome utente e la password immessi nel file web.config esattamente come visualizzati nel file. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.
- Se un proxy inverso o un firewall si trova prima del server Web del servizio Web app per dispositivi mobili ed esegue l'offload SSL, è possibile modificare il file web.config del servizio Web app per dispositivi mobili e aggiungere la chiave seguente alla sezione <appSettings> in modo che il servizio Web app per dispositivi mobili possa utilizzare il protocollo http anziché https. Tuttavia, SSL è comunque richiesto dall'app per dispositivi mobili per il proxy firewall/inverso. <add key="SSL_REQUIRED" value="false"/> 

### Per installare il servizio Web app per dispositivi mobili

<ol>
<li>Aprire Esplora risorse sul server Azure Multi-Factor Authentication e andare alla cartella in cui è installato il server Azure Multi-Factor Authentication (ad esempio, C:\Program Files\Azure Multi-Factor Authentication). Scegliere la versione a 32 bit o 64 bit del file di installazione Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup come appropriato per il server in cui verrà installato il servizio Web app per dispositivi mobili. Copiare il file di installazione nel server con connessione Internet.</li>

<li>Sul server Web connesso a Internet, è necessario eseguire il file di installazione con diritti di amministratore. Il modo più semplice per eseguire questa operazione consiste nell'aprire un prompt dei comandi come amministratore e andare al percorso in cui è stato copiato il file di installazione.</li>

<li>Eseguire il file di installazione Multi-Factor AuthenticationMobileAppWebServiceSetup, se lo si desidera modificare il sito e assegnare alla directory virtuale un nome breve, ad esempio "PA". Un nome della directory virtuale breve è consigliato poiché gli utenti devono immettere l'URL del servizio Web app per dispositivi mobili nel dispositivo mobile durante l'attivazione.</li>

<li>Dopo aver completato l'installazione di Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, andare a C:\inetpub\wwwroot\PA (o alla directory appropriata in base al nome della directory virtuale) e modificare il file web.config.</li>

<li>Individuare le chiavi WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e impostare i valori per il nome utente e la password dell'account del servizio membro del gruppo di sicurezza Amministratori PhoneFactor (vedere la sezione Requisiti sopra). Può trattarsi dello stesso account utilizzato come identità del portale utenti di Azure Multi-Factor Authentication se è stato precedentemente installato. Assicurarsi di immettere il nome utente e la password tra virgolette alla fine della riga (value=””/>). Si consiglia di utilizzare un nome utente completo (ad esempio dominio\nome utente o computer\nome utente).</li>

<li>Individuare l'impostazione pfMobile App Web Service_pfwssdk_PfWsSdk e sostituire il valore "http://localhost:4898/PfWsSdk.asmx" con l'URL dell'SDK servizio Web in esecuzione nel server Azure Multi-Factor Authentication (ad esempio, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Poiché viene utilizzato il protocollo SSL per la connessione, è necessario fare riferimento all'SDK servizio Web per nome del server e non all'indirizzo IP poiché il certificato SSL verrà rilasciato per il nome del server e l'URL utilizzato deve corrispondere al nome del certificato. Se il nome del server non viene risolto in un indirizzo IP dal server con connessione Internet, aggiungere una voce al file hosts sul server per il mapping del nome del server Azure Multi-Factor Authentication con il relativo indirizzo IP. Salvare il file web.config dopo che sono state apportate le modifiche.</li>

<li>Se il sito Web in cui è installato il servizio Web app per dispositivi mobili (ad esempio, il sito Web predefinito) non è già stato associato a un certificato firmato pubblicamente, installare il certificato sul server se non già installato, aprire Gestione IIS e associare il certificato al sito Web.</li>

<li>Aprire un Web browser da qualsiasi computer e andare all'URL in cui è stato installato il servizio Web app per dispositivi mobili (ad esempio, https://www.publicwebsite.com/PA). Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.</li>

### Configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication
Il servizio Web app per dispositivi mobili è ora installato. È necessario configurare il server Azure Multi-Factor Authentication per l'utilizzo con il portale.

#### Per configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication

1. Nel server Azure Multi-Factor Authentication fare clic sull'icona del portale utenti. Se gli utenti sono autorizzati a controllare i relativi metodi di autenticazione, nella scheda Impostazioni, in Consenti agli utenti di selezionare il metodo, selezionare app per dispositivi mobili. Se questa funzionalità non è attivata, agli utenti finali verrà chiesto di contattare l'Help Desk per completare l'attivazione dell'app per dispositivi mobili.
2. Selezionare la casella Consenti agli utenti di attivare l'app per dispositivi mobili.
3. Selezionare la casella Consenti registrazione utente.
4. Fare clic sull'icona dell'app per dispositivi mobili.
5. Immettere l'URL utilizzato con la directory virtuale creata durante l'installazione di Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. È necessario inserire un nome dell'account nello spazio fornito. Il nome della società verrà visualizzato nell'applicazione mobile. Se lasciato vuoto, verrà visualizzato il nome del provider di multi-factor authentication creato nel portale di gestione di Azure. 



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=AcomDC_0218_2016-->