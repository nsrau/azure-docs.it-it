<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Panoramica | Microsoft Azure"
	description="Sviluppo di applicazioni di utenti con Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: iscrizione e accesso degli utenti alle applicazioni

Azure Active Directory B2C è una soluzione di gestione identità cloud completa per utenti Web e applicazioni per dispositivi mobili. È un servizio globale a disponibilità elevata adattabile a centinaia di milioni di identità di utenti. Basato su una piattaforma sicura aziendale, Azure Active Directory B2C mantiene le applicazioni, l'azienda e gli utenti protetti.

In passato, gli sviluppatori di applicazioni che desideravano iscriversi e accedere ai consumer nelle proprie applicazioni dovevano scrivere il proprio codice. E dovevano utilizzare database locali o sistemi per archiviare nomi utente e password. Azure Active Directory B2C offre agli sviluppatori un modo migliore per integrare la gestione delle identità utente nelle applicazioni con l'aiuto di una piattaforma sicura, basata su standard e un set completo di criteri estendibili. Quando si usa Azure Active Directory B2C, gli utenti possono iscriversi alle applicazioni usando gli account di social networking esistenti (Facebook, Google, Amazon, LinkedIn) o creando nuove credenziali (indirizzo di posta elettronica e password o nome utente e password). Queste ultime sono chiamate "account locali".

Azure Active Directory B2C è in anteprima. Durante questo periodo, siamo felici di ricevere i commenti e le esperienze durante la valutazione del prodotto. In base ai commenti e suggerimenti ricevuti, apporteremo tutte le modifiche necessarie per migliorare il servizio offerto. Non rilasciare un'applicazione per l'ambiente di produzione che usa l'anteprima durante questo periodo. Inviare le opinioni utilizzando [Voce utente](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Introduzione

Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, prima di tutto sarà necessario registrarla con un tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md).

È possibile scrivere l'applicazione con il servizio di Azure Active Directory B2C scegliendo di inviare direttamente messaggi di protocollo, con [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) o [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), oppure usando le librerie per eseguire automaticamente l'operazione. Scegliere la piattaforma preferita nella tabella seguente e iniziare.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Novità

Visitare spesso questa pagina per rimanere aggiornati sulle modifiche all'anteprima di Azure Active Directory B2C. Gli aggiornamenti saranno anche resi noti su Twitter con @AzureAD.

- Informazioni sul [framework di criteri estendibili](active-directory-b2c-reference-policies.md) e sui tipi di criteri che è possibile creare e usare nelle applicazioni.
- [Limitazioni, restrizioni e vincoli della versione di anteprima](active-directory-b2c-limitations.md) corrente.

## Procedure

Informazioni su come utilizzare funzionalità di anteprima di Azure Active Directory B2C specifiche:

- Configurare gli account [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md) per l'uso nelle applicazioni destinate agli utenti.
- [Utilizzare attributi personalizzati per raccogliere informazioni sugli utenti](active-directory-b2c-reference-custom-attr.md).
- [Abilitare Azure Multi-Factor Authentication nelle applicazioni degli utenti](active-directory-b2c-reference-mfa.md).
- [Configurare la reimpostazione della password self-service per gli utenti](active-directory-b2c-reference-sspr.md).
- [Personalizzare l'aspetto della pagina di iscrizione, di accesso e di altre pagine per gli utenti](active-directory-b2c-reference-ui-customization.md) gestite da Azure Active Directory B2C.
- [Usare l'API Graph di Azure Active Directory per creare, leggere, aggiornare ed eliminare gli utenti a livello di programmazione](active-directory-b2c-devquickstarts-graph-dotnet.md) nel tenant di Azure Active Directory B2C.

## Passaggi successivi

I collegamenti seguenti sono utili per un'esplorazione più approfondita del servizio:

- Vedere le [Informazioni sui prezzi di Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Ottenere supporto per l'overflow dello stack usando i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- Inviare commenti e i suggerimenti sulla versione di anteprima usando [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/). Specificare "AzureADB2C:" nel titolo del post per permetterci di individuarlo.
- Azure Active Directory B2C supporta protocolli standard del settore, OpenID Connect e OAuth 2.0, usando un modello di registrazione dell'applicazione chiamato "Modello App v 2.0".
  - [Riferimento al protocollo di Modello app 2.0](active-directory-b2c-reference-protocols.md)
  - [Riferimento al token di Modello app 2.0](active-directory-b2c-reference-tokens.md)
- Vedere le [Domande frequenti su Azure Active Directory B2C](active-directory-b2c-faqs.md).
- [Inviare richieste di supporto per Azure Active Directory B2C](active-directory-b2c-support.md).

<!---HONumber=AcomDC_0330_2016-->