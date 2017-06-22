---
title: Endpoint v2.0 di Azure Active Directory | Microsoft Docs
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
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 4a9bf4193017313f532b398880a84966367f0b58
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Accesso per account Microsoft e utenti di Azure AD nella stessa app
In passato, gli sviluppatori di app che intendevano supportare sia gli account personali che quelli aziendali di Microsoft da Azure Active Directory dovevano eseguire l'integrazione con due sistemi separati.  L'**endpoint v2.0 di Azure AD** introduce una nuova versione dell'API di autenticazione che consente agli utenti di accedere con entrambi i tipi di account usando una semplice integrazione.  Le app che usano l'endpoint v2.0 possono anche usare le API REST da [Microsoft Graph](https://graph.microsoft.io) con entrambi i tipi di account.

## <a name="getting-started"></a>Introduzione
Scegliere dall'elenco seguente la piattaforma preferita per compilare un'app usando le nostre librerie open source e i framework.  In alternativa, è possibile usare la documentazione relativa al protocollo OAuth 2.0 e OpenID Connect per inviare e ricevere i messaggi di protocollo direttamente senza usare una libreria di autenticazione.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Novità
Le informazioni fornite di seguito saranno utili per individuare le operazioni che possono essere eseguite o meno con l'endpoint v2.0.

* Informazioni sui [tipi di app che si possono creare con l'endpoint v2.0](active-directory-v2-flows.md).
* Informazioni su [limitazioni, restrizioni e vincoli](active-directory-v2-limitations.md) dell'endpoint v2.0.
* Leggere questa panoramica video per l'endpoint v 2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Riferimento
I collegamenti seguenti sono utili per un'esplorazione più approfondita della piattaforma:

* [Riferimento al protocollo v2.0](active-directory-v2-protocols.md)
* [Riferimento al token v2.0](active-directory-v2-tokens.md)
* [Informazioni di riferimento sulla libreria v2.0](active-directory-v2-libraries.md)
* [Ambiti e consenso nell'endpoint v2.0](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>Guida e supporto
Il modo migliore per ottenere assistenza per lo sviluppo in Azure Active Directory.

* [Tag `azure-active-directory` e `adal` di Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Commenti e suggerimenti su Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Se occorre effettuare l'accesso agli account di lavoro e dell'istituto di istruzione da Azure Active Directory, è consigliabile iniziare con la [guida per gli sviluppatori di Azure AD](active-directory-developers-guide.md).  L'uso dell'endpoint v2.0 è destinato agli sviluppatori che devono eseguire l'accesso agli account personali di Microsoft in modo esplicito.


