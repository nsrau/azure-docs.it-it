---
title: 'Azure Active Directory B2C: registrazione dell''applicazione | Documentazione Microsoft'
description: Come registrare l'applicazione con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: PatAltimore
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 0e91d249116d39675d6e4bdf429442b6bc8af391
ms.contentlocale: it-it
ms.lasthandoff: 09/01/2017

---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrare l'applicazione

Questa esercitazione introduttiva consente di registrare un'applicazione in un tenant di Microsoft Azure Active Directory (Azure AD) B2C in pochi minuti. Al termine, l'applicazione viene registrata per l'uso nel tenant di Azure B2C.

## <a name="prerequisites"></a>Prerequisiti

Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, è necessario innanzi tutto registrarla con tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Azure Active Directory B2C: creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md).

Le applicazioni create dal pannello Azure AD B2C nel portale di Azure devono essere gestite dalla stessa posizione. Le applicazioni B2C, se vengono modificate usando PowerShell o un altro portale, non sono più supportate e non funzionano con Azure AD B2C. Per altri dettagli, vedere la sezione [App con errori](#faulted-apps). 

Questo articolo usa esempi utili per le operazioni preliminari con gli esempi. Per altre informazioni sugli esempi, vedere gli articoli successivi.

## <a name="navigate-to-b2c-settings"></a>Passare alle impostazioni di B2C

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Scegliere i passaggi successivi in base al tipo di applicazione

* [Registrare un'applicazione Web](#register-a-web-app)
* [Registrare un'API Web](#register-a-web-api).
* [Registrare un'applicazione per dispositivi mobili o nativa](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registrare un'app Web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

Se l'applicazione Web chiama un'API Web protetta da Azure AD B2C, seguire questa procedura:
   1. Creare un segreto dell'applicazione passando al pannello **Chiavi** e facendo clic sul pulsante **Genera chiave**. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.
   2. Fare clic su **Accesso all'API**, quindi su **Aggiungi** e selezionare l'API Web e gli ambiti (autorizzazioni).

> [!NOTE]
> Un **segreto dell'applicazione** è una credenziale di sicurezza importante e deve essere protetto in modo appropriato.
> 

[Andare a **Passaggi successivi**](#next-steps)

### <a name="register-a-web-api"></a>Registrare un'API Web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Fare clic su **Ambiti pubblicati** per aggiungere altri ambiti in base alla necessità. Per impostazione predefinita, viene definito l'ambito "user_impersonation", che consente ad altre applicazioni di accedere a questa API per conto dell'utente connesso. Se necessario, è possibile rimuovere l'ambito user_impersonation.

[Andare a **Passaggi successivi**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registrare un'app per dispositivi mobili o nativa

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Andare a **Passaggi successivi**](#next-steps)

## <a name="limitations"></a>Limitazioni

### <a name="choosing-a-web-app-or-api-reply-url"></a>Scelta di un URL di risposta per un'API o un'app Web

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

### <a name="choosing-a-native-app-redirect-uri"></a>Scelta di un URI di reindirizzamento per un'app nativa

Quando si sceglie un URI di reindirizzamento per applicazioni per dispositivi mobili/native, occorre tenere presenti due considerazioni importanti:

* **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nel nostro esempio (com.onmicrosoft.contoso.appname://redirect/path) viene usato com.onmicrosoft.contoso.appname come schema. È consigliabile seguire questo modello. Se due applicazioni condividono lo stesso schema, viene visualizzata una finestra di dialogo di selezione dell'app. Se la scelta dell'utente non è corretta, non è possibile accedere.
* **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio, ad esempio, //contoso/ funziona e //contoso ha esito negativo.

Verificare che nell'URI di reindirizzamento non siano presenti caratteri speciali come caratteri di sottolineatura.

### <a name="faulted-apps"></a>App con errori

Le applicazioni B2C NON devono essere modificate:

* In altri portali di gestione delle applicazioni, tra cui il [portale di Azure classico](https://manage.windowsazure.com/) o il [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/).
* Usando l'API Graph o PowerShell

Se si modifica l'applicazione B2C in uno di questi modi e si prova a modificarla di nuovo nel pannello delle funzionalità di Azure AD B2C nel portale di Azure, l'app si danneggia e non può più essere usata con Azure AD B2C. A questo punto, è necessario eliminare l'applicazione e crearla di nuovo.

Per eliminare l'app, passare al [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/) ed eliminarla nel portale. Per rendere l'applicazione visibile, è necessario essere il proprietario dell'applicazione (e non solo un amministratore del tenant).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle [esercitazioni di avvio rapido](active-directory-b2c-overview.md#get-started) per essere subito operativi.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET con iscrizione, accesso e reimpostazione della password](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
