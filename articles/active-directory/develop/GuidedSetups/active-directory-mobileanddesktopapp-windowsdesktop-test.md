---
title: Introduzione a Windows Desktop per Azure AD v2 - Test | Microsoft Docs
description: Come applicazioni .NET per Windows Desktop (XAML) possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
## <a name="test-your-code"></a>Testare il codice

Per testare l'applicazione, premere `F5` per eseguire il progetto in Visual Studio. Verrà visualizzata la finestra principale:

![Schermata di esempio](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Quando si è pronti per eseguire il test, fare clic su *Call Microsoft Graph API* (Chiama API Microsoft Graph) e usare un account Microsoft Azure Active Directory (account aziendale) o un account Microsoft (live.com, outlook.com) per accedere. Se è la prima volta, verrà visualizzata una finestra in cui si chiede all'utente di eseguire l'accesso:

![Accesso](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Consenso
La prima volta che si accede all'applicazione, viene visualizzata una schermata di consenso, simile alla figura seguente, in cui è necessario accettare in modo esplicito:

![Schermata di consenso](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Risultati previsti
Nella schermata API Call Results (Risultati chiamata API) vengono visualizzate le informazioni sul profilo utente restituite dalla chiamata all'API Microsoft Graph.

Nella casella Token Info (Informazioni sul token) vengono visualizzate anche informazioni di base sul token acquisite tramite `AcquireTokenAsync` o `AcquireTokenSilentAsync`:

|Proprietà  |Format  |Descrizione |
|---------|---------|---------|
|Nome | {Nome utente completo} |Nome e cognome dell'utente|
|Username |<span>user@domain.com</span> |Nome utente usato per identificare l'utente|
|Token Expires (Scadenza token) |{DateTime}         |Ora in cui scadrà il token. MSAL estende automaticamente la data di scadenza rinnovando il token ogni volta che è necessario|
|Token di accesso |{Stringa}         |Stringa di token che verrà inviata alle richieste HTTP che richiedono un'intestazione di autorizzazione|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate
L'API Graph richiede l'ambito `user.read` per leggere il profilo utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API Graph e alcune API personalizzate per il server back-end richiedono anche altri ambiti. Graph, ad esempio, richiede l'ambito `Calendars.Read` per elencare i calendari degli utenti. Per poter accedere al calendario dell'utente nel contesto di un'applicazione, è necessario aggiungere le informazioni di registrazione dell'applicazione delegata `Calendars.Read` e aggiungere `Calendars.Read` alla chiamata `AcquireTokenAsync`. Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

<!--end-collapse-->




