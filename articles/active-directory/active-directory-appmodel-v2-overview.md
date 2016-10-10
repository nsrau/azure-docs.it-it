<properties
	pageTitle="Panoramica dell'endpoint v2.0 | Microsoft Azure"
	description="Introduzione alla compilazione di app con l'accesso sia per account Microsoft che per Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="dastrock"/>

# Accesso per account Microsoft e utenti di Azure AD nella stessa app

In passato, gli sviluppatori di app che intendevano supportare sia gli account Microsoft che Azure Active Directory dovevano eseguire l'integrazione con due sistemi separati. È ora disponibile una nuova versione dell'API di autenticazione che consente agli utenti di accedere con entrambi i tipi di account usando il sistema Azure AD. Questo sistema di autenticazione convergente è chiamato **endpoint v2.0**. Con l'endpoint v2.0 è sufficiente una semplice integrazione per raggiungere un pubblico costituito da milioni di utenti, sia con account personali che con account aziendali o dell'istituto di istruzione.

Le applicazioni con l'endpoint v2.0 possono inoltre usare le API REST da [Microsoft Graph](https://graph.microsoft.io) e [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) con entrambi i tipi di account.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## Introduzione
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Scegliere dall'elenco seguente la piattaforma preferita per compilare un'app usando le nostre librerie open source e i framework. In alternativa, è possibile usare la documentazione relativa al protocollo OAuth 2.0 e OpenID Connect per inviare e ricevere i messaggi di protocollo direttamente senza usare una libreria di autenticazione.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Novità
Le informazioni di carattere generale fornite di seguito saranno utili per individuare le operazioni che possono essere eseguite o meno con l'endpoint v2.0.

- Informazioni sui [tipi di app che si possono creare con l'endpoint v2.0](active-directory-v2-flows.md).
- Informazioni su [limitazioni, restrizioni e vincoli](active-directory-v2-limitations.md) dell'endpoint v2.0.
- Recentemente è stato aggiunto il supporto per gli [ambiti di amministrazione limitati](active-directory-v2-scopes.md) e la [concessione di credenziali del client OAuth2](active-directory-v2-protocols-oauth-client-creds.md). Si raccomanda di provare queste funzionalità.

## riferimento
I collegamenti seguenti sono utili per un'esplorazione più approfondita della piattaforma:

- Build 2016: [Guida introduttiva alle identità Microsoft: accesso di classe enterprise per le app](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Per ottenere supporto su Stack Overflow, usare i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- [Riferimento al protocollo v2.0](active-directory-v2-protocols.md)
- [Riferimento al token v2.0](active-directory-v2-tokens.md)
- [Informazioni di riferimento sulla libreria v2.0](active-directory-v2-libraries.md)
- [Ambiti e consenso nell'endpoint v2.0](active-directory-v2-scopes.md)
- [Portale di registrazione delle app Microsoft](https://apps.dev.microsoft.com)
- [Riferimento all'API REST di Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)

<!---HONumber=AcomDC_0928_2016-->