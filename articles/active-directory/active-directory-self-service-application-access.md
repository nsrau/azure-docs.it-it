<properties
	pageTitle="Accesso alle applicazioni self-service e gestione delegata con Azure Active Directory | Microsoft Azure"
	description="Questo articolo descrive come abilitare l'accesso alle applicazioni self-service e la gestione delegata con Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="asmalser"/>

#Accesso alle applicazioni self-service e gestione delegata con Azure Active Directory

L'abilitazione della funzionalità self-service per gli utenti finali è uno scenario comune nell'IT aziendale. Con un numero elevato di utenti e di applicazioni, spesso la persona più informata per decidere a chi concedere l'accesso non è l'amministratore della directory, ma il responsabile del team o un altro amministratore con delega. Tutto sommato, la persona più informata su ciò che serve per svolgere il proprio lavoro è proprio l'utente dell'applicazione.

L'accesso alle applicazioni self-service è una funzionalità di [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) che permette agli amministratori di directory di:

* Consentire agli utenti di richiedere l'accesso alle applicazioni usando un riquadro "Altre applicazioni" nel [pannello di accesso di Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).
* Impostare le applicazioni a cui gli utenti possono richiedere l'accesso.
* Indicare se è necessaria o meno un'approvazione perché gli utenti possano autoassegnarsi l'accesso a un'applicazione.
* Impostare il responsabile dell'approvazione delle richieste e della gestione degli accessi per ogni applicazione.

Oggi questa funzionalità è supportata per tutte le app preintegrate e personalizzate che supportano il Single Sign-On federato o basato su password nella [raccolta di applicazioni di Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/), incluse applicazioni come Salesforce, Dropbox, Google Apps e altro ancora. Questo articolo illustra come:

* Configurare l'accesso alle applicazioni self-service per gli utenti finali e configurare un flusso di lavoro di approvazione facoltativo. 
* Delegare la gestione dell'accesso alle persone più appropriate all'interno dell'organizzazione per applicazioni specifiche e permettere loro di usare il pannello di accesso di Azure AD per approvare le richieste di accesso, assegnare direttamente l'accesso ad alcuni utenti o (facoltativamente) impostare credenziali per l'accesso alle applicazioni quando è configurato il Single Sign-On basato su password.


##Configurazione dell'accesso alle applicazioni self-service

Per abilitare l'accesso alle applicazioni self-service e specificare quali applicazioni possono essere aggiunte o richieste dagli utenti finali, seguire questa procedura.

**1:** Accedere al [portale di Azure classico](https://manage.windowsazure.com/).

**2:** Nella sezione **Active Directory** selezionare la directory e quindi selezionare la scheda **Applicazioni**.

**3:** Fare clic sul pulsante **Aggiungi** e usare l'opzione relativa alla raccolta per selezionare e aggiungere un'applicazione.

**4:** Dopo aver aggiunto l'app, verrà visualizzata la pagina di avvio rapido dell'app. Fare clic su **Configura accesso Single Sign-On**, selezionare la modalità Single Sign-On preferita e quindi salvare la configurazione.

**5:** Selezionare quindi la scheda **Configura**. Per consentire agli utenti di richiedere l'accesso a questa app dal pannello di accesso di Azure AD, impostare **Consenti l'accesso alle applicazioni self-service** su **Sì**.

![][1]

**6:** Per configurare un flusso di lavoro facoltativo per l'approvazione delle richieste di accesso, impostare **Richiedi approvazione prima di concedere l'accesso** su **Sì**. A questo punto è possibile selezionare uno o più responsabili dell'approvazione usando il pulsante **Responsabili approvazione**.

Il responsabile dell'approvazione può essere qualsiasi utente dell'organizzazione con un account Azure AD e può essere responsabile del provisioning dell'account, della gestione delle licenze o di altri processi aziendali richiesti dall'organizzazione prima di concedere l'accesso a un'app. Il responsabile dell'approvazione può anche essere il proprietario del gruppo per uno o più gruppi di account condivisi e può assegnare gli utenti a uno di questi gruppi per consentire l'accesso attraverso un account condiviso.

Se non è richiesta alcuna approvazione, l'applicazione viene aggiunta immediatamente al pannello di accesso di Azure AD degli utenti. Questa soluzione è appropriata se l'applicazione è stata impostata per il [provisioning automatico degli utenti](active-directory-saas-app-provisioning.md) o è stata impostata la [modalità SSO con password "gestita dall'utente"](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) in cui l'utente ha già un account utente e ne conosce la password.

**7:** Se l'applicazione è stata configurata per l'uso del Single Sign-On basato su password, è anche disponibile un'opzione per consentire al responsabile dell'approvazione di impostare le credenziali SSO per conto di ogni utente. Per altre informazioni sulla gestione delegata degli accessi, vedere la sezione seguente.

**8:** Infine, nel **Gruppo per gli utenti autoassegnati** viene visualizzato il nome del gruppo che verrà usato per archiviare gli utenti a cui è stato concesso o assegnato l'accesso all'applicazione. Il responsabile dell'approvazione degli accessi diventa proprietario di questo gruppo. Se il nome del gruppo visualizzato non esiste, verrà creato automaticamente. Facoltativamente è possibile impostare come nome del gruppo il nome di un gruppo esistente.

**9:** Fare clic su **Salva** nella parte inferiore dello schermo per salvare la configurazione. Ora gli utenti potranno richiedere l'accesso a questa app dal pannello di accesso.

**10:** Per provare l'esperienza dell'utente finale, accedere al pannello di accesso di Azure AD dell'organizzazione all'indirizzo https://myapps.microsoft.com, preferibilmente usando un account che non sia di un responsabile dell'approvazione per l'app.

**11:** Nella scheda **Applicazioni** fare clic sul riquadro **Altre applicazioni**. Verrà visualizzata una raccolta di tutte le applicazioni abilitate per l'accesso self-service nella directory, con la possibilità di cercare e filtrare per categoria di app a sinistra.

**12:** Facendo clic su un'app viene avviato il processo di richiesta. Se non è richiesto alcun processo di approvazione, l'applicazione viene aggiunta immediatamente alla scheda **Applicazioni** dopo un breve messaggio di conferma. Se è necessaria l'approvazione, viene visualizzata una finestra di dialogo per indicare questa condizione e viene inviato un messaggio di posta elettronica ai responsabili dell'approvazione. Nota: per visualizzare il processo di richiesta è necessario eseguire l'accesso al pannello di accesso come utente non responsabile dell'approvazione.

**13:** Il messaggio di posta elettronica indica al responsabile dell'approvazione di accedere al pannello di accesso di Azure AD e approvare la richiesta. Dopo l'approvazione della richiesta e al termine di altri eventuali processi speciali da parte del responsabile dell'approvazione, l'utente potrà visualizzare e accedere all'applicazione dalla scheda **Applicazioni**.

##Gestione delegata degli accessi alle applicazioni

Un responsabile dell'approvazione degli accessi all'applicazione può essere qualsiasi utente dell'organizzazione che sia ritenuto più adatto ad approvare o negare l'accesso all'applicazione in questione. Questo utente può essere responsabile del provisioning dell'account, della gestione delle licenze o di altri processi aziendali richiesti dall'organizzazione prima di concedere l'accesso a un'app.
 
Quando si configura l'accesso alle applicazioni self-service descritto in precedenza, i responsabili dell'approvazione per le applicazioni assegnate visualizzano un riquadro **Gestire le applicazioni** aggiuntivo nel pannello di accesso di Azure AD, che mostra le applicazioni di cui sono amministratori degli accessi. Facendo clic su un'app viene visualizzata una schermata con diverse opzioni.

![][2]

###Approva richieste

Il riquadro **Approva richieste** consente ai responsabili dell'approvazione di visualizzare eventuali approvazioni in sospeso specifiche dell'app e reindirizza alla scheda Approvazioni, in cui è possibile confermare o negare le richieste. Si noti che il responsabile dell'approvazione riceve anche messaggi di posta elettronica automatici ogni volta che viene creata una richiesta, con indicazioni sulle operazioni da eseguire.

###Aggiungi utenti

Il riquadro **Aggiungi utenti** consente ai responsabili dell'approvazione di concedere direttamente l'accesso all'applicazione agli utenti selezionati. Facendo clic su questo riquadro viene aperta una finestra di dialogo che permette al responsabile dell'approvazione di visualizzare e cercare utenti nella propria directory. Quando si aggiunge un utente, l'applicazione viene visualizzata in Office 365 o nel pannello di accesso di Azure AD dell'utente. Se è necessario un processo di provisioning manuale dell'utente all'app perché l'utente possa accedere, il responsabile dell'approvazione deve eseguire questo processo prima di assegnare l'accesso.

###Gestire gli utenti

Il riquadro **Gestire gli utenti** consente ai responsabili dell'approvazione di aggiornare o rimuovere direttamente gli utenti che hanno accesso all'applicazione.

###Configurare le credenziali SSO con password (se applicabile)

Il riquadro **Configura** viene visualizzato solo se l'applicazione è stata configurata dall'amministratore IT per l'uso del Single Sign-On basato su password e l'amministratore ha concesso al responsabile dell'approvazione la possibilità di impostare le credenziali SSO con password come descritto in precedenza. Se selezionato, il responsabile dell'approvazione ha a disposizione diverse opzioni per propagare le credenziali agli utenti assegnati:

![][3]

* **Gli utenti accedono con le proprie password**: in questa modalità, gli utenti assegnati conoscono i relativi nomi utente e password per l'applicazione e devono immetterli al primo accesso all'applicazione. Questo corrisponde al caso di SSO con password in cui [le credenziali sono gestite dagli utenti](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Gli utenti accedono automaticamente usando account separati da me gestiti**: in questa modalità, gli utenti assegnati non devono immettere o conoscere le proprie credenziali specifiche dell'app quando eseguono l'accesso all'applicazione. È il responsabile dell'approvazione che imposta le credenziali per ogni utente dopo aver assegnato l'accesso usando il riquadro **Aggiungi utente**. Quando l'utente fa clic sull'applicazione nel pannello di accesso o in Office 365, viene eseguito automaticamente l'accesso con le credenziali impostate dal responsabile dell'approvazione. Questo corrisponde al caso di SSO con password in cui [le credenziali sono gestite dagli amministratori](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Gli utenti accedono automaticamente usando un singolo account da me gestito**: questo è un caso speciale ed è possibile adottare questa modalità quando è necessario concedere l'accesso a tutti gli utenti assegnati usando un singolo account condiviso. Il caso d'uso più comune per questa modalità è rappresentato dalle applicazioni per social media, in cui un'organizzazione ha un singolo account della società che deve essere aggiornato da più utenti. Anche questo corrisponde al caso di SSO con password in cui [le credenziali sono gestite dagli amministratori](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Dopo aver selezionato questa opzione, al responsabile dell'approvazione verrà chiesto di immettere il nome utente e la password per il singolo account condiviso. Al termine, facendo clic sull'applicazione nel pannello di accesso di Azure AD o in Office 365 viene eseguito l'accesso per tutti gli utenti assegnati usando questo account.

##Risorse aggiuntive
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG

<!---HONumber=AcomDC_0211_2016-->