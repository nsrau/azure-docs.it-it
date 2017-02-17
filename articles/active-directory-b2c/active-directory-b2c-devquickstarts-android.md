---
title: 'Azure Active Directory B2C: chiamare un&quot;API Web da un&quot;applicazione Android | Documentazione Microsoft'
description: "Questo articolo descrive come creare un&quot;app &quot;elenco attività&quot; Android che chiama un&quot;API Web Node.js usando token di connessione OAuth 2.0. Sia l&quot;app Android che l&quot;API Web usano Azure Active Directory B2C per gestire le identità utente e autenticare gli utenti."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: chiamare un'API Web da un'applicazione Android
> [!WARNING]
> È stato pubblicato un esempio di codice Android aggiornato che si trova [qui](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi).  In questo esempio si utilizza una libreria di terze parti che è stata testata per la compatibilità in scenari di base con Azure AD B2C.  Questa libreria utilizza visualizzazioni Web incorporate piuttosto che il browser del sistema.  Google [ha annunciato](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) che a partire dal 20 aprile 2017 non sarà più supportato l'accesso con un account Google con visualizzazioni Web incorporate; pertanto, gli utenti che desiderano supportare gli account di Google dovranno aggiornare le librerie.  

>Microsoft non fornisce correzioni per queste librerie di terze parti e non ha eseguito una verifica su di esse. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria. Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>




<!--HONumber=Feb17_HO1-->


