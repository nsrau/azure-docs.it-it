<properties 
	pageTitle="Distribuzione del portale utenti per il server Azure Multi-Factor Authentication" 
	description="Questa è la pagina di Azure Multi-Factor Authentication che descrive come iniziare a utilizzare Azure MFA e ADFS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/16/2016" 
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

1. All'interno del server Azure Multi-Factor Authentication: fare clic sull'icona del portale utenti nel menu a sinistra, quindi scegliere il pulsante Installa portale utenti. 
1. Fare clic su Avanti.
1. Fare clic su Avanti.
1. Se il computer fa parte di un dominio e la configurazione di Active Directory per la sicurezza della comunicazione tra il portale utenti e il servizio Azure Multi-Factor Authentication non è completa, verrà visualizzato il passaggio in Active Directory. Fare clic sul pulsante Avanti per completare automaticamente questa configurazione.
1. Fare clic su Avanti.
1. Fare clic su Avanti.
1. Fare clic su Chiudi.
1. Aprire un Web browser da qualsiasi computer e passare all'URL in cui è stato installato il portale utenti (ad esempio https://www.publicwebsite.com/MultiFactorAuth). Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.

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

1. Aprire Esplora risorse sul server Azure Multi-Factor Authentication e andare alla cartella in cui è installato il server Azure Multi-Factor Authentication (ad esempio, C:\\Programmi\\Multi-Factor Authentication Server). Scegliere la versione a 32 bit o 64 bit del file di installazione MultiFactorAuthenticationUserPortalSetup in base alle esigenze del server in cui verrà installato il portale utenti. Copiare il file di installazione nel server connesso a Internet.
2. Sul server Web connesso a Internet, è necessario eseguire il file di installazione con diritti di amministratore. Il modo più semplice per eseguire questa operazione consiste nell'aprire un prompt dei comandi come amministratore e andare al percorso in cui è stato copiato il file di installazione.
3. Eseguire il file di installazione MultiFactorAuthenticationUserPortalSetup64; modificare il nome del sito e della directory virtuale, secondo necessità.
4. Dopo aver completato l'installazione del portale utenti andare a C:\\inetpub\\wwwroot\\MultiFactorAuth (o alla directory appropriata in base al nome della directory virtuale) e modificare il file web.config.
5. Individuare la chiave USE\_WEB\_SERVICE\_SDK e modificare il valore da false a true. Individuare le chiavi WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME e WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD e impostare i valori per il nome utente e la password dell'account del servizio membro del gruppo di sicurezza Amministratori PhoneFactor (vedere la sezione Requisiti sopra). Assicurarsi di immettere il nome utente e la password tra virgolette alla fine della riga (value=””/>). È consigliabile usare un nome utente completo (ad esempio dominio\\nome utente o computer\\nome utente).
6. Individuare l'impostazione pfup\_pfwssdk\_PfWsSdk e modificare il valore da "http://localhost:4898/PfWsSdk.asmx" nell'URL dell'SDK del servizio Web in esecuzione sul server Azure Multi-Factor Authentication (ad esempiohttps://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Poiché viene utilizzato il protocollo SSL per la connessione, è necessario fare riferimento all'SDK servizio Web per nome del server e non all'indirizzo IP poiché il certificato SSL verrà rilasciato per il nome del server e l'URL utilizzato deve corrispondere al nome del certificato. Se il nome del server non viene risolto in un indirizzo IP dal server con connessione Internet, aggiungere una voce al file hosts sul server per il mapping del nome del server Azure Multi-Factor Authentication con il relativo indirizzo IP. Salvare il file web.config dopo che sono state apportate le modifiche.
7. Se il sito Web in cui è installato il portal utenti (ad esempio, il sito Web predefinito) non è già stato associato a un certificato firmato pubblicamente, installare il certificato sul server se non già installato, aprire Gestione IIS e associare il certificato al sito Web.
8. Aprire un Web browser da qualsiasi computer e passare all'URL in cui è stato installato il portale utenti (ad esempio https://www.publicwebsite.com/MultiFactorAuth). Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.



## Configurare le impostazioni del portale utenti nel server Azure Multi-Factor Authentication
Il portale utenti è ora installato. È necessario configurare il server Azure Multi-Factor Authentication per l'utilizzo con il portale.

Il server Azure Multi-Factor Authentication offre diverse opzioni per il portale utenti. La tabella seguente include un elenco di queste opzioni e una spiegazione dell'uso a cui sono destinate.

Impostazioni del portale utenti|Descrizione|
:------------- | :------------- | 
URL portale utenti| Consente di immettere l'URL in cui viene ospitato il portale.
Autenticazione primaria| Consente di specificare il tipo di autenticazione da usare quando si accede al portale. Autenticazione di Windows, Radius o LDAP.
Consenti agli utenti di accedere|Consente agli utenti di immettere un nome utente e una password nella pagina di accesso del portale utenti. Se l'impostazione non è selezionata, le caselle risulteranno disattivate.
Consenti registrazione utente|Consente all'utente di registrarsi in Multi-Factor Authentication visualizzando una schermata di configurazione che richiede informazioni aggiuntive, ad esempio il numero di telefono. Richiedi telefono di backup consente agli utenti di specificare un numero di telefono secondario. Richiedi token OATH di terze parti consente agli utenti di specificare un token OATH di terze parti.
Consenti agli utenti di avviare il bypass monouso| Consente agli utenti di avviare un bypass monouso. Se un utente configura questa impostazione, verrà applicata all'accesso successivo. Richiedi secondi per bypass presenta all'utente una casella in cui può modificare il valore predefinito di 300 secondi. In caso contrario, il bypass monouso è valido solo per 300 secondi.
Consenti agli utenti di selezionare il metodo| Consente agli utenti di specificare il metodo di contatto primario. Può trattarsi di una telefonata, un SMS, un'app per dispositivi mobili o un token OATH.
Consenti agli utenti di selezionare la lingua| Consente all'utente di cambiare la lingua usata per la telefonata, l'SMS, l'app per dispositivi mobili o il token OATH.
Consenti agli utenti di attivare l'app mobile| Consente agli utenti di generare un codice di attivazione per completare il processo di attivazione dell'app per dispositivi mobili usato con il server. È anche possibile impostare il numero di dispositivi in cui possono attivare questa funzione. Il numero è compreso tra 1 e 10.
Usa domande di sicurezza per il fallback|Consente di usare domande di sicurezza in caso di errore di Multi-Factor Authentication. È possibile specificare il numero di domande di sicurezza a cui deve essere fornita una risposta corretta.
Consenti agli utenti di associare token OATH di terze parti| Consente agli utenti di specificare un token OATH di terze parti.
Usa token OATH per fallback|Consente l'uso di un token OATH qualora l'autenticazione tramite Multi-Factor Authentication non riesca. È anche possibile specificare il timeout della sessione in minuti.
Abilitazione della registrazione|Abilita la registrazione nel portale utenti. I file di log si trovano in: C:\\Programmi\\Multi-Factor Authentication Server\\Logs.

La maggior parte di queste impostazioni è visibile all'utente dopo che è stato abilitato e ha eseguito l'accesso al portale utenti.

![Impostazioni del portale utenti](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### Per configurare le impostazioni del portale utenti nel server Azure Multi-Factor Authentication




1. Nel server Azure Multi-Factor Authentication fare clic sull'icona del portale utenti. Nella scheda Impostazioni immettere l'URL del portale utenti nella casella di testo URL del portale utenti. Questo URL verrà inserito in messaggi di posta elettronica inviati agli utenti quando vengono importati nel server Azure Multi-Factor Authentication se è stata abilitata la funzionalità di posta elettronica.
2. Scegliere le impostazioni che si desidera utilizzare nel portale utenti. Ad esempio, se gli utenti sono autorizzati a controllare i relativi metodi di autenticazione, verificare che sia selezionata l'opzione Consenti agli utenti insieme ai metodi tra cui scegliere.
3. Fare clic sul collegamento relativo alla Guida nell'angolo superiore destro per comprendere le impostazioni visualizzate.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Scheda Amministratori
Questa scheda consente semplicemente di aggiungere gli utenti che hanno privilegi amministrativi. Quando si aggiunge un amministratore, è possibile ottimizzare le autorizzazioni che riceve. In questo modo, è possibile concedere all'amministratore solo le autorizzazioni necessarie. Fare semplicemente clic sul pulsante Aggiungi, selezionare un utente e le relative autorizzazioni e quindi fare clic su Aggiungi.

![Amministratori del portale utenti](./media/multi-factor-authentication-get-started-portal/admin.png)


## Domande di sicurezza
Questa scheda consente di specificare le domande di sicurezza a cui gli utenti dovranno fornire risposte, se l'opzione Usa domande di sicurezza per il fallback è selezionata. Il server Azure Multi-Factor Authenticaton include domande predefinite che è possibile usare. È anche possibile modificare l'ordine o aggiungere domande personalizzate. Quando si aggiungono domande personalizzate, si può anche specificare la lingua da usare per visualizzarle.

![Domande di sicurezza del portale utenti](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Sessioni passate

## SAML
Consente di configurare il portale utenti perché accetti attestazioni da un provider di identità usando SAML. Si possono specificare la sessione di timeout, il certificato di verifica e l'URL di reindirizzamento per la disconnessione.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## Indirizzi IP attendibili
Questa scheda consente di specificare singoli indirizzi IP o intervalli di indirizzi IP che possono essere aggiunti in modo che, se un utente accede con uno di questi indirizzi IP, Multi-Factor Authentication venga ignorato.

![Indirizzi IP attendibili del portale utenti](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Registrazione utente in modalità self-service
Se si vuole che gli utenti accedano e si registrino, è necessario selezionare le opzioni Consenti agli utenti di accedere e Consenti registrazione utente. Tenere presente che le impostazioni selezionate influiranno sull'esperienza di accesso degli utenti.

Ad esempio, quando un utente accede al portale utenti e fa clic sul pulsante Accedi, viene visualizzata la pagina Configurazione utenti di Azure Multi-Factor Authentication. A seconda della configurazione di Azure Multi-Factor Authentication, l'utente potrebbe essere in grado di selezionare il metodo di autenticazione.

Se l'utente seleziona il metodo di autenticazione tramite una chiamata vocale o è stato preconfigurato per usare questo metodo, la pagina richiederà l'immissione del numero di telefono principale e l'interno, se applicabile. Si può anche consentire all'utente di immettere un numero di telefono di backup.

![Indirizzi IP attendibili del portale utenti](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se l'utente deve usare un PIN quando esegue l'autenticazione, la pagina richiederà anche di immettere un PIN. Dopo aver immesso il numero di telefono e il PIN (se applicabile), l'utente fa clic sul pulsante Chiama per autenticare utente corrente. Azure Multi-Factor Authentication esegue l'autenticazione tramite una chiamata al numero di telefono principale dell'utente. L'utente deve rispondere alla chiamata e immettere il PIN (se applicabile) e premere il tasto # per proseguire con il passaggio successivo del processo di autoregistrazione.

Se l'utente seleziona il metodo di autenticazione tramite SMS o è stato preconfigurato per l'uso di questo metodo, la pagina richiederà il numero di telefono cellulare. Se l'utente deve usare un PIN quando esegue l'autenticazione, la pagina richiederà anche di immettere un PIN. Dopo aver immesso il numero di telefono e il PIN (se applicabile), l'utente fa clic sul pulsante Invia SMS per autenticare utente corrente. Azure Multi-Factor Authentication esegue l'autenticazione tramite SMS al cellulare dell'utente. L'utente deve ricevere l'SMS che include un passcode monouso e rispondere al messaggio con il passcode e il PIN (se applicabile) per proseguire con il passaggio successivo del processo di autoregistrazione.

![SMS del portale utenti](./media/multi-factor-authentication-get-started-portal/text.png)

Se l'utente seleziona il metodo di autenticazione tramite app per dispositivi mobili o è stato preconfigurato per l'uso di questo metodo, la pagina richiederà di installare l'app Azure Multi-Factor Authentication nel dispositivo e generare un codice di attivazione. Dopo aver installato l'app Azure Multi-Factor Authentication, l'utente fa clic sul pulsante Genera codice di attivazione.

>[AZURE.NOTE]Per usare l'app Azure Multi-Factor Authentication, l'utente deve abilitare le notifiche push per il dispositivo.

La pagina visualizza quindi un codice di attivazione e un URL con l'immagine di un codice a barre. Se l'utente deve usare un PIN quando esegue l'autenticazione, la pagina richiederà anche di immettere un PIN. L'utente immette il codice di attivazione e l'URL nell'app Azure Multi-Factor Authentication oppure usa lo scanner del codice a barre per effettuarne la scansione e quindi fa clic sul pulsante Attiva.

Dopo aver completato l'attivazione, l'utente fa clic sul pulsante Esegui autenticazione utente corrente. Azure Multi-Factor Authentication esegue l'autenticazione sull'app per dispositivi mobili dell'utente. L'utente deve immettere il PIN (se applicabile) e scegliere il pulsante Esegui autenticazione nell'app per dispositivi mobili per proseguire con il passaggio successivo del processo di autoregistrazione.


Se gli amministratori hanno configurato il server Azure Multi-Factor Authentication per raccogliere domande e risposte di sicurezza, viene visualizzata la pagina Domande di sicurezza. L'utente deve selezionare quattro domande di sicurezza e fornire le risposte alle domande selezionate.

![Domande di sicurezza del portale utenti](./media/multi-factor-authentication-get-started-portal/secq.png)

A questo punto la registrazione automatica è stata completata e l'utente è connesso al portale utenti. Se consentito dagli amministratori, gli utenti possono accedere al portale utenti in qualsiasi momento per modificare numeri di telefono, PIN, metodi di autenticazione e domande di sicurezza.

 

<!---HONumber=AcomDC_0518_2016-->