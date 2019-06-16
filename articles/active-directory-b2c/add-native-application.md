---
title: Aggiungere un'applicazione client nativa - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come aggiungere un'applicazione client nativa nel tenant di Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4e9b95cb226aeec686816d0ed7160062e110c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511829"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Aggiungere un'applicazione client nativa nel tenant di Active Directory B2C

Le risorse client native devono essere registrate nel tenant prima che l'applicazione possa comunicare con Azure Active Directory B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
2. Immettere un nome per l'applicazione. Ad esempio, *nativeapp1*.
3. Per **Includi app Web/API Web**, selezionare **No**.
4. Per **Includi client nativo**, selezionare **Sì**.
5. Per **URI di reindirizzamento**, immettere un URI di reindirizzamento valido con uno schema personalizzato. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:

    - **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se la scelta dell'utente non è corretta, il tentativo di accesso ha esito negativo.
    - **Completezza**: l'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//contoso/` funziona e `//contoso` ha esito negativo. Verificare che l'URI di reindirizzamento non includa caratteri speciali, come quello di sottolineatura.

6. Fare clic su **Create**(Crea).
7. Nella pagina delle proprietà prendere nota dell'ID applicazione che verrà usato durante la configurazione dell'applicazione client nativa.
