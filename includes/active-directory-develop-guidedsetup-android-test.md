---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 991709ee635872e33dc89dcededc7f6ac3b28ea3
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843098"
---
## <a name="test-your-app"></a>Testare l'app

1. Distribuire il codice al dispositivo/emulatore.

2. Provare a eseguire l'accesso con un account Azure Active Directory (aziendale o dell'istituto di istruzione) o un account Microsoft (live.com, outlook.com). 

    ![Testare l'applicazione](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Immettere nome utente e password](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Fornire il consenso all'app
La prima volta che un utente accede all'applicazione, viene chiesto di fornire il consenso alle autorizzazioni necessarie all'app, come illustrato di seguito: 

![Specificare il consenso per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="success"></a>Completamento della procedura
Dopo che si è eseguito l'accesso e fornito il consenso, l'app visualizzerà una risposta dall'API Microsoft Graph. Questa chiamata specifica riguarda l'endpoint **/me** e restituisce il [profilo utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Per un elenco degli altri endpoint di Microsoft Graph, vedere la [documentazione per sviluppatori dell'API Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Ambiti e autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *User.Read* per leggere il profilo di un utente. Questo ambito viene aggiunto automaticamente in ogni app registrata nel portale di registrazione delle applicazioni. Altre API richiederanno altri ambiti. Ad esempio, l'API Microsoft Graph richiede l'ambito *Calendars.Read* per generare l'elenco dei calendari dell'utente. 

Per accedere ai calendari dell'utente, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`. 

>[!NOTE]
>Se si modifica la registrazione dell'app, è possibile che agli utenti vengano richiesti consensi aggiuntivi.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
