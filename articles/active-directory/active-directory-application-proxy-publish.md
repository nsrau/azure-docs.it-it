<properties
	pageTitle="Pubblicazione di applicazioni con il proxy dell'applicazione di Windows Azure"
	description="Viene descritto come pubblicare applicazioni locali utilizzando il proxy dell’applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/02/2015"
	ms.author="rkarlin"/>


# Pubblicare applicazioni mediante il proxy di applicazione AD Azure

> [AZURE.NOTE]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn532272.aspx).

Dopo aver abilitato il proxy di applicazione di Microsoft Azure AD, è possibile pubblicare le applicazioni per renderle accessibili agli utenti all’esterno della rete privata.

Questa sezione illustra i passaggi necessari per pubblicare le applicazioni in esecuzione nella rete locale per cui si desidera abilitare l'accesso remoto protetto dall'esterno della rete.

> [AZURE.NOTE]Per verificare che il connettore venga eseguito correttamente, la prima applicazione pubblicata deve essere di qualsiasi sito Web accessibile dalla rete privata per assicurare che gli utenti possano accedervi da Internet, prima di pubblicare un'applicazione reale.

## Pubblicare un'applicazione utilizzando la procedura guidata

1. Aprire un browser a scelta e accedere al portale di gestione di Azure.
2. Nel riquadro sinistro del portale di gestione di Azure, fare clic sulla scheda Active Directory.
3. Fare clic sulla directory in cui è abilitato il proxy dell'applicazione e per il quale si desidera pubblicare un'applicazione (ad esempio, Wingtip Toys).
4. Fare clic sulla scheda **Applicazioni** e scegliere il pulsante **Aggiungi** nella parte inferiore dello schermo ![Aggiunta di un'applicazione](http://i.imgur.com/wEeZ7IR.png)
5. Nella finestra di dialogo Cosa si desidera eseguire?, fare clic su **Pubblicare un'applicazione che sarà accessibile all'esterno della rete**.![Nuova applicazione che sarà accessibile dall'esterno della rete](http://i.imgur.com/Wj3vkyD.png)

6. Seguire le istruzioni sullo schermo per fornire le seguenti informazioni sull'applicazione: ![Proprietà dell'applicazione](http://i.imgur.com/MkPNc93.png)

**Impostazione** | **Dettagli**
---|---
URL esterno | Questo è l'URL del servizio cloud utilizzato per accedere all'applicazione dall'esterno della rete privata. L'URL viene generato automaticamente in base al nome specificato, con il suffisso msappproxy.net.
Metodo di preautenticazione | <p>Impostare il tipo del metodo di preautenticazione che si desidera che l'applicazione utilizzi:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD): consente di impostare il metodo di preautenticazione di Microsoft Azure Active Directory (Microsoft Azure AD). Quando un utente tenta di accedere a un'applicazione, il proxy dell’applicazione reindirizza l'utente per accedere con Azure AD, che autentica l'utente, per garantire che questo disponga delle autorizzazioni necessarie per la directory e l'applicazione.</p><p>b. La preautenticazione Passthrough non viene eseguita.</p>
Protocollo URL esterno | <p>Per impostazione predefinita, le applicazioni vengono pubblicate tramite il protocollo HTTPS.</p> <p>Per abilitare HTTP per un'applicazione interna, è necessario impostare la preautenticazione su Passthrough e quindi sarà possibile modificare il protocollo URL esterno da HTTPS a HTTP.</p> <p>Notare che la pubblicazione di applicazioni mediante il protocollo HTTP può creare problemi di sicurezza per l'applicazione e gli utenti.</p> <p>È possibile inserire un dominio personalizzato anziché utilizzare il valore predefinito di msappproxy.net. Per ulteriori informazioni, vedere di seguito.</p>
URL interno | Questo è l'URL interno che il connettore del proxy dell’applicazione usa per accedere all'applicazione internamente. Questo deve essere l'URL dell'applicazione pubblicata che viene utilizzato per accedere all'applicazione dall'interno della rete privata. Si tratta di un URL valido senza percorso e senza spazi o simboli.

Per completare la procedura guidata, fare clic sul segno di spunta nella parte inferiore della schermata. L'applicazione ora è definita in Azure AD.



## Assegnazione di utenti e gruppi all'applicazione

1. Per le applicazioni preautenticate, è necessario assegnare utenti e gruppi che avranno accesso all'applicazione. <p>Per le applicazioni pass-through, l'accesso è disponibile per tutti gli utenti. Tuttavia, affinché un utente possa visualizzare l'applicazione nel proprio elenco delle applicazioni, è necessario assegnare l'applicazione a tale utente.
2. Dopo aver completato l’Aggiunta guidata applicazione, viene visualizzata la pagina di avvio rapido del proxy dell’app. Per assegnare gli utenti, fare clic su **Assegna utenti**.![Schermata introduttiva del proxy dell’applicazione](http://i.imgur.com/OmuWElV.png)
3. Selezionare ogni utente o gruppo che si desidera assegnare a questa app e fare clic su **Assegna**. 

> [AZURE.NOTE]Per le applicazioni di autenticazione integrata di Windows, è possibile assegnare solo gli utenti e i gruppi sincronizzati dall’Active Directory locale. Non è possibile assegnare gli utenti che accedono tramite un account Microsoft e gli utenti guest per le applicazioni pubblicate con il proxy dell'applicazione di Azure Active Directory. Assicurarsi che gli utenti assegnati accedano con le loro credenziali che fanno parte dello stesso dominio dell'applicazione che si desidera pubblicare.

## Configurazione avanzata

1. È possibile modificare le applicazioni pubblicate o configurare le opzioni avanzate, ad esempio SSO per applicazioni locali, dalla pagina **Configura**. ![Configurazione avanzata](http://i.imgur.com/FhbRvoq.png)

2. Selezionare l'applicazione e fare clic su **Configura**. Sono disponibili le seguenti opzioni:

**Impostazione** | **Dettagli**
---|---
Nome | Fornire un nome descrittivo per l'applicazione
URL esterno | Questo è l'URL del servizio cloud utilizzato per accedere all'applicazione dall'esterno della rete privata. L'URL viene generato automaticamente in base al nome specificato, con il suffisso **msappproxy.net.**
Metodo di preautenticazione | Impostare il tipo di metodo di preautenticazione che l'applicazione deve utilizzare:<p>Active directory: consente di impostare il metodo di preautenticazione in Active Directory. Quando un utente tenta di accedere a un'applicazione, il proxy dell’applicazione reindirizza l'utente per accedere con il proxy dell'applicazione che autentica l'utente, garantendo che l'utente disponga delle autorizzazioni necessarie per la directory e l'applicazione.</p><p> La preautenticazione Passthrough non viene eseguita.</p>
URL esterno | Per impostazione predefinita, le applicazioni vengono pubblicate tramite il protocollo HTTPS. Per abilitare HTTP per un'applicazione interna, è necessario impostare la preautenticazione su Passthrough e quindi sarà possibile modificare il protocollo URL esterno da HTTPS a HTTP. Notare che la pubblicazione di applicazioni mediante il protocollo HTTP può creare problemi di sicurezza per l'applicazione e gli utenti. È possibile inserire un dominio personalizzato anziché utilizzare il dominio predefinito (msappproxy.net). Per altre informazioni, vedere Lavorare con domini personalizzati.
URL interno | Questo è l'URL interno che il connettore del proxy dell’applicazione usa per accedere all'applicazione internamente. Questo deve essere l'URL dell'applicazione pubblicata che viene utilizzato per accedere all'applicazione dall'interno della rete privata. Si tratta di un URL valido senza percorso e senza spazi o simboli.
Tradurre URL nelle intestazioni | Per le applicazioni (ad esempio, alcune configurazioni di SharePoint) che richiedono che le intestazioni host HTTP non vengano convertite, impostare su No. Questo disabiliterà la conversione dell’intestazione sia per le intestazioni di richiesta, che per quelle di risposta.
Metodo di autenticazione interna | Se si utilizza il proxy dell’applicazione per la preautenticazione, è possibile impostare un metodo di autenticazione interno per consentire agli utenti di beneficiare di Single Sign-On (SSO) su questa applicazione. <p> Selezionare**Autenticazione integrata di Windows (IWA)** se l'applicazione utilizza l'autenticazione integrata di Windows (IWA) ed è possibile configurare la delega vincolata Kerberos (Kerberos Constrained Delegation, KCD) per abilitare SSO per questa applicazione. <p> Selezionare**Nessuna** se l'applicazione non utilizza l'autenticazione integrata di Windows. <p> Le applicazioni che utilizzano l'autenticazione integrata di Windows devono essere configurate utilizzando la delega vincolata Kerberos, in caso contrario il proxy dell'applicazione non sarà in grado di pubblicare queste applicazioni. <p> Per ulteriori informazioni, vedere SSO per l’autenticazione integrata di Windows locale utilizzando la delega vincolata Kerberos con il proxy dell'applicazione
SPN dell’applicazione interna | Questo è il nome principale di servizio (SPN) dell'applicazione interna come configurato nel proxy dell’applicazione locale. L’SPN viene utilizzato dal connettore del proxy dell’applicazione per recuperare i token Kerberos per l'applicazione mediante la delega vincolata Kerberos (KCD). <p> Per ulteriori informazioni, vedere SSO per l’autenticazione integrata di Windows locale utilizzando la delega vincolata Kerberos con il proxy dell'applicazione

Dopo la pubblicazione di applicazioni mediante il proxy dell’applicazione di Azure Active Directory, queste vengono visualizzate nell'elenco delle applicazioni in Azure AD ed è possibile gestirle. Se si disabilitano i servizi proxy dell'applicazione dopo la pubblicazione delle applicazioni, queste non verranno eliminate, ma non saranno più accessibili dall'esterno della rete privata. Per visualizzare un'applicazione e assicurarsi che sia accessibile, fare doppio clic sul nome dell'applicazione. Se il servizio proxy dell’applicazione è disabilitato e l'applicazione non è disponibile, viene visualizzato un messaggio di avviso nella parte superiore della schermata. Per eliminare un'applicazione, selezionare un'applicazione nell'elenco e fare clic su **Elimina**.

## Risorse aggiuntive

* [Informazioni sulla delega vincolata Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=July15_HO3-->