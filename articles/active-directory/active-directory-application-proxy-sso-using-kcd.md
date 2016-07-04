<properties
	pageTitle="Single Sign-On con il proxy di applicazione | Microsoft Azure"
	description="Descrive come fornire accesso Single Sign-On mediante il proxy di applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="kgremban"/>


# Single Sign-On con il proxy di applicazione

Single Sign-On è un elemento chiave del proxy di applicazione di Azure AD. Questo tipo di accesso offre la migliore esperienza utente con i passaggi seguenti:
1. Un utente accede al cloud
2. Tutti i controlli di convalida della sicurezza avvengono nel cloud (autenticazione preliminare)
3. Quando la richiesta viene inviata all'applicazione locale, il connettore del proxy di applicazione rappresenta l'utente in modo che l'applicazione back-end lo identifichi come proveniente da un dispositivo aggiunto al dominio.

![Diagramma di accesso dall'utente finale alla rete aziendale mediante il proxy dell'applicazione](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Il proxy di applicazione di Azure AD consente di fornire un'esperienza di accesso Single Sign-On (SSO) per gli utenti. Usare le istruzioni seguenti per pubblicare le app mediante l'accesso SSO:


## Accesso Single Sign-On alle app locali con Autenticazione integrata di Windows tramite il proxy di applicazione
È possibile abilitare l'accesso Single Sign-On alle applicazioni che usano l'autenticazione integrata di Windows (IWA) concedendo ai connettori proxy di applicazione l'autorizzazione per rappresentare gli utenti e inviare e ricevere token per loro conto in Active Directory.


### Diagramma di rete

Questo diagramma illustra il flusso quando un utente tenta di accedere a un'applicazione locale che usa l'autenticazione integrata di Windows.

![Diagramma del flusso di autenticazione di Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. L'utente immette l'URL per accedere all'applicazione locale tramite il proxy di applicazione.
2. Il proxy di applicazione reindirizza la richiesta ai servizi di autenticazione di Azure AD per la preautenticazione. A questo punto, Azure AD applica gli eventuali criteri di autenticazione e autorizzazione appropriati, ad esempio l'autenticazione a più fattori. Se l'utente viene convalidato, Azure AD crea un token e lo invia all'utente.
3. L'utente passa il token al proxy di applicazione.
4. Il proxy di applicazione convalida il token e recupera il nome dell'entità utente (UPN) da esso e invia la richiesta, il nome dell'entità utente e il nome dell'entità servizio (SPN) al connettore tramite un canale sicuro con doppia autenticazione.
5. Il connettore esegue la negoziazione della delega vincolata Kerberos con l'istanza di Active Directory locale, rappresentando l'utente per ottenere un token Kerberos per l'applicazione.
6. Active Directory invia il token Kerberos per l'applicazione al connettore.
7. Il connettore invia la richiesta originale al server dell'applicazione, usando il token Kerberos ricevuto da Active Directory.
8. L'applicazione invia la risposta al connettore, che viene quindi restituita al servizio proxy di applicazione e infine all'utente.

### Prerequisiti

- Assicurarsi che le app, ad esempio le app Web SharePoint, siano impostate per usare l'autenticazione integrata di Windows. Per altre informazioni, vedere [Attivare il supporto per l'autenticazione Kerberos](https://technet.microsoft.com/library/dd759186.aspx) oppure per SharePoint, vedere [Pianificare l'autenticazione Kerberos in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
- Creare i nomi delle entità servizio per le applicazioni.
- Assicurarsi che il server che esegue il connettore e che il server che esegue l'app in fase di pubblicazione siano aggiunti a un dominio e che appartengano allo stesso dominio. Per altre informazioni sull'aggiunta a un dominio, vedere [Aggiungere un computer a un dominio](https://technet.microsoft.com/library/dd807102.aspx).


### Configurazione di Active Directory

La configurazione di Active Directory varia a seconda del fatto che il connettore proxy di applicazione e il server pubblicato si trovino nello stesso dominio o meno.

#### Connettore e server pubblicato nello stesso dominio

1. In Active Directory passare a **Strumenti** > **Utenti e computer**.
2. Selezionare il server che esegue il connettore.
3. Fare clic con il pulsante destro del mouse e scegliere **Proprietà** > **Delega**.
4. Selezionare **Computer attendibile per la delega solo ai servizi specificati** e in **Servizi ai quali l'account può presentare credenziali delegate** aggiungere il valore per l'identità SPN del server applicazioni.
5. In questo modo il connettore proxy di applicazione può rappresentare gli utenti in AD nei confronti delle applicazioni definite nell'elenco.

![Schermata della finestra delle proprietà per Connector-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### Connettore e server pubblicato in domini diversi

1. Per un elenco dei prerequisiti necessari per usare la delega vincolata Kerberos tra domini, vedere [Delega vincolata Kerberos tra domini](https://technet.microsoft.com/library/hh831477.aspx).
2. In Windows Server 2012 R2 usare la proprietà `principalsallowedtodelegateto` nel server del connettore per consentire al proxy di applicazione la delega per il server del connettore, dove il server pubblicato è `sharepointserviceaccount` e il server che delega è `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount` può essere l'account del computer SPS o un account del servizio tramite cui è in esecuzione il pool di app SPS.


### Configurazione del portale di Azure classico

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md). Assicurarsi di selezionare **Azure Active Directory** come **Metodo di autenticazione preliminare**.
2. Una volta visualizzata l'applicazione nell'apposito elenco, selezionarla e fare clic su **Configura**.
3. In **Proprietà** impostare **Metodo di autenticazione interna** su **Autenticazione integrata di Windows**. ![Configurazione dell'applicazione avanzata](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Immettere l’**SPN dell'applicazione interna** del server dell'applicazione. In questo esempio il nome dell'entità servizio per l'applicazione pubblicata è http/lob.contoso.com.  

>[AZURE.IMPORTANT] Per il corretto funzionamento della preautenticazione, i nomi delle entità servizio in Azure Active Directory devono essere identici ai nomi delle entità servizio in Active Directory locale. Assicurarsi che l'istanza di Azure Active Directory sia sincronizzata con l'istanza di Active Directory locale.

| | |
| --- | --- |
| Metodo di autenticazione interna | Se si usa Azure AD per l'autenticazione preliminare, è possibile impostare un metodo di autenticazione interno per permettere agli utenti di usufruire dell'accesso Single Sign-On (SSO) a questa applicazione. <br><br> Selezionare **Autenticazione integrata di Windows** se l'applicazione usa l'autenticazione integrata di Windows ed è possibile configurare la delega vincolata Kerberos per abilitare l'accesso Single Sign-On per questa applicazione. Le applicazioni con autenticazione integrata di Windows devono essere configurate usando la delega vincolata Kerberos, in caso contrario il proxy di applicazione non sarà in grado di pubblicare queste applicazioni. <br><br> Selezionare **Nessuna** se l'applicazione non usa l'autenticazione integrata di Windows. |
| SPN dell'applicazione interna | Si tratta del nome dell'entità servizio (SPN) dell'applicazione interna come configurato nell'istanza di Azure AD locale. Il nome dell'entità servizio viene usato dal connettore proxy di applicazione per recuperare i token Kerberos per l'applicazione mediante la delega vincolata Kerberos. |


## Accesso Single Sign-On per app non Windows
Il flusso di delega Kerberos nel proxy di applicazione di Azure AD inizia quando Azure AD autentica l'utente nel cloud. Quando la richiesta arriva in locale, il connettore del proxy di applicazione di Azure AD rilascia un ticket Kerberos per conto dell'utente tramite l'interazione con Active Directory locale. Questo processo è definito come delega vincolata Kerberos. Nella fase successiva, viene inviata una richiesta all'applicazione back-end con il ticket Kerberos. Per la definizione dell'invio della richiesta sono presenti numerosi protocolli. La maggior parte dei server non Windows prevede l'uso del protocollo Negotiate/SPNego, al momento supportato nel proxy di applicazione di Azure AD.

![Diagramma SSO non Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### Identità delegata parziale
Le applicazioni non Windows in genere ottengono l'identità dell'utente sotto forma di nome utente o nome account SAM, non di indirizzo di posta elettronica (username@domain). A differenza, quindi, della maggior parte dei sistemi basati su Windows che preferiscono un UPN, che è più affidabile e assicura l'assenza di duplicati tra i domini.

Per questo motivo, il proxy di applicazione consente di selezionare l'identità da visualizzare nel ticket Kerberos per ogni applicazione. Alcune di queste opzioni sono adatte ai sistemi che non accettano il formato di indirizzo di posta elettronica.

![Schermata del parametro dell'identità di accesso delegata](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se viene usata l'identità parziale e questa identità non può essere univoca per tutti i domini o le foreste nell'organizzazione, è possibile pubblicare due volte le applicazioni mediante due diversi gruppi di connettore. Poiché ogni applicazione dispone di un pubblico di utenti diversi, è possibile unire i connettori a un altro dominio.


## Utilizzo dell'accesso Single Sign-On quando le identità cloud e locali non sono identiche
Se non diversamente configurato, il proxy di applicazione presuppone che gli utenti dispongano della stessa identità nel cloud e in locale. È possibile configurare, per ogni applicazione, le identità da usare durante l'esecuzione di Single Sign-On.

Questa funzionalità consente a molte organizzazioni con diverse identità locali e cloud di disporre dell'accesso Single Sign-On dal cloud ad applicazioni locali senza richiedere agli utenti di immettere nomi utente e password diversi. Sono incluse organizzazioni che:

- Hanno più domini internamente (joe@us.contoso.com, joe@eu.contoso.com) e un singolo dominio nel cloud (joe@contoso.com).

- Hanno un nome di dominio non indirizzabile internamente (joe@contoso.usa) e un dominio legale nel cloud.

- Non usano nomi di dominio internamente (joe).

- Usano diversi alias locali e nel cloud. Ad esempio, joe-johns@contoso.com e joej@contoso.com

Ciò risulta utile anche con le applicazioni che non accettano indirizzi nel formato di indirizzo di posta elettronica, ossia uno scenario molto comune per i server back-end non Windows.


### Impostazione dell'accesso Single Sign-On per varie identità cloud e locali

1. Configurare le impostazioni di Azure AD Connect in modo che l'identità principale sia l'indirizzo di posta elettronica (mail). Questo avviene come parte del processo di personalizzazione modificando il campo **Nome dell'entità utente** nelle impostazioni di sincronizzazione. Notare che queste impostazioni determinano anche il modo in cui gli utenti accedono a Office 365, ai dispositivi Windows 10 e ad altre applicazioni che usano Azure AD come archivio identità. ![Schermata di identificazione degli utenti - Elenco a discesa Nome dell'entità utente](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nelle impostazioni di configurazione dell'applicazione che si desidera modificare, selezionare l'**Identità di accesso delegata** da usare:
  - Nome dell'entità utente: joe@contoso.com  
  - Nome alternativo dell'entità utente: joed@contoso.local  
  - Nome utente parte del nome dell'entità utente: joe  
  - Nome utente parte del nome dell'entità utente alternativo: joe  
  - Nome account SAM locale: in base alla configurazione del controller di dominio locale

  ![Schermata del menu a discesa dell'identità di accesso delegata](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

### Risoluzione dei problemi di accesso Single Sign-On per diverse identità
Se si verifica un errore nel processo di accesso Single Sign-On, tale errore verrà visualizzato nel registro eventi del computer connettore, come illustrato in [Risoluzione dei problemi](active-directory-application-proxy-troubleshoot.md). Tuttavia, in alcuni casi, la richiesta verrà correttamente inviata all'applicazione back-end mentre tale applicazione invierà una risposta in numerose altre risposte HTTP. Per la risoluzione di problemi di questo tipo, è consigliabile iniziare esaminando il numero di eventi 24029 sul computer connettore nel registro eventi di sessione del proxy di applicazione. L'identità utente usata per la delega verrà visualizzata nel campo "utente" dei dettagli dell'evento. Per attivare il log della sessione, scegliere **Visualizza registri analitici e di debug** dal menu di visualizzazione del Visualizzatore eventi.


## Vedere anche

- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (Blog sul proxy di applicazione)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=AcomDC_0622_2016-->