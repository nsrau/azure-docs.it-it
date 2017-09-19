---
title: Configurazione guidata di JS SPA per Azure AD v2 - Test | Microsoft Docs
description: Come le applicazioni SPA di Javascript possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
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
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: c559c80781da3631a783d96539622c4c89fe7e17
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
## <a name="test-your-code"></a>Testare il codice

> ### <a name="testing-with-visual-studio"></a>Test con Visual Studio
> Se si usa Visual Studio, premere `F5` per eseguire il progetto: verrà aperto il browser e l'utente verrà indirizzato a *http://localhost:{port}*, in cui verrà visualizzato il pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph).

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Test con Python o un altro server Web
> Se non si usa Visual Studio, assicurarsi che il server Web sia avviato e configurato per ascoltare una porta TCP in base alla cartella contenente il file *index.html*. Per Python, è possibile avviare l'ascolto della porta mediante l'esecuzione di nel prompt dei comandi/terminale dalla cartella dell'app:
> 
> ```bash
> python -m http.server 8080
> ```
>  Aprire quindi il browser e digitare *http://localhost:8080* o *http://localhost:{port}* in cui *port* corrisponde alla porta che il server Web sta ascoltando. È necessario visualizzare i contenuti della pagina index.html con il pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph).

## <a name="test-your-application"></a>Testare l'applicazione

Dopo che il browser ha caricato *index.html*, fare clic sul pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph). Se è la prima volta, il browser reindirizza l'utente all'endpoint v2 di Microsoft Azure Active Directory, da cui viene chiesto di accedere.
 
![Schermata di esempio](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consenso
La prima volta che si accede all'applicazione, viene visualizzata una schermata di consenso, simile alla seguente, in cui è necessario accettare:

 ![Schermata di consenso](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Risultati previsti
Si dovrebbero visualizzare le informazioni del profilo utente restituite dalla risposta alla chiamata all'API Microsoft Graph.
 
 ![Risultati](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Sono anche visualizzate le informazioni di base sul token acquisite nelle caselle *Access Token* e *ID Token Claims*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede l'ambito `user.read` per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, per Microsoft Graph, l'ambito `Calendars.Read` è necessario per elencare i calendari dell'utente. Per poter accedere al calendario dell'utente nel contesto di un'applicazione, è necessario aggiungere l'autorizzazione delegata `Calendars.Read` alle informazioni di registrazione dell'applicazione e quindi aggiungere l'ambito `Calendars.Read` alla chiamata `acquireTokenSilent`. Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se per un'API back-end non è richiesto alcun ambito (non consigliabile), è possibile usare `clientId` come ambito nelle chiamate `acquireTokenSilent` e/o `acquireTokenRedirect`.

<!--end-collapse-->

