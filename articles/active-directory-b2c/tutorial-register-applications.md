---
title: 'Esercitazione: Registrare le applicazioni in Azure Active Directory B2C | Microsoft Docs'
description: Informazioni su come registrare le applicazioni in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248723"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Esercitazione: Registrare le applicazioni in Azure Active Directory B2C

Prima che le [applicazioni](active-directory-b2c-apps.md) possano interagire con Azure Active Directory (Azure AD) B2C, devono essere registrate in un tenant gestito. Questa esercitazione mostra come registrare le applicazioni usando il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Registrare un'API Web
> * Registrare un'applicazione per dispositivi mobili o nativa

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](tutorial-create-tenant.md), crearlo ora. È possibile usare un tenant esistente.

## <a name="register-a-web-application"></a>Registrare un'applicazione Web

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.

    ![Passare alla directory della sottoscrizione](./media/tutorial-register-applications/switch-directories.png)

2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Applicazioni** e quindi **Aggiungi**.

    ![Aggiunta di un'applicazione](./media/tutorial-register-applications/add-application.png)

4. Immettere un nome per l'applicazione. Ad esempio, *webapp1*.
5. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
6. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. È ad esempio possibile impostarlo per l'ascolto in locale su `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento. A scopo di test, è possibile impostare l'URL su `https://jwt.ms`, in modo da visualizzare il contenuto di un token per l'ispezione. Per questa esercitazione, impostarlo su `https://jwt.ms`. 

    L'URL di risposta deve iniziare con lo schema `https` e tutti i valori degli URL di risposta devono condividere un singolo dominio DNS. Se ad esempio l'applicazione ha un URL di risposta `https://login.contoso.com`, è possibile aggiungerne altri, come questo URL: `https://login.contoso.com/new`. In alternativa, è possibile fare riferimento a un sottodominio DNS di `login.contoso.com`, ad esempio `https://new.login.contoso.com`. Se si vuole avere un'applicazione con `login-east.contoso.com` e `login-west.contoso.com` come URL di risposta, è necessario aggiungere tali URL nell'ordine seguente: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Gli ultimi due URL possono essere aggiunti perché sono sottodomini del primo, ovvero `contoso.com`.

7. Fare clic su **Crea**.

    ![Impostare le proprietà dell'applicazione](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione scambia un codice con un token, è necessario creare un segreto dell'applicazione.

1. Selezionare **Chiavi** e quindi fare clic su **Genera chiave**.

    ![Generare chiavi](./media/tutorial-register-applications/generate-keys.png)

2. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.

    ![Salvare la chiave](./media/tutorial-register-applications/save-key.png)
    
3. Selezionare **Accesso all'API**, fare clic su **Aggiungi** e selezionare l'API Web e gli ambiti (autorizzazioni).

    ![Configurare l'accesso all'API](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Registrare un'API Web

1. Selezionare **Applicazioni** e quindi **Aggiungi**.
3. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
4. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
5. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. È ad esempio possibile impostarlo per essere in ascolto localmente di `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.
6. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
7. Fare clic su **Crea**.
8. Selezionare l'applicazione *webapi1* creata e quindi selezionare **Ambiti pubblicati** per aggiungere altri ambiti in base alle esigenze. Per impostazione predefinita, è definito l'ambito `user_impersonation`. L'ambito `user_impersonation` consente ad altre applicazioni di accedere a questa API per conto dell'utente connesso. Se necessario, l'ambito `user_impersonation` può essere rimosso.

    ![Impostare gli ambiti pubblicati](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Registrare un'applicazione per dispositivi mobili o nativa

1. Selezionare **Applicazioni** e quindi **Aggiungi**.
2. Immettere un nome per l'applicazione. Ad esempio, *nativeapp1*.
3. Per **Includi app Web/API Web**, selezionare **No**.
4. Per **Includi client nativo**, selezionare **Sì**.
5. Per **URI di reindirizzamento**, immettere un URI di reindirizzamento valido con uno schema personalizzato. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:

    - **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se la scelta dell'utente non è corretta, il tentativo di accesso ha esito negativo.
    - **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//contoso/` funziona e `//contoso` ha esito negativo. Verificare che l'URI di reindirizzamento non includa caratteri speciali, come quello di sottolineatura.

6. Fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Registrare un'API Web
> * Registrare un'applicazione per dispositivi mobili o nativa

> [!div class="nextstepaction"]
> [Creare flussi utente in Azure Active Directory B2C](tutorial-create-user-flows.md)