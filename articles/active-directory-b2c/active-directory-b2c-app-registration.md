---
title: Registrare un'applicazione in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come registrare l'applicazione con Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8068c4a8f38cd33a1a0547f5db5079bc75c76ec1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013412"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Registrare un'applicazione in Azure Active Directory B2C

Per compilare un'[applicazione](active-directory-b2c-apps.md) che accetta l'iscrizione e l'accesso dell'utente, è necessario prima di tutto registrarla con un tenant di Azure AD B2C. Questo articolo consente di registrare un'applicazione in un tenant di Azure Active Directory (Azure AD) B2C in pochi minuti. Al termine, l'applicazione viene registrata per l'uso nel tenant di Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

Per ottenere il tenant, seguire la procedura illustrata in [Creare un tenant di Azure Active Directory B2C](tutorial-create-tenant.md).

Scegliere i passaggi successivi in base al tipo di applicazione:

- [Registrare un'applicazione Web](#register-a-web-application)
- [Registrare un'API Web](#register-a-web-api).
- [Registrare un'applicazione per dispositivi mobili o nativa](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Registrare un'applicazione Web

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Applicazioni** e quindi **Aggiungi**.
4. Immettere un nome per l'applicazione. Ad esempio, *testapp1*.
5. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
6. Per **URL di risposta** immettere l'endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'app. È ad esempio possibile impostarlo per essere in ascolto localmente di `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.
7. Fare clic su **Create**(Crea).

### <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione chiama un'API Web protetta da Azure AD B2C, è necessario creare un segreto dell'applicazione.

1. Selezionare **Chiavi** e quindi fare clic su **Genera chiave**. 
2. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.
3. Selezionare **Accesso all'API**, fare clic su **Aggiungi** e selezionare l'API Web e gli ambiti (autorizzazioni).

## <a name="register-a-web-api"></a>Registrare un'API Web

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Applicazioni** e quindi **Aggiungi**.
4. Immettere un nome per l'applicazione. Ad esempio, *testapp2*.
5. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
6. Per **URL di risposta** immettere l'endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'app. È ad esempio possibile impostarlo per essere in ascolto localmente di `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.
7. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
8. Fare clic su **Create**(Crea).
9. Selezionare **Ambiti pubblicati** per aggiungere altri ambiti in base alla necessità. Per impostazione predefinita, è definito l'ambito `user_impersonation`. L'ambito `user_impersonation` consente ad altre applicazioni di accedere a questa API per conto dell'utente connesso. Se necessario, l'ambito `user_impersonation` può essere rimosso.

## <a name="register-a-mobile-or-native-application"></a>Registrare un'applicazione per dispositivi mobili o nativa

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Applicazioni** e quindi **Aggiungi**.
4. Immettere un nome per l'applicazione. Ad esempio, *testapp3*.
5. Per **Includi app Web/API Web**, selezionare **No**.
6. Per **Includi client nativo**, selezionare **Sì**.
7. Per **URI di reindirizzamento** immettere un [URI di reindirizzamento con uno schema personalizzato](active-directory-b2c-apps.md). Assicurarsi di scegliere un URI di reindirizzamento valido e di non includere caratteri speciali come i caratteri di sottolineatura.
8. Fare clic su **Create**(Crea).

### <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione chiama un'API Web protetta da Azure AD B2C, è necessario creare un segreto dell'applicazione.

1. Selezionare **Chiavi** e quindi fare clic su **Genera chiave**. 
2. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.
3. Selezionare **Accesso all'API**, fare clic su **Aggiungi** e selezionare l'API Web e gli ambiti (autorizzazioni).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle [esercitazioni introduttive](active-directory-b2c-overview.md) per essere subito operativi.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET con iscrizione, accesso e reimpostazione della password](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
