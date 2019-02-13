---
title: Aggiungere un'applicazione Web - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come aggiungere un'applicazione Web nel tenant di Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: c20f455a0a325dadd3eeeb77dea7026de4834c56
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757356"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C

Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
6. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
7. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:44332`.
8. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
9. Fare clic su **Crea**.
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. In questa esercitazione vengono usati gli ambiti per definire autorizzazioni di lettura e scrittura per l'API Web.

1. Selezionare **Applicazioni** e quindi *webapi1*.
2. Selezionare **Ambiti pubblicati**.
3. Immettere `Hello.Read` come **ambito** e `Read access to hello` come descrizione.
4. Immettere `Hello.Write` come **ambito** e `Write access to hello` come descrizione.
5. Fare clic su **Salva**.

Gli ambiti pubblicati possono essere usati per concedere a un'applicazione client l'autorizzazione per l'API Web.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare da un'applicazione un'API Web protetta, è necessario concedere all'applicazione le autorizzazioni per l'API. Nell'esercitazione preliminare è stata creata in Azure AD B2C un'applicazione Web denominata *webapp1*. Questa applicazione client verrà usata per chiamare l'API Web.

1. Selezionare **Applicazioni** e quindi l'applicazione Web.
2. Selezionare **Accesso all'API** e quindi **Aggiungi**.
3. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti **Hello.Read** e **Hello.Write** definiti in precedenza.
5. Fare clic su **OK**.

L'applicazione verrà registrata per la chiamata dell'API Web protetta. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione. L'applicazione ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.