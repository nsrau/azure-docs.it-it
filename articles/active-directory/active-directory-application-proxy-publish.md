<properties
	pageTitle="Pubblicazione di app con il proxy dell'applicazione di Azure AD | Microsoft Azure"
	description="Viene descritto come pubblicare applicazioni locali utilizzando il proxy dell’applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="kgremban"/>


# Pubblicare applicazioni mediante il proxy di applicazione AD Azure

> [AZURE.NOTE]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Dopo aver abilitato il proxy dell'applicazione Microsoft Azure Active Directory (AD), è possibile pubblicare le applicazioni per renderle accessibili agli utenti all'esterno della rete privata.

Questo articolo illustra i passaggi necessari per pubblicare le applicazioni in esecuzione nella rete locale per cui si vuole abilitare l'accesso remoto protetto dall'esterno della rete.

> [AZURE.NOTE]Per verificare che il connettore venga eseguito correttamente, la prima applicazione pubblicata deve essere di qualsiasi sito Web accessibile dalla rete privata per assicurare che gli utenti possano accedervi da Internet, prima di pubblicare un'applicazione reale.


## Pubblicare un'applicazione utilizzando la procedura guidata

1. Aprire il browser desiderato e accedere al portale di Azure classico.
2. Nel riquadro sinistro del portale di Azure classico fare clic sulla scheda **Active Directory**.
3. Fare clic sulla directory in cui è abilitato il proxy dell'applicazione e per il quale si desidera pubblicare un'applicazione (ad esempio, Wingtip Toys).
4. Fare clic sulla scheda **Applicazioni** e quindi fare clic sul pulsante **Aggiungi** nella parte inferiore dello schermo

	![Aggiunta di un'applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

5. Nella finestra di dialogo **Cosa si desidera eseguire?** fare clic su **Pubblicare un'applicazione che sarà accessibile all'esterno della rete**.

	![Nuova applicazione che sarà accessibile dall'esterno della rete](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. Seguire le istruzioni sullo schermo per fornire le seguenti informazioni sull'applicazione:

| **Proprietà** | **Dettagli** |
|---|---|
| URL esterno | Questo è l'URL del servizio cloud utilizzato per accedere all'applicazione dall'esterno della rete privata. L'URL viene generato automaticamente in base al nome specificato, con il suffisso msappproxy.net. |
| Metodo di preautenticazione | Impostare il tipo del metodo di preautenticazione che deve essere usato dall'applicazione: <br><br> a. Azure Active Directory: quando un utente tenta di accedere a un'applicazione, il proxy dell'applicazione reindirizza l'utente per accedere con Azure AD, che autentica l'utente, per garantire che questo disponga delle autorizzazioni necessarie per la directory e l'applicazione. <br><br> b. Passthrough: la preautenticazione non viene eseguita. |
| Protocollo URL esterno | Per impostazione predefinita, le applicazioni vengono pubblicate tramite il protocollo HTTPS. Il servizio reindirizzerà automaticamente gli utenti che digitano l'URL con http. <br><br>Per abilitare HTTP per un'applicazione interna, è necessario impostare il metodo di preautenticazione su Passthrough e quindi sarà possibile modificare il protocollo URL esterno da HTTPS a HTTP. Notare che la pubblicazione di applicazioni mediante il protocollo HTTP può creare problemi di sicurezza per l'applicazione e gli utenti. <br><br>È possibile inserire un dominio personalizzato anziché usare il valore predefinito di msappproxy.net. Per altre informazioni, vedere [Lavorare con domini personalizzati](active-directory-application-proxy-custom-domains.md). |
| URL interno | Questo è l'URL interno che il connettore del proxy dell’applicazione usa per accedere all'applicazione internamente. Questo deve essere l'URL dell'applicazione pubblicata che viene utilizzato per accedere all'applicazione dall'interno della rete privata. Si tratta di un URL valido senza spazi o simboli. <br><br>È possibile specificare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. Ciò consente di pubblicare, ad esempio, siti diversi posizionati nello stesso server di SharePoint con nomi e regole di accesso diversi. <br><br>Il percorso viene specificato nel campo dell'URL interno e sarà visibile nell'URL esterno. I percorsi interno ed esterno devono essere identici. |

  ![Proprietà dell'applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

  Per completare la procedura guidata, fare clic sul segno di spunta nella parte inferiore della schermata. L'applicazione ora è definita in Azure AD.


## Assegnazione di utenti e gruppi all'applicazione

1. Per le applicazioni preautenticate, è necessario assegnare utenti e gruppi che avranno accesso all'applicazione. Per le applicazioni passthrough, l'accesso è disponibile per tutti gli utenti. Tuttavia, affinché un utente possa visualizzare l'applicazione nel proprio elenco delle applicazioni, è necessario assegnare l'applicazione a tale utente.

2. Dopo aver completato l’Aggiunta guidata applicazione, viene visualizzata la pagina di avvio rapido del proxy dell’app. Per assegnare gli utenti, fare clic su **Assegna utenti**.

	![Schermata introduttiva del proxy dell'applicazione - Assegnazione degli utenti](./media/active-directory-application-proxy-publish/quickstart.png)

3. Selezionare ogni utente o gruppo che si desidera assegnare a questa app e fare clic su **Assegna**.

> [AZURE.NOTE]Per le applicazioni di autenticazione integrata di Windows, è possibile assegnare solo gli utenti e i gruppi sincronizzati dall’Active Directory locale. Non è possibile assegnare gli utenti che accedono tramite un account Microsoft e gli utenti guest per le applicazioni pubblicate con il proxy dell'applicazione di Azure Active Directory. Assicurarsi che gli utenti assegnati accedano con le loro credenziali che fanno parte dello stesso dominio dell'applicazione che si desidera pubblicare.


## Configurazione avanzata

1. È possibile modificare le app pubblicate o configurare le opzioni avanzate, ad esempio SSO per applicazioni locali, dalla pagina Configura.

	![Configurazione avanzata](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. Selezionare l'app e fare clic su **Configura**. Sono disponibili le seguenti opzioni:

**Impostazione** | **Dettagli**
---|---
Nome | Fornire un nome descrittivo per l'applicazione
URL esterno | Questo è l'URL del servizio cloud utilizzato per accedere all'applicazione dall'esterno della rete privata. L'URL viene generato automaticamente in base al nome specificato, con il suffisso msappproxy.net.
Metodo di preautenticazione | Impostare il tipo del metodo di preautenticazione che deve essere usato dall'applicazione: <br><br> a. Azure Active Directory: quando un utente tenta di accedere a un'applicazione, il proxy dell'applicazione reindirizza l'utente per accedere con Azure AD, che autentica l'utente, per garantire che questo disponga delle autorizzazioni necessarie per la directory e l'applicazione. <br><br> b. Passthrough: la preautenticazione non viene eseguita.
Protocollo URL esterno | Per impostazione predefinita, le applicazioni vengono pubblicate tramite il protocollo HTTPS. Il servizio reindirizzerà automaticamente gli utenti che digitano l'URL con http. <br><br>Per abilitare HTTP per un'applicazione interna, è necessario impostare il metodo di preautenticazione su Passthrough e quindi sarà possibile modificare il protocollo URL esterno da HTTPS a HTTP. Notare che la pubblicazione di applicazioni mediante il protocollo HTTP può creare problemi di sicurezza per l'applicazione e gli utenti. <br><br>È possibile inserire un dominio personalizzato anziché usare il valore predefinito di msappproxy.net. Per altre informazioni, vedere [Lavorare con domini personalizzati](active-directory-application-proxy-custom-domains.md).
URL interno | Questo è l'URL interno che il connettore del proxy dell’applicazione usa per accedere all'applicazione internamente. Questo deve essere l'URL dell'applicazione pubblicata che viene utilizzato per accedere all'applicazione dall'interno della rete privata. Si tratta di un URL valido senza spazi o simboli. <br><br>È possibile specificare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. Ciò consente di pubblicare, ad esempio, siti diversi posizionati nello stesso server di SharePoint con nomi e regole di accesso diversi. <br><br>Il percorso viene specificato nel campo dell'URL interno e sarà visibile nell'URL esterno. I percorsi interno ed esterno devono essere identici.
Tradurre URL nelle intestazioni | Per le applicazioni, ad esempio alcune configurazioni di SharePoint, che richiedono che le intestazioni host HTTP non vengano convertite, impostare su **No**. Verrà disabilitata la conversione dell'intestazione sia per le intestazioni di richiesta, che per quelle di risposta.
Metodo di autenticazione interno | Se si usa il proxy dell'applicazione per la preautenticazione, è possibile impostare un metodo di autenticazione interno per consentire agli utenti di beneficiare di Single Sign-On (SSO) su questa applicazione. <br><br> Selezionare **Autenticazione integrata di Windows (IWA)** se l'applicazione usa l'autenticazione integrata di Windows (IWA) ed è possibile configurare la delega vincolata Kerberos (Kerberos Constrained Delegation, KCD) per abilitare SSO per questa applicazione. Le applicazioni con autenticazione integrata di Windows devono essere configurate usando la delega vincolata Kerberos, in caso contrario il proxy di applicazione non sarà in grado di pubblicare queste applicazioni. <br><br> Selezionare **Nessuna** se l'applicazione non usa l'autenticazione integrata di Windows. <br><br>Per altre informazioni, vedere [Accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md).
SPN dell'applicazione interna | Questo è il nome principale di servizio (SPN) dell'applicazione interna come configurato nel proxy dell’applicazione locale. L’SPN viene utilizzato dal connettore del proxy dell’applicazione per recuperare i token Kerberos per l'applicazione mediante la delega vincolata Kerberos (KCD). <br><br>Per altre informazioni, vedere [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md).

Dopo la pubblicazione di applicazioni mediante il proxy dell’applicazione di Azure Active Directory, queste vengono visualizzate nell'elenco delle applicazioni in Azure AD ed è possibile gestirle.

Se si disabilitano i servizi proxy dell'applicazione dopo la pubblicazione delle applicazioni, queste non verranno eliminate, ma non saranno più accessibili dall'esterno della rete privata.

Per visualizzare un'applicazione e assicurarsi che sia accessibile, fare doppio clic sul nome dell'applicazione. Se il servizio proxy dell’applicazione è disabilitato e l'applicazione non è disponibile, viene visualizzato un messaggio di avviso nella parte superiore della schermata.

Per eliminare un'applicazione, selezionare un'applicazione nell'elenco e fare clic su **Elimina**.

## Vedere anche
Si può fare molto di più con il proxy dell'applicazione:

- [Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)
- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

## Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive

* [Informazioni sulla delega vincolata Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_0114_2016-->