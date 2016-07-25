<properties
	pageTitle="Procedure ottimali: Gestione delle password di Azure AD | Microsoft Azure"
	description="Procedure ottimali per la distribuzione e l'utilizzo, documentazione di esempio per l'utente finale e guide di formazione per la gestione delle password in Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="asteen"/>

# Distribuzione di Gestione delle password e formazione degli utenti per l’uso

> [AZURE.IMPORTANT] **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).

Dopo aver abilitato la reimpostazione delle password, il passaggio successivo consiste nel fare in modo che gli utenti usino il servizio all'interno dell'organizzazione. A tale scopo, è necessario assicurarsi che gli utenti siano configurati per usare il servizio correttamente e, inoltre, che dispongano della formazione necessaria per la gestione corretta delle proprie password. In questo articolo vengono descritti i concetti seguenti:

* [**Come rendere gli utenti configurati per Gestione delle password**](#how-to-get-users-configured-for-password-reset)
  * [Cosa rende un account configurato per la reimpostazione delle password](#what-makes-an-account-configured)
  * [Metodi per inserire i dati di autenticazione autonomamente](#ways-to-populate-authentication-data)
* [**Metodi ottimali per implementare la reimpostazione delle password nell'organizzazione **](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Implementazione basata sulla posta elettronica ed esempi di comunicazioni tramite posta elettronica](#email-based-rollout)
  * [Creare un portale personalizzato per la gestione delle password per gli utenti](#creating-your-own-password-portal)
  * [Come usare la registrazione applicata per forzare gli utenti a effettuare la registrazione al momento dell’accesso](#using-enforced-registration)
  * [Come caricare i dati di autenticazione per gli account utente](#uploading-data-yourself)
* [**Materiale di formazione di esempio per utenti e supporto (presto disponibile)**](#sample-training-materials)

## Come rendere gli utenti configurati per la reimpostazione delle password
In questa sezione vengono descritti vari metodi con cui è possibile garantire che ciascun utente dell'organizzazione possa usare la reimpostazione delle password self-service in modo efficace nel caso in cui dimentichi la password.

### Cosa rende un account configurato
Perché un utente possa usare la reimpostazione delle password, devono essere soddisfatte **tutte** le condizioni seguenti:

1.	La reimpostazione delle password deve essere abilitata nella directory. Per informazioni su come abilitare la reimpostazione delle password, consultare [Abilitare gli utenti alla reimpostazione delle password di Azure AD](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) o [Abilitare gli utenti alla reimpostazione o alla modifica delle password di AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.	L'utente deve essere dotato di licenza.
 - Agli utenti del cloud deve essere stata assegnata una **qualsiasi licenza Office 365 a pagamento**, una licenza **AAD Basic** o una **licenza AAD Premium**.
 - Agli utenti locali (federati o con sincronizzazione hash) **deve essere stata assegnata una licenza AAD Premium**.
3.	L'utente deve disporre del **set minimo di dati di autenticazione definiti**, in conformità con i criteri di reimpostazione delle password correnti.
 - I dati di autenticazione vengono considerati definiti se il campo corrispondente nella directory contiene i dati in formato corretto.
 - Un set minimo di dati di autenticazione è definito con **almeno una** delle opzioni di autenticazione abilitate se è configurato un criterio a un gate o **almeno due** delle opzioni di autenticazione abilitate se è configurato un criterio a due gate.
4.	Se l'utente usa un account locale, [Writeback delle password](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) deve essere abilitato e attivato

### Metodi per inserire i dati di autenticazione
Esistono diverse opzioni su come specificare i dati che gli utenti dell'organizzazione useranno per la reimpostazione delle password.

- Modificare gli utenti nel [portale di gestione di Azure](https://manage.windowsazure.com) o nel [portale di amministrazione di Office 365](https://portal.microsoftonline.com)
- Usare Azure AD Sync per sincronizzare le proprietà utente in Azure AD da un dominio di Active Directory locale
- Usare Windows PowerShell per modificare le proprietà utente [seguendo la procedura di seguito](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Consentire agli utenti di registrare i propri dati indirizzandoli al portale di registrazione all'indirizzo [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Richiedere agli utenti di registrarsi per la reimpostazione quando accedono al proprio account di Azure AD impostando l’opzione di configurazione [**Richiedere agli utenti di registrarsi all'accesso?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) su **Sì**.

Affinché il sistema funzioni, non è necessario che gli utenti effettuino la registrazione per la reimpostazione delle password. Ad esempio, se nella directory locale sono presenti numeri di telefono cellulare o di ufficio esistenti, è possibile sincronizzarli in Azure AD e tali numeri verranno usati per la reimpostazione delle password automaticamente.

È inoltre possibile leggere informazioni sull’[utilizzo dei dati per la reimpostazione della password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) e su [come è possibile popolare i singoli campi di autenticazione con PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## Metodo ottimale per implementare la reimpostazione delle password per gli utenti
Di seguito sono riportati i passaggi di implementazione generali per la reimpostazione della password:

1.	Abilitare la reimpostazione delle password nella directory passando alla scheda **Configura** nel [portale di gestione di Azure](https://manage.windowsazure.com) e selezionando **Sì** per l’opzione **Utenti abilitati per la reimpostazione delle password**.
2.	Assegnare le licenze appropriate a ciascun utente a cui si vuole offrire la reimpostazione delle password passando alla scheda **Licenze** nel [portale di gestione di Azure](https://manage.windowsazure.com).
3.	Facoltativamente, limitare la reimpostazione delle password a un gruppo di utenti per implementare la funzionalità lentamente nel tempo, impostando l’opzione **Limitare l'accesso alla reimpostazione delle password** su **Sì** e selezionando un gruppo di sicurezza da abilitare per la reimpostazione delle password (questi utenti devono disporre tutti di licenze assegnate).
4.	Indicare agli utenti di usare la reimpostazione delle password invitandoli, tramite un messaggio di posta elettronica, ad effettuare la registrazione, abilitando la registrazione applicata nel pannello di accesso oppure caricando i dati di autenticazione appropriati per tali utenti tramite DirSync, PowerShell o il [portale di gestione di Azure](https://manage.windowsazure.com). Di seguito vengono fornite ulteriori informazioni su questo argomento.
5.	Nel tempo, esaminare gli utenti che hanno effettuato la registrazione passando alla scheda Report e visualizzando il report [**Attività di registrazione reimpostazione password**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity).
6.	Una volta che un buon numero di utenti ha effettuato la registrazione, esaminare gli utenti che usano la reimpostazione delle password, passando alla scheda Report e visualizzando il report [**Attività di reimpostazione password**](active-directory-passwords-get-insights.md#view-password-reset-activity).

Esistono diversi metodi per comunicare agli utenti che possono effettuare la registrazione per la reimpostazione delle password e usare la reimpostazione delle password all'interno dell'organizzazione. Tali metodi sono descritti nei dettagli di seguito.

### Implementazione basata sulla posta elettronica
Probabilmente, l'approccio più semplice per informare gli utenti sulla registrazione per la reimpostazione delle password e sull’uso della reimpostazione delle password è inviando un messaggio di posta elettronica. Di seguito è riportato un modello che è possibile usare per eseguire questa operazione. È possibile sostituire i colori e i logo con quelli desiderati per personalizzare il modello in base alle proprie esigenze.

  ![][001]

È possibile [scaricare il modello di messaggio di posta elettronica qui](http://1drv.ms/1xWFtQM).

### Creazione del portale delle password
Una strategia particolarmente adatta ai grandi clienti per la distribuzione delle funzionalità di gestione delle password è la creazione di un singolo "portale delle password" tramite il quale gli utenti possono gestire tutti gli aspetti riguardanti le password in un'unica posizione.

Molti dei maggiori clienti scelgono di creare una voce DNS radice come https://passwords.contoso.com con collegamenti al portale di reimpostazione delle password di Azure AD, al portale di registrazione per la reimpostazione delle password e alle pagine di modifica delle password. In questo modo è possibile includere in qualsiasi comunicazione cartacea o tramite posta elettronica un singolo URL facile da ricordare al quale gli utenti possono accedere quando desiderano iniziare a usare il servizio.

Per iniziare, è stata creata una pagina semplice che usa i paradigmi di progettazione dell'interfaccia utente reattiva più recenti e che funzionerà su tutti i browser e dispositivi mobili.

  ![][007]

È possibile [scaricare il modello di sito Web qui](https://github.com/kenhoff/password-reset-page). Si consiglia di personalizzare il logo e i colori in base alle esigenze dell'organizzazione.

### Uso della registrazione applicata
Se si vuole che gli utenti effettuino autonomamente la registrazione per la reimpostazione delle password, è possibile anche forzarli a effettuare la registrazione quando accedono al pannello di accesso all’indirizzo [http://myapps.microsoft.com](http://myapps.microsoft.com). È possibile abilitare questa opzione dalla scheda **Configura** della directory, abilitando l’opzione **Richiedere agli utenti di effettuare la registrazione durante l'accesso al pannello di accesso**.

È inoltre possibile definire se verrà o meno richiesto loro di effettuare nuovamente la registrazione dopo un periodo di tempo configurabile modificando l’opzione **Numero di giorni prima che gli utenti debbano confermare i dati di contatto** su un valore diverso da zero. Per altre informazioni vedere [Personalizzazione del comportamento di Gestione delle password utente](active-directory-passwords-customize.md#password-management-behavior).

  ![][002]

Dopo aver abilitato questa opzione, quando gli utenti accedono al pannello di accesso, visualizzano una finestra popup che li informa che l'amministratore ha richiesto loro di verificare le informazioni di contatto. Gli utenti possono usare questa opzione per reimpostare la propria password, se perdono sempre l'accesso al proprio account.

  ![][003]

Facendo clic su **Verifica ora** viene visualizzato il **portale di registrazione per la reimpostazione delle password** in [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) e viene richiesto di effettuare la registrazione. La registrazione tramite questo metodo può essere chiusa scegliendo il pulsante **Annulla** o chiudendo la finestra ma, se non effettuano la registrazione, gli utenti ricevono un promemoria ogni volta che eseguono l'accesso.

  ![][004]

### Caricamento autonomo dei dati
Per caricare i dati di autenticazione autonomamente, non è necessario che gli utenti effettuino la registrazione per la reimpostazione delle password per poter reimpostare le proprie password. Se gli utenti dispongono di dati di autenticazione definiti nel proprio account che soddisfano i criteri di reimpostazione delle password definiti, gli utenti saranno in grado di reimpostare le proprie password.

Per informazioni sulle proprietà che è possibile impostare tramite AAD Connect o Windows PowerShell, vedere [Dati usati nella reimpostazione delle password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

È possibile caricare i dati di autenticazione tramite il [portale di gestione di Azure](https://manage.windowsazure.com) seguendo la procedura seguente:

1.	Passare alla propria directory nell’**estensione di Active Directory** nel [portale di gestione di Azure](https://manage.windowsazure.com).
2.	Fare clic sulla scheda **Utenti**.
3.	Selezionare dall’elenco l'utente a cui si è interessati.
4.	Nella prima scheda la proprietà **Indirizzo di posta elettronica alternativo** può essere usata per abilitare la reimpostazione delle password.

    ![][005]

5.	Fare clic sulla scheda **Informazioni sul lavoro**.
6.	In questa pagina sono disponibili le proprietà **Telefono ufficio**, **Telefono cellulare**, **Telefono per l'autenticazione** e **Indirizzo di posta elettronica per l’autenticazione**. Anche queste proprietà possono essere impostate per consentire a un utente di reimpostare la propria password.

    ![][006]

Per informazioni sull’uso di queste proprietà, vedere [Dati usati per la reimpostazione delle password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Vedere [Come accedere ai dati di reimpostazione della password per gli utenti da PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) per vedere come è possibile leggere e impostare questi dati con PowerShell.

## Materiale di formazione di esempio
Microsoft sta attualmente lavorando al materiale di formazione di esempio che è possibile usare per fare in modo che l'organizzazione IT e gli utenti imparino rapidamente a distribuire e usare la reimpostazione delle password. Non rimane che attendere.


<br/> <br/> <br/>

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_0713_2016-->