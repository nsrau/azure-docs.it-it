<properties
	pageTitle="Domande frequenti su Azure Multi-Factor Authentication"
	description="Fornisce un elenco di domande e risposte frequenti correlate ad Azure Multi-Factor Authentication. Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un livello di sicurezza aggiuntivo agli accessi e alle transazioni degli utenti."
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
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="billmath"/>  

# Domande frequenti su Azure Multi-Factor Authentication


Queste domande frequenti offrono risposte su Azure Multi-Factor Authentication e sull'uso del servizio Multi-Factor Authentication, incluse domande relative al modello di fatturazione e all'usabilità.

## Generale

**D: Come è possibile ottenere assistenza per Azure Multi-Factor Authentication?**

- [Cercare nella Microsoft Knowledge Base](https://www.microsoft.com/it-IT/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)

  Cercare nella Knowledge Base le soluzioni ai problemi tecnici comuni.

- [Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

  È possibile cercare e sfogliare le domande e risposte tecniche dalla community o porre le proprie domande nei [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- [Reimpostazione delle password](mailto:phonefactorsupport@microsoft.com)

  I clienti legacy di PhoneFactor che hanno domande o necessitano di assistenza per reimpostare una password possono usare il collegamento per la [reimpostazione della password](mailto:phonefactorsupport@microsoft.com) per aprire una richiesta di assistenza.

- [Supporto per il server Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947)
  
  Utilizzare questo collegamento per contattare un supporto Professional di Microsoft. Verranno poste alcune domande per determinare le opzioni di supporto disponibili, tra cui l'invio di messaggi di posta elettronica, l'invio online o il supporto telefonico.



**D: In che modo il server Azure Multi-Factor Authentication gestisce i dati utente?**

Quando si usa il server Multi-Factor Authentication, i dati utente vengono archiviati solo nel server locale. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue l'autenticazione a due fattori, il server Multi-Factor Authentication invia dati al servizio cloud Azure Multi-Factor Authentication per l'autenticazione. Quando queste richieste di autenticazione vengono inviate al servizio cloud, i dati prelevati dai campi seguenti vengono inviati nella richiesta e nei log, in modo che siano disponibili nei report di autenticazione e utilizzo del cliente. Alcuni campi sono facoltativi ed è possibile configurarli nel server multi-Factor Authentication. La comunicazione dal server Multi-Factor Authentication al servizio cloud Multi-Factor Authentication avviene tramite Secure Sockets Layer (SSL) o Transport Layer Security (TLS) sulla porta 443 in uscita. Di seguito sono riportati i campi di dati inclusi nei log di autenticazione a due fattori:

- **ID univoco:** nome utente o ID del server Multi-Factor Authentication locale
- **Nome e cognome:** facoltativo
- **Indirizzo di posta elettronica:** facoltativo
- **Numero di telefono:** quando si esegue una chiamata vocale o l'autenticazione tramite SMS
- **Token del dispositivo:** quando si esegue l'autenticazione con l'app per dispositivi mobili
- **Modalità di autenticazione**
- **Risultato dell'autenticazione**
- **Nome del server Multi-Factor Authentication**
- **IP del server Multi-Factor Authentication**
- **IP client:** se disponibile



Oltre a questi campi, il risultato dell'autenticazione (esito positivo o rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme ai dati di autenticazione e sono disponibili nei report di autenticazione e utilizzo.




## Fatturazione

**D: Le chiamate telefoniche o i messaggi SMS usati per autenticare gli utenti verranno addebitati all'organizzazione?**

Tutti i costi sono distribuiti nel costo del servizio per utente o per autenticazione. Alle aziende non vengono addebitati costi per singole chiamate telefoniche effettuate o per messaggi di testo inviati agli utenti tramite Azure Multi-Factor Authentication. I gestori telefonici potrebbero addebitare ai proprietari di telefoni cellulari costi correlati al roaming o di altro tipo per la ricezione di chiamate o SMS.

**D: Come viene eseguita la fatturazione a un'organizzazione per Azure Multi-Factor Authentication?**

Azure Multi-Factor Authentication è disponibile come servizio autonomo, con opzioni di fatturazione per utente e per autenticazione o in bundle con Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite. Il servizio autonomo è disponibile come opzione basata sull'utilizzo fatturata mensilmente a fronte dell'impegno monetario stabilito con Azure oppure come licenza annuale per utente disponibile tramite un contratto Microsoft Enterprise Agreement, il programma Microsoft Open License, il programma Microsoft Cloud Solution Provider o Direct.

>[AZURE.IMPORTANT]
Le aree australiane sono disponibili per i clienti con una presenza commerciale in Australia o Nuova Zelanda.

Modello di fatturazione | Prezzo
------------- | ------------- |
Utilizzo per utente (impegno monetario con Azure)| $ 1,40 al mese (autenticazioni illimitate)
Utilizzo per autenticazione (impegno monetario con Azure)|$ 1,40 per 10 autenticazioni
Licenza annuale per utente (Direct)|$ 1,40 al mese (autenticazioni illimitate)
Licenza annuale per utente (contratto multilicenza)|Contattare il [rappresentante del contratto Enterprise](https://www.microsoft.com/it-IT/licensing/licensing-programs/enterprise.aspx).

**D: Il modello di fatturazione per utente comporta l'addebito dei costi in base al numero di utenti configurati per l'uso di Multi-Factor Authentication o in base al numero di utenti che eseguono le verifiche?**

La fatturazione si basa sul numero di utenti configurati per l'uso di Multi-Factor Authentication.

**D: Come funziona la fatturazione per Multi-Factor Authentication?**

Quando un'organizzazione adotta il modello di utilizzo e fatturazione del consumo per autenticazione o per utente, l'amministratore dell'organizzazione sceglie il tipo di utilizzo al momento della creazione di un provider Multi-Factor Authentication nel portale di Azure classico. Si tratta di una risorsa che viene fatturata alla sottoscrizione di Azure dell'organizzazione, proprio come per le macchine virtuali, i siti Web e così via. Quando si usa il modello di licenza, le licenze di Azure Multi-Factor Authentication vengono acquistate e quindi assegnate agli utenti, proprio come accade per Office 365 e altri prodotti con sottoscrizione.

**D: È disponibile una versione gratuita di Azure Multi-Factor Authentication per gli amministratori?**

Un sottoinsieme di funzionalità di Azure Multi-Factor Authentication noto come Multi-Factor Authentication per gli amministratori di Azure è disponibile gratuitamente per i membri del gruppo di amministratori globali di Azure, se non è stato collegato un provider di Azure Multi-Factor Authentication basato sul consumo all'istanza di Azure Active Directory corrispondente. L'uso del provider di Multi-Factor Authentication consente di aggiornare tutti gli amministratori e gli utenti nella directory configurati per l'uso di Multi-Factor Authentication alla versione completa di Azure Multi-Factor Authentication.

**D: È disponibile una versione gratuita di Azure Multi-Factor Authentication per gli utenti di Office 365?**

Un sottoinsieme di funzionalità di Azure Multi-Factor Authentication noto come Multi-Factor Authentication per Office 365 è disponibile gratuitamente per gli utenti ai quali è stata assegnata una licenza di Office 365, se non è stato collegato un provider di Azure Multi-Factor Authentication basato sul consumo all'istanza di Azure Active Directory corrispondente. L'uso del provider di Multi-Factor Authentication consente di aggiornare tutti gli amministratori e gli utenti nella directory configurati per l'uso di Multi-Factor Authentication alla versione completa di Azure Multi-Factor Authentication.

**D: L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo per utente a quelli per autenticazione in qualsiasi momento?**

Quando crea una risorsa, l'organizzazione sceglie un modello di fatturazione. Non è possibile modificare il modello di fatturazione dopo il provisioning della risorsa. È tuttavia possibile creare una nuova risorsa di Multi-Factor Authentication per sostituire l'originale. Notare che le impostazioni degli utenti e le opzioni di configurazione non possono essere trasferite alla nuova risorsa.

**D: L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo ai modelli di licenza in qualsiasi momento?**

È possibile acquistare licenze di Azure Multi-Factor Authentication, Azure Active Directory Premium, Enterprise Mobility Suite ed Enterprise Cloud Suite in qualsiasi momento. Quando le licenze vengono aggiunte a una directory in cui è già presente un provider di Azure Multi-Factor Authentication per utente, la fatturazione in base all'utilizzo viene decrementata del numero di licenze di proprietà. Se tutti gli utenti configurati per l'uso di Multi-Factor Authentication dispongono di licenze assegnate, l'amministratore può eliminare il provider di Azure Multi-Factor Authentication. Non è possibile combinare la fatturazione in base all'utilizzo per autenticazione con un modello di licenza. Quando un provider di Multi-Factor Authentication per autenticazione è collegato a una directory, verranno fatturate all'organizzazione tutte le richieste di verifica di Multi-Factor Authentication, indipendentemente dalle eventuali licenze di proprietà.

**D: È necessario che l'organizzazione usi e sincronizzi le identità per usare Azure Multi-Factor Authentication?**

Quando si usano i modelli di fatturazione in base all'utilizzo, non è obbligatorio usare Azure Active Directory. Il collegamento di un provider di Multi-Factor Authentication a una directory è facoltativo. Se l'organizzazione non è collegata a una directory, è possibile distribuire il server Azure Multi-Factor Authentication o l'SDK di Azure Multi-Factor Authentication in locale. Quando si usa il modello di licenza, è necessario usare Azure Active Directory in quanto le licenze vengono aggiunte alla directory quando vengono acquistate e assegnate agli utenti nella directory.


## Usabilità

**D: Cosa fare nel caso in cui un utente non riceve risposta sul telefono, o se il telefono non è disponibile all'utente?**

Se l'utente ha già configurato un telefono di backup, riprovare selezionando tale telefono quando richiesto nella pagina di accesso. Se non è stato configurato alcun altro metodo, è opportuno che l'utente contatti l'amministratore e chieda di aggiornare il numero assegnato al telefono principale, cellulare o dell'ufficio.


**D: Cosa fare se un amministratore se viene contattato da un utente per un account al quale l'utente non riesce più ad accedere?**

È possibile reimpostare l'account utente chiedendo all'utente stesso di ripetere il processo di registrazione. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**: Cosa fare se il telefono di un utente che usa password di app viene smarrito o rubato?**

È possibile eliminare tutte le password dell'app degli utenti per impedire eventuali accessi non autorizzati. Dopo aver sostituito il dispositivo, l'utente può ricrearle. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**D: Cosa accade se l'utente non può accedere alle applicazioni non basate su browser?**

- A un utente configurato per l'uso di Multi-Factor Authentication è necessaria una password di app per accedere ad alcune app non basate su browser.
- L'utente deve eliminare le informazioni di accesso, riavviare l'app e accedere con il proprio nome utente e la password di app.

Altre informazioni sulla creazione di password di app e altro nella [Guida alle password per le app](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Autenticazione moderna per i client di Office 2013
>
> I client di Office 2013, tra cui Outlook, supportano nuovi protocolli di autenticazione. È possibile configurare Office 2013 affinché supporti l'autenticazione a più fattori. Dopo aver configurato Office 2013, le password di app non sono necessarie per i client di Office 2013. Per altre informazioni, vedere l'[annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**D: Cosa fare se un utente non riceve un SMS o se risponde a un SMS bidirezionale, ma la verifica scade?**

Il servizio di Azure Multi-Factor Authentication invia messaggi tramite aggregatori di SMS. Molti fattori possono influire sull'affidabilità del recapito e della ricezione di un messaggio di testo, tra cui l'aggregatore usato, il paese di destinazione, l'operatore telefonico dell'utente e la potenza del segnale. Di conseguenza, il recapito dei messaggi di testo e la ricezione delle risposte agli SMS non sono garantiti con l'esecuzione di SMS bidirezionali. Laddove possibile, è consigliabile usare SMS unidirezionali anziché SMS bidirezionali, poiché sono più affidabili ed evitano all'utente costi globali per l'SMS, causati dalla risposta a un messaggio di testo inviato da un altro paese.

Le verifiche del messaggio di testo sono inoltre più affidabili in alcuni paesi come gli Stati Uniti e il Canada. Gli utenti che hanno difficoltà nella ricezione dei messaggi di testo in modo affidabile quando usano Azure Multi-Factor Authentication, sono invitati a selezionare in alternativa i metodi dell'app per dispositivi mobili o della telefonata. L'app per dispositivi mobili è ideale poiché le notifiche dell'app per dispositivi mobili possono essere ricevute sia con la connessione del cellulare sia con quella Wi-Fi, e il passcode dell'app per dispositivi mobili viene visualizzato anche quando il dispositivo non dispone di alcun segnale. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

**D: È possibile usare i token hardware con il server Azure multi-Factor Authentication?**

Se si usa il server Azure Multi-Factor Authentication, è possibile importare token OATH TOPT di terze parti e quindi usarle per l'autenticazione a più fattori. È supportata l'importazione di token OATH TOTP di terze parti in un formato PSKC (Portable Symmetric Key Container) precedente che Gemalto può produrre per i token ed è supportata anche l'importazione di token in formato CSV. Se si importano i token in formato CSV, il file CSV deve contenere un numero di serie, la chiave privata nel formato Base32 e un intervallo di tempo (in genere 30 secondi).

È possibile usare token ActiveIdentity che sono token OATH TOTP qualora sia possibile inserire il file di chiave privato in un file CSV che è possibile importare nel server Azure Multi-Factor Authentication. È possibile usare i token OATH con ADFS (Active Directory Federation Services); con RADIUS (Remote Authentication Dial-In User Service), quando il sistema client riesce a elaborare le risposte di richiesta di accesso e con l'autenticazione basata su form di IIS (Internet Information Server).

**D: È possibile usare il server di Azure Multi-Factor Authentication per proteggere Servizi Terminal?**

Sì, ma se si usa Windows Server 2012 R2 o versione successiva, è possibile farlo solo tramite Gateway Desktop remoto.

Le modifiche della sicurezza in Windows Server 2012 R2 hanno variato le modalità di connessione del server di Azure Multi-Factor Authentication al pacchetto di sicurezza dell'autorità di protezione locale in Windows Server 2012 e versioni precedenti. Per le versioni di Servizi terminal in Windows Server 2012 o versioni precedenti è possibile semplicemente seguire la procedura [per proteggere un'applicazione con l'autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se si usa Windows Server 2012 R2, è necessario un Gateway Desktop remoto.

**D: Perché un utente potrebbe ricevere una chiamata Multi-Factor Authentication da un chiamante anonimo dopo aver impostato l'ID chiamante?**

Quando vengono effettuate chiamate MFA tramite la rete telefonica pubblica, queste vengono a volte indirizzate su un gestore che non supporta l'ID chiamante. Per questo motivo, l'ID chiamante non è garantito, anche se il sistema di autenticazione a più fattori lo invia sempre.


## Errori

**D: Cosa fare quando un utente visualizza l'errore "La richiesta di autenticazione non è per un account attivato" quando esegue l'autenticazone tramite le notifiche di app per dispositivi mobili?**

Seguire questa procedura:

1. Passare al [profilo nel portale di Azure](https://account.activedirectory.windowsazure.com/profile/) e accedere con l'account dell'organizzazione.
2. Se necessario, fare clic su **Altre opzioni di verifica** e selezionare un'opzione diversa da usare per la verifica dell'account.
3. Fare clic su **Verifica aggiuntiva di sicurezza**.
4. Rimuovere l'account esistente dall'app per dispositivi mobili.
5. Fare clic su **Configura** e seguire le istruzioni per riconfigurare l'app per dispositivi mobili.




**D: Cosa fare se l'utente visualizza un messaggio di errore 0x800434D4L quando tenta di accedere usando un'app non basata su browser?**

Attualmente, un utente può usare la verifica aggiuntiva di sicurezza solo con applicazioni e servizi ai quali può accedere tramite il proprio browser. Applicazioni non basate su browser, denominate anche *applicazioni rich client*, che vengono installate sul computer locale come Windows PowerShell non funzioneranno con gli account che richiedono la verifica aggiuntiva di sicurezza. In questo caso, l'utente potrebbe visualizzare l'errore 0x800434D4L generato dall'applicazione.

Una soluzione alternativa consiste nel disporre di account utente separati per le operazioni correlate all'amministratore e per quelle non correlate all'amministratore. In un secondo momento è possibile collegare le cassette postali tra l'account amministratore e l'account non amministratore in modo da poter accedere a Outlook usando l'account non amministratore. Per altre informazioni, vedere [Consentire a un amministratore di aprire e visualizzare il contenuto della cassetta postale di un utente](http://help.outlook.com/141/gg709759.aspx?sl=1).

<!---HONumber=AcomDC_0824_2016-->