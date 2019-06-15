---
title: Aggiungere un'applicazione Web - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come aggiungere un'applicazione Web nel tenant di Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1d098550c6fb733e088f8ad211d29f48f55d2d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511712"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C

 Registrare le risorse API Web nel tenant in modo che possano accettare e rispondere alle richieste dalle applicazioni client che presentano un token di accesso. Questo articolo illustra come registrare l'applicazione in Azure Active Directory (Azure AD) B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi che si usa la directory che contiene il tenant di Azure AD B2C. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
6. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
7. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. Nell'applicazione di produzione, è possibile impostare l'URL di risposta su un valore, ad esempio `https://localhost:44332`. A scopo di test, impostare l'URL di risposta `https://jwt.ms`.
8. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
9. Fare clic su **Create**(Crea).
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. In questa esercitazione vengono usati gli ambiti per definire autorizzazioni di lettura e scrittura per l'API Web.

1. Selezionare **Applicazioni** e quindi *webapi1*.
2. Selezionare **Ambiti pubblicati**.
3. Immettere `Read` come **ambito** e `Read access to the application` come descrizione.
4. Immettere `Write` come **ambito** e `Write access to the application` come descrizione.
5. Fare clic su **Save**.

Gli ambiti pubblicati possono essere usati per concedere a un'applicazione client l'autorizzazione per l'API Web.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Ad esempio, in [Esercitazione: Registrare un'applicazione in Azure Active Directory B2C](tutorial-register-applications.md), viene creata un'applicazione Web in Azure AD B2C denominata *webapp1*. Questa applicazione può essere usata per chiamare l'API Web.

1. Selezionare **Applicazioni** e quindi l'applicazione Web.
2. Selezionare **Accesso all'API** e quindi **Aggiungi**.
3. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
4. Nell'elenco a discesa **Selezionare gli ambiti** selezionare gli ambiti **Lettura** e **Scrittura** definiti in precedenza.
5. Fare clic su **OK**.

L'applicazione verrà registrata per la chiamata dell'API Web protetta. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione. L'applicazione ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.
