---
title: 'Introduzione a iOS con Azure AD v2: test | Microsoft Docs'
description: "Informazioni sulle modalità per le applicazioni iOS (Swift) per chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Verificare l'esecuzione di query di API Graph di Microsoft dall'applicazione iOS

Premere `Command` + `R` per eseguire il codice nel simulatore.

![Schermata di esempio](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

Quando si è pronti per eseguire il test, toccare *"Call Microsoft Graph API"* (Chiamare l'API Graph di MIcrosoft) e verrà richiesto di digitare il nome utente e la password.

### <a name="consent"></a>Consenso
La prima volta che si accede all'applicazione, viene visualizzata una schermata di consenso, simile alla figura seguente, in cui è necessario accettare in modo esplicito:

![Schermata di consenso](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Risultati previsti
Si dovrebbero visualizzare le informazioni del profilo utente restituite dalla chiamata dell'API Microsoft Graph nella sezione *Registrazione*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede l'ambito `user.read` per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, per Microsoft Graph, l'ambito `Calendars.Read` è necessario per elencare i calendari dell'utente. Per poter accedere al calendario dell'utente nel contesto di un'applicazione, è necessario aggiungere l'autorizzazione delegata `Calendars.Read` alle informazioni di registrazione dell'applicazione e quindi aggiungere l'ambito `Calendars.Read` alla chiamata `acquireTokenSilent`. Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

<!--end-collapse-->



