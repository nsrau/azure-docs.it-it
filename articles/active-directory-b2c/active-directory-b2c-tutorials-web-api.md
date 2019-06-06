---
title: "Esercitazione: Concedere l'accesso a un'API Web ASP.NET - Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web ASP.NET e chiamarla da un'applicazione Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6b93a7848e5c8516507c825d3064fb61a404e3cf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507757"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Esercitazione: Concedere l'accesso a un'API Web ASP.NET con Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web protetta in Azure Active Directory (Azure AD) B2C da un'applicazione Web ASP.NET.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione in un'applicazione Web con Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
6. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
7. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:44332`.
8. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
9. Fare clic su **Create**(Crea).
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. In questa esercitazione vengono usati gli ambiti per definire autorizzazioni di lettura e scrittura per l'API Web.

1. Selezionare **Applicazioni** e quindi *webapi1*.
2. Selezionare **Ambiti pubblicati**.
3. Immettere `Hello.Read` come **ambito** e `Read access to hello` come descrizione.
4. Immettere `Hello.Write` come **ambito** e `Write access to hello` come descrizione.
5. Fare clic su **Save**.

Gli ambiti pubblicati possono essere usati per concedere a un'applicazione client l'autorizzazione per l'API Web.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Nell'esercitazione preliminare è stata creata un'applicazione Web denominata *webapp1* in Azure AD B2C. Questa applicazione client verrà usata per chiamare l'API Web.

1. Selezionare **Applicazioni** e quindi *webapp1*.
2. Selezionare **Accesso all'API** e quindi **Aggiungi**.
3. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti **Hello.Read** e **Hello.Write** definiti in precedenza.
5. Fare clic su **OK**.

L'applicazione verrà registrata per la chiamata dell'API Web protetta. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione. L'applicazione ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-sample"></a>Configurare l'esempio

Dopo la registrazione dell'API Web e la definizione degli ambiti, si configura l'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'API Web di esempio. L'API Web di esempio è inclusa nel progetto scaricato nell'esercitazione preliminare.

La soluzione di esempio contiene due progetti:

I due progetti inclusi nella soluzione di esempio sono i seguenti:

- **TaskWebApp** crea e modifica un elenco attività. L'esempio usa il flusso utente di **iscrizione o accesso** per l'iscrizione o l'accesso degli utenti.
- **TaskService** supporta le funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco attività. L'API è protetta da Azure AD B2C e viene chiamata da TaskWebApp.

### <a name="configure-the-web-application"></a>Configurare l'applicazione Web

1. Aprire la soluzione **B2C-WebAPI-DotNet** in Visual Studio.
2. Aprire **Web.config** nel progetto **TaskWebApp**.
3. Per eseguire l'API in locale, usare l'impostazione localhost per **api:TaskServiceUrl**. Modificare il file Web.config come segue: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configurare l'URI dell'API. È l'URI usato dall'applicazione Web per effettuare la richiesta API. Configurare anche le autorizzazioni richieste.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Aprire **Web.config** nel progetto **TaskService**.
2. Configurare l'API per l'uso del tenant.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Per l'ID client usare l'ID dell'applicazione registrata per l'API.

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Aggiornare l'impostazione del flusso utente con il nome del flusso utente di iscrizione e accesso.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Configurare l'impostazione degli ambiti in modo che corrisponda a quella creata nel portale.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

È necessario eseguire entrambi i progetti, ovvero **TaskWebApp** e **TaskService**. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio...** . 
2. Selezionare **Progetti di avvio multipli**.
3. Modificare il valore di **Azione** in entrambi i progetti in **Avvia**.
4. Fare clic su **OK** per salvare la configurazione.
5. Premere **F5** per eseguire entrambe le applicazioni. Ogni applicazione viene aperta in una scheda del browser separata. `https://localhost:44316/` è l'applicazione Web.
    `https://localhost:44332/` è l'API Web.

6. Nell'applicazione Web fare clic su **sign-up / sign-in** per accedere all'applicazione Web. Usare l'account creato in precedenza. 
7. Dopo l'accesso fare clic su **To-do list** e creare un elemento elenco attività.

Quando si crea un elemento elenco attività, l'applicazione Web invia una richiesta all'API Web per la generazione dell'elemento. L'applicazione Web protetta chiama l'API Web protetta nel tenant di Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](tutorial-add-identity-providers.md)
