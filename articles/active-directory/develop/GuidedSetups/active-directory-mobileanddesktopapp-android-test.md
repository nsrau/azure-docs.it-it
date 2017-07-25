---
title: Introduzione ad Android per Azure AD v2 - Test | Microsoft Docs
description: "Informazioni sul modo in cui un'app di Android può ottenere un token di accesso e chiamare l'API o le API Graph Microsoft che richiedono token di acceso dall'endpoint di Azure Active Directory v2"
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
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 8523f46d6a352c9a6625ddeacc5abe2b4bbf977e
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="test-your-code" class="xliff"></a>

## Testare il codice

1. Distribuire il codice nel dispositivo/emulatore.
2. Quando si è pronti per eseguire il test, usare un account Microsoft Azure Active Directory (account aziendale) o un account Microsoft (live.com, outlook.com) per accedere. 

![Schermata di esempio](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Accesso](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

<a id="consent" class="xliff"></a>

### Consenso
La prima volta che un utente accede all'applicazione, viene visualizzata una schermata di consenso, simile alla figura seguente, in cui è necessario accettare in modo esplicito: 

![Consenso](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


<a id="expected-results" class="xliff"></a>

### Risultati previsti
Dovrebbero essere visualizzati i risultati di una chiamata all'endpoint 'me' dell'API Microsoft Graph usato per ottenere il profilo utente: https://graph.microsoft.com/v1.0/me. Per un elenco degli endpoint di Microsoft Graph comuni, consultare questo [articolo](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
<a id="more-information-about-scopes-and-delegated-permissions" class="xliff"></a>

### Altre informazioni sugli ambiti e sulle autorizzazioni delegate
L'API Graph richiede l'ambito `user.read` per leggere il profilo utente. Questo ambito viene aggiunto per impostazione predefinita a ogni applicazione registrata tramite il portale di registrazione. Altre API Graph e alcune API personalizzate per il server back-end richiedono anche altri ambiti. Graph, ad esempio, richiede l'ambito `Calendars.Read` per elencare i calendari degli utenti. Per poter accedere al calendario dell'utente nel contesto di un'applicazione, è necessario aggiungere le informazioni di registrazione di questa applicazione delegata e aggiungere `Calendars.Read` alla chiamata `AcquireTokenAsync`. Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se per un'API back-end non è richiesto alcun ambito (non consigliabile), è possibile usare `ClientId` come ambito nella chiamata `AcquireTokenAsync`.
<!--end-collapse-->

