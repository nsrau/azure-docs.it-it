---
title: "Esercitazione: registrare un'applicazione per consentire l'iscrizione e l'accesso tramite Azure Active Directory B2C | Microsoft Docs"
description: Usare il portale di Azure per creare un tenant di Azure Active Directory B2C e registrare un'applicazione.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebfe4a2d8c6a5b1d5334034e406131fac81f0c19
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713899"
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>Esercitazione: registrare un'applicazione per consentire l'iscrizione e l'accesso tramite Azure Active Directory B2C

Questa esercitazione consente di creare un tenant di Microsoft Azure Active Directory (Azure AD) B2C e registrare un'applicazione in pochi minuti.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Registrare l'applicazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

Le funzionalità B2C non possono essere abilitate nei tenant esistenti. A questo scopo, è necessario creare un tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Congratulazioni, è stato creato un tenant di Azure Active Directory B2C. L'amministratore globale del tenant può aggiungere altri amministratori globali in base alle esigenze. Per passare al nuovo tenant, fare clic sul *collegamento per gestire il nuovo tenant*.

![Collegamento per gestire il nuovo tenant](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Se si prevede di usare un tenant B2C per un'app di produzione, vedere l'articolo sui [tenant B2C a livello di produzione e di anteprima](active-directory-b2c-reference-tenant-type.md). Quando si elimina un tenant B2C esistente e lo si crea di nuovo con lo stesso nome di dominio, si verificano alcuni problemi noti. È necessario creare un tenant B2C con un nome di dominio diverso.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Collegare il tenant alla sottoscrizione

È necessario collegare il tenant di Azure AD B2C alla sottoscrizione di Azure per abilitare tutte le funzionalità B2C e pagare i costi di utilizzo. Per altre informazioni, vedere [questo articolo](active-directory-b2c-how-to-enable-billing.md). Se si non collega il tenant di Azure AD B2C alla sottoscrizione di Azure, alcune funzionalità saranno bloccate e verrà visualizzato un messaggio di avviso ("Nessuna sottoscrizione collegata a questo tenant B2C oppure la sottoscrizione richiede attenzione") nelle impostazioni B2C. È importante eseguire questo passaggio prima dell'invio delle applicazioni alla produzione.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Per accedere al pannello, è anche possibile immettere `Azure AD B2C` in **Cerca risorse** nella parte superiore del portale. Nell'elenco dei risultati selezionare **Azure AD B2C** per accedere al pannello delle impostazioni B2C.

## <a name="register-your-application"></a>Registrare l'applicazione

Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, è necessario innanzi tutto registrarla con tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Azure Active Directory B2C: creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md).

Le applicazioni create nel portale di Azure devono essere gestite dalla stessa posizione. Se vengono modificate con PowerShell o un altro portale, le applicazioni Azure AD B2C non sono più supportate e non funzionano con Azure AD B2C. Per altri dettagli, vedere la sezione [App con errori](#faulted-apps). 

Questo articolo usa esempi utili per le operazioni preliminari con gli esempi. Per altre informazioni sugli esempi, vedere gli articoli successivi.

### <a name="navigate-to-b2c-settings"></a>Passare alle impostazioni di B2C

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>Scegliere i passaggi successivi in base al tipo di applicazione

* [Registrare un'applicazione Web](#register-a-web-app)
* [Registrare un'API Web](#register-a-web-api).
* [Registrare un'applicazione per dispositivi mobili o nativa](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registrare un'app Web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Creare un segreto client per l'app Web

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

* **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio (com.onmicrosoft.contoso.appname://redirect/path), come schema viene usato com.onmicrosoft.contoso.appname. È consigliabile seguire questo modello. Se due applicazioni condividono lo stesso schema, viene visualizzata una finestra di dialogo di selezione dell'app. Se la scelta dell'utente non è corretta, non è possibile accedere.
* **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio, ad esempio, //contoso/ funziona e //contoso ha esito negativo.

Verificare che nell'URI di reindirizzamento non siano presenti caratteri speciali come caratteri di sottolineatura.

### <a name="faulted-apps"></a>App con errori

Le applicazioni B2C NON devono essere modificate:

* In altri portali di gestione delle applicazioni, come il [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/).
* Usando l'API Graph o PowerShell

Se si modifica l'applicazione Azure AD B2C come descritto e quindi si prova a modificarla di nuovo usando le funzionalità di Azure AD B2C nel portale di Azure, l'app viene danneggiata e non può più essere usata con Azure AD B2C. È necessario eliminare l'applicazione e crearla di nuovo.

Per eliminare l'app, passare al [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/) ed eliminarla nel portale. Per rendere l'applicazione visibile, è necessario essere il proprietario dell'applicazione (e non solo un amministratore del tenant).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Registrare l'applicazione

> [!div class="nextstepaction"]
> [Abilitare un'applicazione Web per l'autenticazione mediante account](active-directory-b2c-tutorials-web-app.md)