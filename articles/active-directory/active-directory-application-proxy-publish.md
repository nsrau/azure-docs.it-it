<properties
	pageTitle="Pubblicazione di applicazioni con il proxy dell'applicazione di Windows Azure"
	description="Viene descritto come pubblicare applicazioni locali utilizzando il proxy dell’applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="rkarlin"/>


# Pubblicare applicazioni mediante il proxy di applicazione AD Azure

> [AZURE.NOTE]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-edition.md).

Dopo aver abilitato il proxy di applicazione di Microsoft Azure AD, è possibile pubblicare le applicazioni per renderle accessibili agli utenti all’esterno della rete privata.

Questa sezione illustra i passaggi necessari per pubblicare le applicazioni in esecuzione nella rete locale per cui si desidera abilitare l'accesso remoto protetto dall'esterno della rete.

> [AZURE.NOTE]Per verificare che il connettore venga eseguito correttamente, la prima applicazione pubblicata deve essere di qualsiasi sito Web accessibile dalla rete privata per assicurare che gli utenti possano accedervi da Internet, prima di pubblicare un'applicazione reale.

## Pubblicare un'applicazione utilizzando la procedura guidata

1. Aprire un browser a scelta e accedere al portale di gestione di Azure.
2. Nel riquadro sinistro del portale di gestione di Azure, fare clic sulla scheda Active Directory.
3. Fare clic sulla directory in cui è abilitato il proxy dell'applicazione e per il quale si desidera pubblicare un'applicazione (ad esempio, Wingtip Toys).
4. Fare clic sulla scheda **Applicazioni** e quindi fare clic sul pulsante **Aggiungi** nella parte inferiore dello schermo ![Aggiunta di un'applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
5. Nella finestra di dialogo Cosa si desidera eseguire? fare clic su **Pubblicare un'applicazione che sarà accessibile all'esterno della rete**. ![Nuova applicazione che sarà accessibile dall'esterno della rete](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. Seguire le istruzioni sullo schermo per fornire le seguenti informazioni sull'applicazione: ![Proprietà dell'applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

**Impostazione** | **Dettagli**
---|---
URL esterno | Questo è l'URL del servizio cloud utilizzato per accedere all'applicazione dall'esterno della rete privata. L'URL viene generato automaticamente in base al nome specificato, con il suffisso msappproxy.net.
Metodo di preautenticazione | <p>Impostare il tipo del metodo di preautenticazione che si desidera che l'applicazione utilizzi:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD): consente di impostare il metodo di preautenticazione di Microsoft Azure Active Directory (Microsoft Azure AD). Quando un utente tenta di accedere a un'applicazione, il proxy dell’applicazione reindirizza l'utente per accedere con Azure AD, che autentica l'utente, per garantire che questo disponga delle autorizzazioni necessarie per la directory e l'applicazione.</p><p>b. La preautenticazione Passthrough non viene eseguita.</p>
Protocollo URL esterno | <p>Per impostazione predefinita, le applicazioni vengono pubblicate tramite il protocollo HTTPS.</p> <p>Per abilitare HTTP per un'applicazione interna, è necessario impostare la preautenticazione su Passthrough e quindi sarà possibile modificare il protocollo URL esterno da HTTPS a HTTP.</p> <p>Notare che la pubblicazione di applicazioni mediante il protocollo HTTP può creare problemi di sicurezza per l'applicazione e gli utenti.</p> <p>È possibile inserire un dominio personalizzato anziché utilizzare il valore predefinito di msappproxy.net. Per ulteriori informazioni, vedere di seguito.</p>
URL interno | Questo è l'URL interno che il connettore del proxy dell’applicazione usa per accedere all'applicazione internamente. Questo deve essere l'URL dell'applicazione pubblicata che viene utilizzato per accedere all'applicazione dall'interno della rete privata. Si tratta di un URL valido senza spazi o simboli. È possibile specificare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. Ciò consente di pubblicare, ad esempio, siti diversi posizionati nello stesso server di SharePoint con nomi e regole di accesso diversi.
Il percorso viene specificato nel campo dell'URL interno e sarà visibile nell'URL esterno. I percorsi interno ed esterno devono essere identici.


Per completare la procedura guidata, fare clic sul segno di spunta nella parte inferiore della schermata. L'applicazione ora è definita in Azure AD.



## Assegnazione di utenti e gruppi all'applicazione

1. Per le applicazioni preautenticate, è necessario assegnare utenti e gruppi che avranno accesso all'applicazione. <p>Per le applicazioni passthrough, l'accesso è disponibile per tutti gli utenti. Tuttavia, affinché un utente possa visualizzare l'applicazione nel proprio elenco delle applicazioni, è necessario assegnare l'applicazione a tale utente.
2. Dopo aver completato l’Aggiunta guidata applicazione, viene visualizzata la pagina di avvio rapido del proxy dell’app. Per assegnare gli utenti, fare clic su **Assegna utenti**. ![Schermata introduttiva del proxy dell’applicazione](./media/active-directory-application-proxy-publish/quickstart.png)
3. Selezionare ogni utente o gruppo che si desidera assegnare a questa app e fare clic su **Assegna**. 

> [AZURE.NOTE]Per le applicazioni di autenticazione integrata di Windows, è possibile assegnare solo gli utenti e i gruppi sincronizzati dall’Active Directory locale. Non è possibile assegnare gli utenti che accedono tramite un account Microsoft e gli utenti guest per le applicazioni pubblicate con il proxy dell'applicazione di Azure Active Directory. Assicurarsi che gli utenti assegnati accedano con le loro credenziali che fanno parte dello stesso dominio dell'applicazione che si desidera pubblicare.

## Configurazione avanzata

1. È possibile modificare le app pubblicate o configurare le opzioni avanzate, ad esempio SSO per applicazioni locali, dalla pagina **Configura**. ![Configurazione avanzata](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. Selezionare l'app e fare clic su **Configura**. Sono disponibili le seguenti opzioni:

**Impostazione** | **Dettagli**
---|---
Nome | Fornire un nome descrittivo per l'applicazione
URL esterno | Questo è l'URL del servizio cloud utilizzato per accedere all'applicazione dall'esterno della rete privata. L'URL viene generato automaticamente in base al nome specificato, con il suffisso **msappproxy.net.**
Metodo di preautenticazione | Impostare il tipo di metodo di preautenticazione che l'applicazione deve usare:<p>Active Directory: consente di impostare il metodo di preautenticazione su Active Directory. Quando un utente tenta di accedere a un'applicazione, il proxy dell'applicazione reindirizza l'utente per accedere con il proxy dell'applicazione che autentica l'utente, garantendo che l'utente disponga delle autorizzazioni necessarie per la directory e l'applicazione.</p><p> La preautenticazione passthrough non viene eseguita.</p>
URL esterno | Per impostazione predefinita, le applicazioni vengono pubblicate tramite il protocollo HTTPS. Per abilitare HTTP per un'applicazione interna, è necessario impostare la preautenticazione su Passthrough e quindi sarà possibile modificare il protocollo URL esterno da HTTPS a HTTP. Notare che la pubblicazione di applicazioni mediante il protocollo HTTP può creare problemi di sicurezza per l'applicazione e gli utenti. È possibile inserire un dominio personalizzato anziché utilizzare il dominio predefinito (msappproxy.net). Per altre informazioni, vedere Lavorare con domini personalizzati.
URL interno | Questo è l'URL interno che il connettore del proxy dell’applicazione usa per accedere all'applicazione internamente. Questo deve essere l'URL dell'applicazione pubblicata che viene utilizzato per accedere all'applicazione dall'interno della rete privata. Si tratta di un URL valido senza spazi o simboli. È possibile specificare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. Ciò consente di pubblicare, ad esempio, siti diversi posizionati nello stesso server di SharePoint con nomi e regole di accesso diversi.
Il percorso viene specificato nel campo dell'URL interno e sarà visibile nell'URL esterno. I percorsi interno ed esterno devono essere identici. Convertire l'URL nelle intestazioni | Per le applicazioni (ad esempio, alcune configurazioni di SharePoint) che richiedono che le intestazioni host HTTP non vengano convertite, impostare su No. Questo disabiliterà la conversione dell'intestazione sia per le intestazioni di richiesta, che per quelle di risposta. Metodo di autenticazione interna | Se si usa il proxy dell'applicazione per la preautenticazione, è possibile impostare un metodo di autenticazione interno per consentire agli utenti di beneficiare di Single Sign-On (SSO) per questa applicazione. <p> Selezionare**Autenticazione integrata di Windows (IWA)** se l'applicazione usa l'autenticazione integrata di Windows (IWA) ed è possibile configurare la delega vincolata Kerberos (Kerberos Constrained Delegation, KCD) per abilitare SSO per questa applicazione. <p> Selezionare **Nessuna** se l'applicazione non usa l'autenticazione integrata di Windows. <p> Le applicazioni che usano l'autenticazione integrata di Windows devono essere configurate con la delega vincolata Kerberos, in caso contrario il proxy dell'applicazione non sarà in grado di pubblicare queste applicazioni. <p> Per altre informazioni, vedere SSO per l'autenticazione integrata di Windows locale utilizzando la delega vincolata Kerberos con il proxy dell'applicazione SPN dell'applicazione interna | Questo è il nome principale di servizio (SPN) dell'applicazione interna come configurato nel proxy dell'applicazione locale. L’SPN viene utilizzato dal connettore del proxy dell’applicazione per recuperare i token Kerberos per l'applicazione mediante la delega vincolata Kerberos (KCD). <p> Per altre informazioni, vedere [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)

Dopo la pubblicazione di applicazioni mediante il proxy dell’applicazione di Azure Active Directory, queste vengono visualizzate nell'elenco delle applicazioni in Azure AD ed è possibile gestirle. Se si disabilitano i servizi proxy dell'applicazione dopo la pubblicazione delle applicazioni, queste non verranno eliminate, ma non saranno più accessibili dall'esterno della rete privata. Per visualizzare un'applicazione e assicurarsi che sia accessibile, fare doppio clic sul nome dell'applicazione. Se il servizio proxy dell’applicazione è disabilitato e l'applicazione non è disponibile, viene visualizzato un messaggio di avviso nella parte superiore della schermata. Per eliminare un'applicazione, selezionare un'applicazione nell'elenco e fare clic su **Elimina**.

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

<!---HONumber=Oct15_HO3-->