---
title: 'Azure Active Directory B2C: registrazione dell&quot;applicazione | Documentazione Microsoft'
description: Come registrare l&quot;applicazione con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 541849501335fb25d96cffa81b8119adc158cdd7
ms.lasthandoff: 03/14/2017


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrare l'applicazione

> [!IMPORTANT]
> Le applicazioni create dal pannello Azure AD B2C nel portale di Azure devono essere gestite dalla stessa posizione. Le applicazioni B2C, se vengono modificate usando PowerShell o un altro portale, non sono più supportate ed è probabile che non funzionino con Azure AD B2C.
> 
> 

## <a name="prerequisite"></a>Prerequisito
Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, è necessario innanzi tutto registrarla con tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Azure Active Directory B2C: creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md). Dopo avere eseguito tutti i passaggi nell'articolo, si disporrà del pannello delle funzionalità B2C aggiunto alla schermata iniziale.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Passare al pannello delle funzionalità B2C
Se il pannello delle funzionalità B2C è stato aggiunto a Schermata iniziale, verrà visualizzato appena si accede al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant B2C.

È possibile accedere al pannello anche facendo clic su **Altri servizi** e quindi cercare **Azure AD B2C** nel riquadro di spostamento a sinistra del [portale di Azure](https://portal.azure.com/).

> [!IMPORTANT]
> È necessario avere diritti di amministratore globale del tenant B2C per poter accedere al pannello delle funzionalità B2C. Un amministratore globale o un utente di qualsiasi altro tenant non può accedere.  È possibile passare al tenant B2C usando l'apposito controllo nell'angolo superiore destro del portale di Azure.
> 
> 

## <a name="register-a-web-application"></a>Registrare un'applicazione Web
1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "App Contoso B2C".
4. Attivare/Disattivare l'opzione **Includi app Web/API Web** impostandola su **Sì**. Gli **URL di risposta** sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44316/`.
5. Fare clic su **Salva** per registrare l'applicazione.
6. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.


## <a name="register-a-web-api"></a>Registrare un'API Web
1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "API Contoso B2C".
4. Attivare/Disattivare l'opzione **Includi app Web/API Web** impostandola su **Sì**. Gli **URL di risposta** sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44316/`.
5. Fare clic su **Salva** per registrare l'applicazione.
6. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.


## <a name="register-a-mobilenative-application"></a>Registrare un'applicazione per dispositivi mobili/nativa
1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "App Contoso B2C".
4. Attivare/Disattivare l'opzione **Includi client nativo** impostandola su **Sì**.
5. Immettere un **URI di reindirizzamento** con uno schema personalizzato. Ad esempio, com.onmicrosoft.contoso.appname://redirect/path. Assicurarsi di scegliere un [URI di reindirizzamento valido](#choosing-a-redirect-uri).
6. Fare clic su **Salva** per registrare l'applicazione.
7. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.

### <a name="choosing-a-redirect-uri"></a>Scelta di un URI di reindirizzamento
Quando si sceglie un URI di reindirizzamento per applicazioni per dispositivi mobili/native, occorre tenere presenti due considerazioni importanti: 
* **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nel nostro esempio (com.onmicrosoft.contoso.appname://redirect/path) viene usato com.onmicrosoft.contoso.appname come schema. È consigliabile seguire questo modello. Se due applicazioni condividono lo stesso schema, verrà visualizzata una finestra di dialogo di selezione dell'app. Se la scelta dell'utente non è corretta, non sarà possibile accedere. 
* **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio, ad esempio, //contoso/ funzionerà e //contoso avrà esito negativo. 

## <a name="build-a-quick-start-application"></a>Creare un'applicazione di avvio rapido
Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle esercitazioni di avvio rapido per essere subito operativi. Di seguito sono elencati alcuni suggerimenti:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


