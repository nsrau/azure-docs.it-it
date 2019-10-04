---
title: "Esercitazione: Abilitare l'autenticazione in un'applicazione Web - Azure Active Directory B2C"
description: Esercitazione su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42634aa86f210382adb1ae224c847a92d89109b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103316"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione Web con Azure Active Directory B2C

Questa esercitazione illustra come usare Active Directory B2C (Azure AD B2C) per l'iscrizione e l'accesso degli utenti in un'applicazione Web ASP.NET. Azure AD B2C consente alle applicazioni di eseguire l'autenticazione per account di social network, aziendali e di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare l'applicazione in Azure AD B2C
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Creare flussi utente](tutorial-create-user-flows.md) per abilitare le esperienze utente nell'applicazione.
* Installare [Visual Studio 2019](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.

## <a name="update-the-application"></a>Aggiornare l'applicazione

Nell'esercitazione completata come parte dei prerequisiti è stato aggiunta un'applicazione Web in Azure AD B2C. Per consentire la comunicazione con l'esempio in questa esercitazione, è necessario aggiungere un URI di reindirizzamento all'applicazione in Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi l'applicazione *webapp1*.
1. In **URL di risposta** aggiungere `https://localhost:44316`.
1. Selezionare **Salva**.
1. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.
1. Selezionare **Chiavi**, **Genera chiave** e quindi **Salva**. Prendere nota della chiave, che verrà usata durante la configurazione dell'applicazione Web.

## <a name="configure-the-sample"></a>Configurare l'esempio

In questa esercitazione si configura un esempio che è possibile scaricare da GitHub. L'esempio usa ASP.NET per offrire un semplice elenco attività e [componenti middleware Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clonare l'esempio da GitHub. Assicurarsi di estrarre il file di esempio in una cartella il cui percorso sia costituito da un numero totale di caratteri minore di 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

I due progetti inclusi nella soluzione di esempio sono i seguenti:

* **TaskWebApp** crea e modifica un elenco attività. L'esempio usa il flusso utente di **iscrizione o accesso** per l'iscrizione e l'accesso degli utenti.
* **TaskService** supporta le funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco attività. L'API è protetta da Azure AD B2C e viene chiamata da TaskWebApp.

Modificare l'esempio per usare l'applicazione registrata nel tenant in uso, con l'ID applicazione e la chiave annotati in precedenza. Configurare anche i flussi utente creati. L'esempio definisce i valori di configurazione come impostazioni nel file *Web.config*.

Aggiornare le impostazioni nel file Web.config per garantire il funzionamento con il flusso utente:

1. Aprire la soluzione **B2C-WebAPI-DotNet** in Visual Studio.
1. Nel progetto **TaskWebApp** aprire il file **Web.config**.
    1. Aggiornare il valore di `ida:Tenant` e `ida:AadInstance` con il nome del tenant di Azure AD B2C creato. Ad esempio, sostituire `fabrikamb2c` con `contoso`.
    1. Sostituire il valore di `ida:ClientId` con l'ID applicazione registrato.
    1. Sostituire il valore di `ida:ClientSecret` con la chiave registrata. È necessario codificare in XML il segreto client prima di aggiungerlo al file Web.config.
    1. Sostituire il valore di `ida:SignUpSignInPolicyId` con `b2c_1_signupsignin1`.
    1. Sostituire il valore di `ida:EditProfilePolicyId` con `b2c_1_profileediting1`.
    1. Sostituire il valore di `ida:ResetPasswordPolicyId` con `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Eseguire l'esempio

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TaskWebApp** e quindi scegliere **Imposta come progetto di avvio**.
1. Premere **F5**. Il browser predefinito si apre con l'indirizzo del sito Web locale `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Selezionare **Sign up / Sign in** per iscriversi come utente dell'applicazione. Verrà usato il flusso utente **b2c_1_signupsignin1**.
1. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Dato che non si ha ancora un account, selezionare **Iscriversi adesso**. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.
1. Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti.

    ![Pagina di iscrizione visualizzata nel flusso di lavoro di accesso/iscrizione](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Selezionare **Crea** per creare un account locale nel tenant di Azure AD B2C.

L'utente dell'applicazione può ora usare il proprio indirizzo di posta elettronica per accedere all'applicazione Web e usarla.

La funzionalità **To-Do List** non sarà disponibile finché non si completa l'esercitazione successiva della serie, [Esercitazione: Usare Azure AD B2C per proteggere un'API Web ASP.NET](active-directory-b2c-tutorials-web-api.md).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiornare l'applicazione in Azure AD B2C
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

Passare ora all'esercitazione successiva per abilitare la funzionalità **To-Do List** dell'applicazione Web. Si registra un'applicazione API Web nel proprio tenant di Azure AD B2C e quindi si modifica l'esempio di codice per usare il tenant per l'autenticazione API.

> [!div class="nextstepaction"]
> [Esercitazione: Usare Azure Active Directory B2C per proteggere un'API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)
