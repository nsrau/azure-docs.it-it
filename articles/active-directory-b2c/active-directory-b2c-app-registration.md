---
title: 'Azure Active Directory B2C: registrazione dell''applicazione | Documentazione Microsoft'
description: Come registrare l'applicazione con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrare l'applicazione

> [!IMPORTANT]
> Le applicazioni create dal pannello Azure AD B2C nel portale di Azure devono essere gestite dalla stessa posizione. Le applicazioni B2C, se vengono modificate usando PowerShell o un altro portale, non sono più supportate e non funzioneranno con Azure AD B2C. Altre informazioni sono riportate [di seguito](#faulted-apps).
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
1. Fare clic su **+Aggiungi** nella parte superiore del pannello.
1. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "App Contoso B2C".
1. Attivare/Disattivare l'opzione **Includi app Web/API Web** impostandola su **Sì**.
1. Immettere un valore [appropriato](#limitations) per gli **URL di risposta**, che sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44316/`.
1. Fare clic su **Crea** per registrare l'applicazione.
1. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.
1. Se l'applicazione Web chiamerà anche un'API Web protetta da Azure AD B2C, eseguire queste operazioni:
    1. Creare un **Segreto dell'applicazione** passando al pannello **Chiavi** e facendo clic sul pulsante **Genera chiave**.
    1. Fare clic su **Accesso all'API**, quindi su **Aggiungi** e infine selezionare l'API Web e gli ambiti (autorizzazioni).

> [!NOTE]
> Un **segreto dell'applicazione** è una credenziale di sicurezza importante e deve essere protetto in modo appropriato.
>

## <a name="register-a-web-api"></a>Registrare un'API Web

1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
1. Fare clic su **+Aggiungi** nella parte superiore del pannello.
1. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "API Contoso B2C".
1. Attivare/Disattivare l'opzione **Includi app Web/API Web** impostandola su **Sì**.
1. Immettere un valore [appropriato](#choosing-a-web-app/api-reply-url) per gli **URL di risposta**, che sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44316/`.
1. Immettere un **URI ID app**. Questo è l'identificatore usato per l'API Web. Ad esempio, immettere 'notes'. Verrà generato l'URI dell'identificatore completo seguente.
1. Fare clic su **Crea** per registrare l'applicazione.
1. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.
1. Fare clic su **Ambiti pubblicati**. È possibile definire qui le autorizzazioni (ambiti) che possono essere concesse ad altre applicazioni.
1. Aggiungere altri ambiti, in base alla necessità. Per impostazione predefinita, verrà definito l'ambito "user_impersonation", che consente ad altre applicazioni di accedere a questa API per conto dell'utente connesso. Se necessario, è possibile rimuovere questa opzione.
1. Fare clic su **Save**.

## <a name="register-a-mobilenative-application"></a>Registrare un'applicazione per dispositivi mobili/nativa

1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
1. Fare clic su **+Aggiungi** nella parte superiore del pannello.
1. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "App Contoso B2C".
1. Attivare/Disattivare l'opzione **Includi client nativo** impostandola su **Sì**.
1. Immettere un **URI di reindirizzamento** con uno schema personalizzato. Ad esempio, com.onmicrosoft.contoso.appname://redirect/path. Assicurarsi di scegliere un [URI di reindirizzamento valido](#choosing-a-native-application-redirect-uri) e di non includere caratteri speciali come i caratteri di sottolineatura.
1. Fare clic su **Salva** per registrare l'applicazione.
1. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.
1. Se l'applicazione nativa chiamerà anche un'API Web protetta da Azure AD B2C, eseguire queste operazioni:
    1. Creare un **Segreto dell'applicazione** passando al pannello **Chiavi** e facendo clic sul pulsante **Genera chiave**.
    1. Fare clic su **Accesso all'API**, quindi su **Aggiungi** e infine selezionare l'API Web e gli ambiti (autorizzazioni).

> [!NOTE]
> Un **segreto dell'applicazione** è una credenziale di sicurezza importante e deve essere protetto in modo appropriato.
>

## <a name="limitations"></a>Limitazioni

### <a name="choosing-a-web-appapi-reply-url"></a>Scelta di un URL di risposta per un'API/app Web

Attualmente, le app registrate con Azure AD B2C sono limitate a un set specifico di valori di URL di risposta. Gli URL di risposta per le app e i servizi Web devono iniziare con lo schema `https` e tutti i valori degli URL di risposta devono condividere un singolo dominio DNS. Non è possibile ad esempio registrare un'app Web con uno degli URL di risposta seguenti:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Il sistema di registrazione confronta l'intero nome DNS dell'URL di risposta esistente con il nome DNS dell'URL di risposta che si sta aggiungendo. La richiesta di aggiungere il nome DNS non viene soddisfatta se si verificano le condizioni seguenti:

* Il nome DNS intero del nuovo URL di risposta non corrisponde al nome DNS dell'URL di risposta esistente.
* Il nome DNS intero del nuovo URL di risposta non è un sottodominio secondario dell'URL di risposta esistente.

Se ad esempio l'applicazione ha questo URL di risposta:

`https://login.contoso.com`

è possibile eseguire un'aggiunta analoga alla seguente:

`https://login.contoso.com/new`

In questo caso, il nome DNS corrisponde esattamente. In alternativa, è possibile eseguire un'aggiunta analoga alla seguente:

`https://new.login.contoso.com`

In questo caso, si fa riferimento a un sottodominio DNS di login.contoso.com. Se si desidera un'app con URL di risposta login-east.contoso.com e login-west.contoso.com, è necessario aggiungere tali URL nell'ordine seguente:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Gli ultimi due URL possono essere aggiunti perché si tratta di sottodomini del primo URL di risposta, ovvero contoso.com.

### <a name="choosing-a-native-application-redirect-uri"></a>Scelta di un URI di reindirizzamento per un'applicazione nativa

Quando si sceglie un URI di reindirizzamento per applicazioni per dispositivi mobili/native, occorre tenere presenti due considerazioni importanti:

* **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nel nostro esempio (com.onmicrosoft.contoso.appname://redirect/path) viene usato com.onmicrosoft.contoso.appname come schema. È consigliabile seguire questo modello. Se due applicazioni condividono lo stesso schema, verrà visualizzata una finestra di dialogo di selezione dell'app. Se la scelta dell'utente non è corretta, non sarà possibile accedere.
* **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio, ad esempio, //contoso/ funzionerà e //contoso avrà esito negativo.

Verificare che nell'URI di reindirizzamento non siano presenti caratteri speciali come caratteri di sottolineatura.

### <a name="faulted-apps"></a>App con errori

Le applicazioni B2C NON devono essere modificate:

* In altri portali di gestione delle applicazioni, tra cui il [portale di Azure classico](https://manage.windowsazure.com/) o il [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/).
* Usando l'API Graph o PowerShell

Se si modifica l'applicazione B2C in uno di questi modi e si prova a modificarla di nuovo nel pannello delle funzionalità di Azure AD B2C nel portale di Azure, l'app si danneggia e non può più essere usata con Azure AD B2C. A questo punto, sarà necessario eliminare l'applicazione e crearla di nuovo.

Per eliminare l'app, passare al [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/) ed eliminarla nel portale. Per rendere l'applicazione visibile, è necessario essere il proprietario dell'applicazione (e non solo un amministratore del tenant).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle [esercitazioni di avvio rapido](active-directory-b2c-overview.md#get-started) per essere subito operativi.

