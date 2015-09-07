<properties 
	pageTitle="Domande frequenti su Azure Multi-Factor Authentication"
	description="Azure Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un livello di sicurezza aggiuntivo agli accessi e alle transazioni degli utenti."
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
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Domande frequenti su Azure Multi-Factor Authentication


Queste domande frequenti rispondono alle domande su Azure Multi-Factor Authentication. Queste domande frequenti riguardano l'uso del servizio, ad esempio il modello di fatturazione e l'usabilità.

## Generale

**D: Come è possibile ottenere assistenza per Azure Multi-Factor Authentication?**

[Cercare nella Microsoft Knowledge Base (KB)](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Ricercare nella Microsoft Knowledge Base (KB) delle soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per il server Microsoft Azure Multi-Factor Authentication (Phone Factor).

[Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- È possibile cercare e sfogliare le domande e risposte tecniche dalla community o porre le proprie domande facendo clic [qui](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Reimpostazione delle password](mailto:phonefactorsupport@microsoft.com)

- I clienti legacy di Phonefactor che vogliono porre domande sulla reimpostazione della password o richiedere assistenza su come ottenere la reimpostazione della password, dovranno usare il collegamento seguente per aprire un caso di supporto tecnico.

[Supporto tecnico per il server Microsoft Azure multi-Factor Authentication (Phone Factor)](https://support.microsoft.com/oas/default.aspx?prid=14947)

- Utilizzare questo collegamento per contattare un supporto Professional di Microsoft. Verranno poste alcune domande per determinare quali sono le opzioni di supporto. Le opzioni di supporto possono includere posta elettronica, invio online o supporto telefonico. 

[Richieste di informazioni generali sul server di Microsoft Azure Multi-Factor Authentication (Phone Factor)](http://azure.microsoft.com/services/multi-factor-authentication)

- Per saperne di più sul server Microsoft Azure Multi-Factor Authentication (Phone Factor) o se ci sono domande su come acquistare il prodotto e le diverse opzioni di supporto disponibili, accedere oppure inviare un’email a [pfsales@microsoft.com](mailto:pfsales@microsoft.com). 



**D: In che modo il server Azure Multi-Factor Authentication gestisce i dati utente?**

Quando si usa il server Multi-Factor Authentication (MFA) locale, i dati di un utente vengono archiviati nel server locale. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue un'autenticazione a due fattori, il server MFA invia dati al servizio cloud Azure MFA per eseguire l'autenticazione. Quando queste richieste di autenticazione vengono inviate al servizio cloud, i campi seguenti vengono inviati nella richiesta e dei log, in modo che siano disponibili nei report di autenticazione/utilizzo del cliente. Alcuni campi sono facoltativi e possono essere abilitati o disabilitati nel server Multi-Factor Authentication. La comunicazione dal server MFA al servizio cloud MFA usa SSL/TLS sulla porta 443 in uscita. Questi campi sono:

- ID univoco: nome utente o ID interno del MFA
- Nome e cognome: facoltativo
- Indirizzo di posta elettronica: facoltativo
- Numero di telefono: quando si esegue una chiamata vocale o l'autenticazione tramite SMS
- Token del dispositivo: quando si esegue l'autenticazione con l'app per dispositivi mobili
- Modalità di autenticazione 
- Risultato dell'autenticazione 
- Nome del server MFA 
- IP del server MFA 
- IP client: se disponibile



Oltre a questi campi, il risultato dell'autenticazione (esito positivo/rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme ai dati di autenticazione e sono disponibili nei report di autenticazione/utilizzo.




## Fatturazione

**D: Le chiamate telefoniche o i messaggi SMS usati per autenticare gli utenti verranno addebitati all'organizzazione?**

Tutti i costi sono distribuiti nel costo del servizio per utente o per autenticazione. Alle aziende non vengono addebitati costi per singole chiamate telefoniche effettuate o per messaggi di testo inviati agli utenti finali durante l’utilizzo di Azure Multi-Factor Authentication. Ci potrebbero essere costi aggiuntivi per i proprietari del telefono relativi al roaming o ad altri costi da parte delle compagnie telefoniche per ricevere chiamate telefoniche o messaggi di testo.

**"D: Come viene eseguita la fatturazione all'organizzazione per Azure Multi-Factor Authentication?**

Si sceglie il modello di utilizzo/fatturazione 'per utente' o 'per autenticazione’ durante la creazione di un Provider Multi-Factor Authentication nel portale di gestione di Azure. È una risorsa basata sul consumo che viene fatturata per la sottoscrizione ad Azure dell'azienda, proprio come sono fatturati per la sottoscrizione le macchine virtuali, i siti Web, e così via.

**: Il modello di fatturazione "per utente" comporta l'addebito dei costi in base al numero di utenti abilitati per Multi-Factor Authentication o in base al numero di utenti che eseguono le verifiche?**

La fatturazione si basa sul numero di utenti abilitati per la Multi-Factor Authentication.

## Usabilità

**D: Cosa fare se non si riceve una risposta al telefono o si è dimenticato il telefono?**

Se è già stato configurato un telefono di backup, riprovare selezionando tale telefono quando richiesto nella pagina di accesso. Se non è stato configurato alcun altro metodo, contattare l'amministratore e chiedere di aggiornare il numero assegnato al telefono principale, cellulare o dell'ufficio.

**D: Un utente è stato rimosso da un ruolo di amministratore, ma Multi-Factor Authentication non è stato disabilitato e ora l'utente non è visualizzato nell'elenco. Come fare per rimuovere la funzionalità?**

- A seconda del portale in uso, nel riquadro sinistro, fare clic su Utenti o su Utenti e Gruppi.
- Selezionare la casella di controllo accanto all'utente che si desidera modificare, poi fare clic su Modifica o sull'icona di Modifica.
- Fare clic su Impostazioni, in Assegna ruolo, selezionare Sì e aggiungere l'utente al ruolo di amministratore precedente.
- Passare alla pagina di autenticazione a più fattori. L'account dovrebbe essere ora visibile nell'elenco nella pagina. 
- Seguire i passaggi precedenti per disabilitare l'autenticazione a più fattori per un account. 
- A questo punto, è ora possibile rimuovere l'account dal ruolo di amministratore.


**D: Cosa fare se un utente contatta l'amministratore con un account che è stato bloccato?**

È possibile reimpostare l'utente chiedendo di ripetere il processo di registrazione. A questo scopo, vedere [Gestione delle impostazioni utente con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md)

**D: Cosa fare in caso di perdita o furto di un dispositivo che usa le password di app?**

È possibile eliminare tutte le password dell’app degli utenti per impedire eventuali accessi non autorizzati. Dopo aver sostituito il dispositivo, l'utente può ricrearle. A questo scopo, vedere [Gestione delle impostazioni utente con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md)

**D: Cosa fare se l'utente non riesce ad accedere ad app non basate su browser?**

- Gli utenti abilitati per l'autenticazione a più fattori avranno bisogno della password della app per accedere ad alcune app non basate su browser.
- Gli utenti dovranno cancellare le informazioni di accesso (Elimina informazioni di accesso), riavviare l'applicazione e accedere con il proprio nome utente e la password della app. 

Per informazioni sulla creazione delle password dell'app, vedere [Guida alle password per le app](multi-factor-authentication-end-user-app-passwords.md)


>[AZURE.NOTE]Autenticazione Moderna per i Client di Office 2013
>
> I Client di Office 2013 (tra cui Outlook) ora supportano i nuovi Protocolli di autenticazione e possono essere abilitati per supportare la Multi-Factor Authentication. Ciò significa che una volta attivati, le password della app non vengono richieste per l'utilizzo con i Client di Office 2013. Per altre informazioni, vedere l'[annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

**D: Cosa fare se non si riceve un SMS o se si risponde a un SMS bidirezionale, ma la verifica scade?**

Il servizio di Azure Multi-Factor Authentication invia messaggi tramite aggregatori di SMS. Molti fattori possono influire sull'affidabilità del recapito e della ricezione di un messaggio di testo, tra cui l'aggregatore utilizzato, il paese di destinazione, l’operatore telefonico e la potenza del segnale. Pertanto, il recapito dei messaggi di testo e la ricezione delle risposte agli SMS non sono garantiti con l'esecuzione di SMS bidirezionali. Utilizzare SMS unidirezionali è consigliabile rispetto all’uso di SMS bidirezionali, laddove possibile, poiché sono più affidabili ed evitano all’utente costi globali per l’SMS, causati dalla risposta a un messaggio di testo inviato da un altro paese.

Le Verifiche del messaggio di testo sono inoltre più affidabili in alcuni paesi come gli Stati Uniti e il Canada. Gli utenti che hanno difficoltà nella ricezione dei messaggi di testo in modo affidabile quando utilizzano Azure Multi-Factor Authentication, sono invitati a selezionare in alternativa i metodi dell’app per dispositivi mobili o della telefonata. L’app per dispositivi mobili è ideale poiché le notifiche dell’app per dispositivi mobili possono essere ricevute sia con la connessione del cellulare sia con quella Wi-Fi, e il passcode dell’app per dispositivi mobili viene visualizzato anche quando il dispositivo non dispone di alcun segnale. L'app Azure Authenticator è disponibile per [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).


## Errori

**D: Cosa fare quando viene visualizzato un errore di richiesta di autenticazione non corretta per un account attivato quando si esegue l'autenticazione mediante le notifiche di applicazioni per dispositivi mobili?**

- Visitare la pagina all'indirizzo [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) e accedere con l'account aziendale.
- Se necessario, fare clic su Altre opzioni di verifica e selezionare un'opzione diversa per completare la verifica dell'account.
- Cliccare su Verifica aggiuntiva di sicurezza
- Rimuovere l'account esistente dall'applicazione per dispositivi mobili.
- Fare clic su Configura e seguire le istruzioni per riconfigurare l'applicazione per dispositivi mobili.




**D: Cosa fare quando viene visualizzato un errore 0x800434D4L mentre si tenta di effettuare l'accesso tramite un'applicazione non basata su browser?**

Attualmente, la verifica aggiuntiva di sicurezza può essere utilizzata solo con applicazioni/servizi a cui è possibile accedere tramite il browser. Applicazioni non basate su browser (denominate anche applicazioni rich client) che vengono installate sul computer locale come Windows Powershell non funzioneranno con gli account che sono richiesti per la verifica aggiuntiva di sicurezza. In questo caso, si vedrà applicazione generare l'errore 0x800434D4L.

Una soluzione alternativa consiste nel disporre di un account utente separato per le operazioni correlate all'amministratore e un altro per le operazioni non correlate all’amministratore. In un secondo momento, è possibile collegare le cassette postali tra l'account amministratore e l'account non amministratore in modo da poter accedere ad outlook utilizzando l'account non amministratore. Per altre informazioni, vedere [Consentire ad un amministratore di aprire e visualizzare il contenuto della cassetta postale di un utente] (http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1).

<!---HONumber=August15_HO9-->