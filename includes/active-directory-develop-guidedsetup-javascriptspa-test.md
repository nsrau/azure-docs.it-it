---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 0c4908315bd7c0e4d83c7aba735cc4d102216a28
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988434"
---
## <a name="test-your-code"></a>Testare il codice

### <a name="test-with-node"></a>Eseguire il test con Node

Se non si usa Visual Studio, assicurarsi che il server Web sia stato avviato.

1. Configurare il server per rimanere in ascolto di una porta TCP basata sulla posizione del file **index.html**. Per Node, avviare il server Web per essere in ascolto della porta eseguendo i comandi seguenti in un prompt della riga di comando dalla cartella dell'applicazione:

    ```bash
    npm install
    node server.js
    ```
1. Aprire il browser e digitare http://<span></span>localhost:30662 o http://<span></span>localhost:{porta} dove **porta** è la porta che il server Web sta ascoltando. Dovrebbero essere visualizzati i contenuti del file index.html e il pulsante **Accedi**.

<p/><!-- -->

### <a name="test-with-visual-studio"></a>Eseguire test con Visual Studio

Se si usa Visual Studio, assicurarsi di selezionare la soluzione di progetto e premere **F5** per eseguire il progetto. Il browser viene aperto sulla posizione http://<span></span>localhost:{porta} e viene visualizzato il pulsante **Accedi**.

## <a name="test-your-application"></a>Testare l'applicazione

Dopo che il browser ha caricato il file index.html, fare clic su **Accedi**. Verrà richiesto di accedere con l'endpoint Microsoft Azure Active Directory (Azure AD) v2.0:

![Accedere all'account JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione

Al primo accesso all'applicazione viene richiesto di specificare il consenso per permettere all'applicazione di accedere al profilo e di completare l'accesso per l'utente:

![Specificare il consenso per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione

Dopo l'accesso, le informazioni del profilo utente restituite nella risposta dell'API Microsoft Graph dovrebbero essere visualizzate nella pagina.

![Risultati previsti dalla chiamata API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede l'ambito `user.read` per leggere il profilo di un utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph, ad esempio, richiede l'ambito `Calendars.Read` per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata `Calendars.Read` alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito `Calendars.Read` alla chiamata ad `acquireTokenSilent`.

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se per un'API back-end non è richiesto alcun ambito (non consigliabile), è possibile usare `clientId` come ambito nelle chiamate per acquisire i token.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]