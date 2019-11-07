---
title: Aggiungere un'applicazione API Web-Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come aggiungere un'applicazione API Web al tenant di Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 37cb242f667190fcd29bed1b7a82ca44ba2c94e9
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641547"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C

 Registrare le risorse dell'API Web nel tenant in modo che possano accettare e rispondere alle richieste da applicazioni client che presentano un token di accesso. Questo articolo illustra come registrare un'API Web in Azure Active Directory B2C (Azure AD B2C).

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza di **applicazioni** corrente o la nuova esperienza di **registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
6. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
7. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. Nell'applicazione di produzione, è possibile impostare l'URL di risposta su un valore come `https://localhost:44332`. A scopo di test, impostare l'URL di risposta su `https://jwt.ms`.
8. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio, `https://contosotenant.onmicrosoft.com/api`.
9. Fare clic su **Create**.
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **registrazioni app (anteprima)** , quindi selezionare **nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *webapi1*.
1. In **URI di reindirizzamento**selezionare **Web**, quindi immettere un endpoint in cui Azure ad B2C deve restituire i token richiesti dall'applicazione. In un'applicazione di produzione, è possibile impostare l'URI di reindirizzamento come `https://localhost:5000`un endpoint. Durante lo sviluppo o il test, è possibile impostarlo su `https://jwt.ms`, un'applicazione Web di proprietà di Microsoft che Visualizza il contenuto decodificato di un token (il contenuto del token non lascia mai il browser). È possibile aggiungere e modificare gli URI di reindirizzamento nelle applicazioni registrate in qualsiasi momento.
1. Selezionare **Registra**.
1. Registrare l' **ID dell'applicazione (client)** da usare nel codice dell'API Web.

Se si dispone di un'applicazione che implementa il flusso di concessione implicita, ad esempio un'applicazione a pagina singola basata su JavaScript, è possibile abilitare il flusso attenendosi alla procedura seguente:

1. In **Gestisci**selezionare **autenticazione**.
1. Selezionare **prova la nuova esperienza** (se visualizzata).
1. In **concessione implicita**selezionare le caselle di controllo **token di accesso** e **token ID** .
1. Selezionare **Salva**.

* * *

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. In questa esercitazione vengono usati gli ambiti per definire autorizzazioni di lettura e scrittura per l'API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Ad esempio, in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](tutorial-register-applications.md), un'applicazione Web denominata *app Web 1* viene registrata nel Azure ad B2C. Questa applicazione può essere usata per chiamare l'API Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

L'applicazione verrà registrata per la chiamata dell'API Web protetta. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione. L'applicazione ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.
