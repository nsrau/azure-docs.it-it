<properties
	pageTitle="Come ottenere un tenant di Azure AD | Microsoft Azure"
	description="Come ottenere un tenant di Azure Active Directory per la registrazione e la creazione di applicazioni."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Come ottenere un tenant di Azure Active Directory

In Azure Active Directory (Azure AD), un [tenant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) rappresenta un'organizzazione. Si tratta di un'istanza dedicata del servizio Azure AD che l'organizzazione riceve al momento dell'iscrizione a un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365 e che diventa di sua proprietà. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD.

Un tenant ospita gli utenti di un'azienda e le informazioni su di essi, ad esempio password dati del profilo utente, autorizzazioni e così via. Contiene anche gruppi, applicazioni e altre informazioni relative all'azienda e alla sicurezza.

Per consentire agli utenti di Azure AD di accedere a un'applicazione, è necessario registrarla nel proprio tenant. La pubblicazione di un'applicazione in un tenant di Azure AD è **assolutamente gratuita**. Infatti, la maggior parte degli sviluppatori crea più tenant e applicazioni a fini di sperimentazione, sviluppo, staging e test. Le organizzazioni che si iscrivono all'applicazione e che la utilizzano, possono facoltativamente scegliere se acquistare le licenze, nel caso in cui intendano usufruire delle funzionalità avanzate della directory.

Come ottenere un tenant di Azure AD La procedura può variare nei casi in cui:

- [Si ha un abbonamento a Office 365](#use-an-existing-office-365-subscription)
- [Si ha una sottoscrizione di Azure precedente, associata a un account Microsoft](#use-an-msa-azure-subscription)
- [Si ha una sottoscrizione di Azure precedente, associata a un account aziendale](#use-an-organizational-azure-subscription)
- [Non si ha nessuna delle sottoscrizioni precedenti e si vuole iniziare da zero](#start-from-scratch)

## Usare una sottoscrizione di Office 365 esistente
Se si è abbonati a Office 365 ma non si ha una sottoscrizione di Azure (e quindi non è possibile accedere al [portale di gestione di Azure](https://manage.windowsazure.com)), seguire [queste istruzioni](https://technet.microsoft.com/library/dn832618.aspx) per ottenere l'accesso al tenant di Azure AD.

## Usare una sottoscrizione di Azure associata a un account Microsoft
Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account Microsoft personale, si ha già un tenant. Nel [portale di gestione di Azure](https://manage.windowsazure.com) dovrebbe essere presente un tenant denominato "Tenant predefinito" in "Tutti gli elementi" e in "Active Directory". È possibile usare questo tenant in base alle esigenze, ma potrebbe essere necessario dover creare un account amministratore aziendale.

A tale scopo, seguire questa procedura. In alternativa, è possibile creare un nuovo tenant e creare un amministratore in quel tenant seguendo una procedura simile.

1.	Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) con l'account personale
2.	Passare alla sezione "Active Directory" del portale (nella barra di spostamento sinistra)
3.	Selezionare la voce "Directory predefinita" nell'elenco delle directory disponibili
4.	Fare clic sul collegamento Utenti nella parte superiore della pagina. Nell'elenco verrà visualizzato un singolo utente con il valore "account Microsoft" indicato nella colonna Originato da
5.	Fare clic su "Aggiungi utente" nella parte inferiore della pagina
6.	Nel modulo di aggiunta dell'utente specificare le informazioni seguenti:
    - Tipo di utente: nuovo utente dell'organizzazione
    - Nome utente: scegliere un nome utente per questo amministratore
    - Nome/Cognome/Nome visualizzato: scegliere i valori appropriati
    - Ruolo: Amministratore globale
    - Indirizzo di posta elettronica alternativo: immettere i valori appropriati
    - Facoltativo: Abilita Multi-Factor Authentication
    - Infine, fare clic sul pulsante verde "CREA" per completare la creazione dell'utente e visualizzare la password temporanea.
7.	Una volta completato il modulo per l'aggiunta per l'utente e ricevuta la password temporanea per il nuovo utente amministrativo, assicurarsi di prendere nota della password in quanto sarà necessario eseguire l'accesso con questo nuovo utente per cambiare la password. È anche possibile inviare la password direttamente all'utente, usando un indirizzo di posta elettronica alternativo.
8.	Per cambiare la password temporanea, accedere a https://login.microsoftonline.com con questo nuovo account utente e, quando richiesto, modificare la password.


## Usare una sottoscrizione di Azure aziendale
Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account aziendale, si ha già un tenant. Nel [portale di gestione di Azure](https://manage.windowsazure.com) dovrebbe essere presente un tenant in "Tutti gli elementi" e in "Active Directory". È possibile usare questo tenant in base alle esigenze. Per creare un nuovo tenant, fare clic sul pulsante "Nuovo" nell'angolo inferiore sinistro del portale.


## Iniziare da zero
Se quanto descritto finora è incomprensibile, non è il caso di preoccuparsi. Passare a [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) per iscriversi ad Azure con un nuovo account aziendale. Al termine della procedura verrà creato il proprio tenant di Azure AD con il nome di dominio scelto durante l'iscrizione. Per trovare il tenant nel [portale di gestione di Azure](https://manage.windowsazure.com), passare ad "Active Directory" nella barra di spostamento a sinistra.

Durante la procedura di iscrizione ad Azure, verrà richiesto di indicare i dettagli della carta di credito. Non c'è nulla di cui preoccuparsi, la pubblicazione di applicazioni in Azure AD o la creazione di nuovi tenant non vengono addebitate.

<!---HONumber=Oct15_HO3-->