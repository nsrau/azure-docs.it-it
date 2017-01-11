---
title: Panoramica dell&quot;endpoint v2.0 | Documentazione Microsoft
description: Introduzione alla compilazione di app con l&quot;accesso sia per account Microsoft che per Azure Active Directory.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e98ec55dbe5ff0411af124b45bd7ef6177aa957e


---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Accesso per account Microsoft e utenti di Azure AD nella stessa app
In passato, gli sviluppatori di app che intendevano supportare sia gli account Microsoft che Azure Active Directory dovevano eseguire l'integrazione con due sistemi separati.  È ora disponibile una nuova versione dell'API di autenticazione che consente agli utenti di accedere con entrambi i tipi di account usando il sistema Azure AD.  Questo sistema di autenticazione convergente è chiamato **endpoint v2.0**.  Con l'endpoint v2.0 è sufficiente una semplice integrazione per raggiungere un pubblico costituito da milioni di utenti, sia con account personali che con account aziendali o dell'istituto di istruzione.

Le applicazioni con l'endpoint v2.0 possono inoltre usare le API REST da [Microsoft Graph](https://graph.microsoft.io) e [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) con entrambi i tipi di account.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Introduzione
[!VIDEO https://channel9.msdn.com/Events/Build/2016/P530/player]


Scegliere dall'elenco seguente la piattaforma preferita per compilare un'app usando le nostre librerie open source e i framework.  In alternativa, è possibile usare la documentazione relativa al protocollo OAuth 2.0 e OpenID Connect per inviare e ricevere i messaggi di protocollo direttamente senza usare una libreria di autenticazione.

<!-- TODO: Finalize this table  -->
[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Novità
Le informazioni di carattere generale fornite di seguito saranno utili per individuare le operazioni che possono essere eseguite o meno con l'endpoint v2.0.

* Informazioni sui [tipi di app che si possono creare con l'endpoint v2.0](active-directory-v2-flows.md).
* Informazioni su [limitazioni, restrizioni e vincoli](active-directory-v2-limitations.md) dell'endpoint v2.0.
* Recentemente è stato aggiunto il supporto per gli [ambiti di amministrazione limitati](active-directory-v2-scopes.md) e la [concessione di credenziali del client OAuth2](active-directory-v2-protocols-oauth-client-creds.md).  Si raccomanda di provare queste funzionalità.

## <a name="reference"></a>riferimento
I collegamenti seguenti sono utili per un'esplorazione più approfondita della piattaforma:

* Build 2016: [Guida introduttiva alle identità Microsoft: accesso di classe enterprise per le app](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
* Per ottenere supporto sull'overflow dello stack, usare i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
* [Riferimento al protocollo v2.0](active-directory-v2-protocols.md)
* [Riferimento al token v2.0](active-directory-v2-tokens.md)
* [Informazioni di riferimento sulla libreria v2.0](active-directory-v2-libraries.md)
* [Ambiti e consenso nell'endpoint v2.0](active-directory-v2-scopes.md)
* [Portale di registrazione delle app Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
* [Riferimento all'API REST di Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
* [Microsoft Graph](https://graph.microsoft.io)




<!--HONumber=Dec16_HO4-->


