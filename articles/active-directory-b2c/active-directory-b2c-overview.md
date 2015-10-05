<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Panoramica | Microsoft Azure"
	description="Sviluppo di applicazioni di utenti con Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: iscrizione e accesso degli utenti alle applicazioni

**Azure Active Directory B2C** è una soluzione di gestione identità cloud completa per utenti web e applicazioni per dispositivi mobili. È un servizio globale a disponibilità elevata adattabile a centinaia di milioni di identità di utenti. Basato su una piattaforma sicura aziendale, Azure Active Directory B2C mantiene le applicazioni, l'azienda e gli utenti protetti.

In passato, gli sviluppatori di applicazioni che volevano far iscrivere e accedere gli utenti nelle proprie applicazioni avrebbero dovuto utilizzare database o sistemi locali e scrivere tutto il codice di gestione di identità da soli. Azure Active Directory B2C offre agli sviluppatori un modo migliore per integrare la gestione delle identità utente nelle applicazioni con l'aiuto di una piattaforma basata su standard e un set completo di criteri estendibili. L’utilizzo di Azure Active Directory B2C consente agli utenti di registrarsi per le applicazioni mediante gli account sociali esistenti (Facebook, Google, Amazon, LinkedIn) o creando nuove credenziali (indirizzo di posta elettronica e password o nome utente e password); i secondi sono chiamati "account locali".

Azure Active Directory B2C è in anteprima. Durante questo periodo, siamo felici di ricevere i commenti e le esperienze durante la valutazione del prodotto. In base ai commenti e suggerimenti ricevuti, apporteremo tutte le modifiche necessarie per migliorare il servizio offerto. Non rilasciare un'applicazione per l'ambiente di produzione usando l'anteprima durante questo periodo. Inviare le opinioni utilizzando [Voce utente](http://feedback.azure.com/forums/169401-azure-active-directory).

## Introduzione

Per costruire un'applicazione che accetta l’iscrizione e l’accesso dell’utente, è innanzitutto necessario registrarla con una directory di Azure Active Directory B2C. Ottenere le directory utilizzando la procedura descritta in questo [articolo](active-directory-b2c-get-started.md).

È possibile scrivere l'applicazione con il servizio di Azure Active Directory B2C o scegliendo di inviare direttamente messaggi di protocollo, utilizzando [OAuth 2.0](active-directory-b2c-protocols.md#oauth2-authorization-code-flow) o [Open ID connessione](active-directory-b2c-protocols.md#openid-connect-sign-in-flow) oppure utilizzando le librerie per svolgere il lavoro (scegliere di seguito la piattaforma e iniziare a usarla).

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Novità

Visitare spesso questa pagina per rimanere aggiornati sulle modifiche all'anteprima di Azure Active Directory B2C. Gli aggiornamenti saranno anche resi noti su Twitter all'indirizzo @AzureAD.

- Scopri il [framework di criteri estendibili](active-directory-b2c-reference-policies.md) e i tipi di criteri che è possibile creare e utilizzare nelle applicazioni.
- [Limitazioni, restrizioni e vincoli della versione di anteprima](active-directory-b2c-limitations.md) corrente

## HOWTO

Informazioni su come utilizzare funzionalità di anteprima di Azure Active Directory B2C specifiche:

- Configurare gli account ([Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md)) per l'utilizzo nelle applicazioni degli utenti.
- [Utilizzare attributi personalizzati per raccogliere informazioni sugli utenti](active-directory-b2c-reference-custom-attr.md).
- [Abilitare Multi-Factor Authentication nelle applicazioni degli utenti](active-directory-b2c-reference-mfa.md).
- [Installazione la reimpostazione self-service della password per gli utenti](active-directory-b2c-reference-sspr.md).
- [Personalizzare l’aspetto dell'iscrizione, dell’accesso e altre pagine per gli utenti](active-directory-b2c-reference-ui-customization.md) gestite da Azure Active Directory B2C.
- [Utilizzare Active Directory API Graph di Azure per creare, leggere, aggiornare ed eliminare gli utenti a livello di programmazione](active-directory-b2c-devquickstarts-graph-dotnet.md) nella directory di Azure Active Directory B2C.

## Riferimento

I collegamenti seguenti sono utili per un'esplorazione più approfondita del servizio:

- Per ottenere supporto su Stack Overflow, usare i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- Per inviare i commenti e i suggerimenti sulla versione di anteprima, è possibile usare [UserVoice](http://feedback.azure.com/forums/169401-azure-active-directory). Specificare "AzureADB2C:" nel titolo del post per permetterci di individuarlo.
- Azure Active Directory B2C supporta protocolli standard del settore, OpenID Connect e OAuth 2.0, utilizzando un modello di registrazione dell'applicazione chiamato "Modello App v 2.0".
  - [Riferimento al protocollo di Modello app 2.0](active-directory-b2c-protocols.md)
  - [Riferimento al token di Modello app 2.0](active-directory-b2c-tokens.md)
- [Domande frequenti Azure Active Directory B2C](active-directory-b2c-faqs.md)
- [Richieste di supporto del file su Azure Active Directory B2C](active-directory-b2c-support.md)

<!---HONumber=Sept15_HO4-->