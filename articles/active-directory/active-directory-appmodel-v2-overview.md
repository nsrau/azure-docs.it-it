<properties
	pageTitle="Panoramica del Modello app 2.0 | Microsoft Azure"
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Accesso per account Microsoft e utenti di Azure AD nella stessa app

In passato, gli sviluppatori di app che intendevano supportare sia gli account Microsoft che Azure Active Directory dovevano eseguire l'integrazione con due sistemi separati. È ora disponibile una nuova versione dell'API di autenticazione che consente agli utenti di accedere con entrambi i tipi di account usando il sistema Azure AD. Questo sistema di autenticazione convergente è chiamato **endpoint v2.0**. Con l'endpoint v2.0 è sufficiente una semplice integrazione per raggiungere un pubblico costituito da milioni di utenti, sia con account personali che con account aziendali o dell'istituto di istruzione.

Le applicazioni con l'endpoint v2.0 possono inoltre usare le API REST da [Microsoft Graph](https://graph.microsoft.io) e [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) con entrambi i tipi di account.

> [AZURE.NOTE]
	Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).


## Introduzione
Scegliere la piattaforma preferita per compilare un'app usando le nostre librerie open source e i framework. In alternativa, è possibile usare la documentazione del protocollo OAuth 2.0 e OpenID Connect per inviare e ricevere i messaggi di protocollo direttamente senza usare una libreria di autenticazione. <!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Novità	
Le informazioni di carattere generale fornite di seguito saranno utili per individuare le operazioni che possono essere eseguite o meno con l'endpoint v2.0.

- Se l'app viene compilata durante il periodo di anteprima dell'endpoint 2015 v2.0, assicurarsi di [leggere le informazioni sulle modifiche più significative apportate al protocollo](active-directory-v2-preview-oidc-changes.md) di recente.
- Informazioni sui [tipi di app che si possono creare con l'endpoint v2.0](active-directory-v2-flows.md).
- Gli sviluppatori che hanno familiarità con Azure Active Directory possono vedere l'articolo che illustra gli [aggiornamenti ai protocolli e le differenze nell'endpoint v2.0](active-directory-v2-compare.md).
- Comprendere le [limitazioni, le restrizioni e i vincoli](active-directory-v2-limitations.md) dell'endpoint v2.0.

## riferimento
I collegamenti seguenti sono utili per un'esplorazione più approfondita della piattaforma:

- Per ottenere supporto su Stack Overflow, usare i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- [Riferimento al protocollo v2.0](active-directory-v2-protocols.md)
- [Riferimento al token v2.0](active-directory-v2-tokens.md)
- [Ambiti e consenso nell'endpoint v2.0](active-directory-v2-scopes.md)
- [Portale di registrazione delle app Microsoft](https://apps.dev.microsoft.com)
- [Riferimento all'API REST di Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- Di seguito sono elencate le librerie client open source e gli esempi che sono stati testati con l'endpoint v2.0. Si noti che funzionalità quali [la registrazione client dinamica di OpenID Connect](https://openid.net/specs/openid-connect-registration-1_0.html) e gli endpoint di convalida del token non sono ancora supportate e potrebbero dover essere disabilitate nella libreria per poter usare l'endpoint v2:  

  - [Java WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Client di base PHP OpenID Connect](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Esempio Android OpenID Connect](https://github.com/learning-layers/android-openid-connect)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them!  Use the phrase "AppModelv2:" in the title of your post so we can find it.
-->

<!---HONumber=AcomDC_0224_2016-->