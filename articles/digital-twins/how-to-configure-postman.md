---
title: Come configurare Postman in Gemelli digitali di Azure | Microsoft Docs
description: Come configurare Postman per Gemelli digitali di Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: adgera
ms.openlocfilehash: 92ff8cb732c7c10c525d8a8ec76180cb435bd466
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975016"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Come configurare Postman per Gemelli digitali di Azure

Questo articolo descrive come configurare un'applicazione di Azure Active Directory (Azure AD) per usare il flusso di concessione implicita OAuth 2.0. Quindi, illustra come configurare il client REST Postman per effettuare richieste HTTP di token di connessione alle API di gestione.

## <a name="postman-summary"></a>Riepilogo di Postman

Iniziare a usare Gemelli digitali di Azure tramite uno strumento client REST, ad esempio [Postman](https://www.getpostman.com/), per preparare l'ambiente di test locale. Il client Postman consente di creare rapidamente richieste HTTP complesse. Scaricare la versione desktop del client Postman accedendo a [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) è uno strumento di test REST che inserisce le principali funzionalità di richiesta HTTP in un'utile GUI desktop e basata su plug-in. Tramite il client Postman, gli sviluppatori di soluzioni possono specificare il tipo di richiesta HTTP (POST, GET, UPDATE, PATCH e DELETE), l'endpoint dell'API da chiamare e l'uso di SSL. Postman supporta anche l'aggiunta di intestazioni della richiesta HTTP, parametri, dati del modulo e corpi.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurare Azure Active Directory per usare il flusso di concessione implicita OAuth 2.0

Configurare l'app di Azure AD per usare il flusso di concessione implicita OAuth 2.0.

1. Seguire i passaggi di [questo avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) per creare un'applicazione di Azure AD di tipo nativo. In alternativa è possibile riusare una registrazione di app nativa esistente.

1. In **Autorizzazioni necessarie** selezionare **Aggiungi** e immettere **Gemelli digitali di Azure** in **Aggiungi accesso all'API**. Se la ricerca non individua l'API, cercare invece **Azure Smart Spaces**. Quindi selezionare **Concedi autorizzazioni > Autorizzazioni delegate** e **Fine**.

    ![API di aggiunta in registrazioni di app di Azure AD](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Fare clic su **Manifesto** per aprire il manifesto dell'applicazione per l'app. Impostare *oauth2AllowImplicitFlow* su `true`.

      ![Flusso implicito di Azure AD][1]

1. Configurare un **URL di risposta** su [`https://www.getpostman.com/oauth2/callback`](https://www.getpostman.com/oauth2/callback).

      ![URL di risposta di Azure AD][2]

1. Copiare e conservare l'**ID applicazione** dell'app di Azure AD. Viene usato di seguito.

## <a name="configure-the-postman-client"></a>Configurare il client Postman

Successivamente, installare e configurare Postman per ottenere un token di Azure AD. In seguito, eseguire una richiesta HTTP autenticata a Gemelli digitali usando il token acquisito:

1. Passare a [www.getpostman.com]([https://www.getpostman.com/) per scaricare l'app.
1. Verificare che l'**URL di autorizzazione** sia corretto. Deve essere nel formato seguente:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | NOME  | Sostituire con | Esempio |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Il nome del tenant o dell'organizzazione | `microsoft` |

1. Selezionare la scheda **Authorization** (Autorizzazione), **OAuth 2.0** e quindi **Get New Access Token** (Ottieni nuovo token di accesso).

    | Campo  | Valore |
    |---------|---------|
    | Grant Type (Tipo di concessione) | `Implicit` |
    | Callback URL (URL callback) | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL (URL autorizzazione) | Usare l'**URL di autorizzazione** ottenuto al passaggio 2 precedente |
    | ID client | Usare l'**ID applicazione** per l'app di Azure AD creata o riconfigurata nella sezione precedente |
    | Scope | Lasciare vuoto |
    | Stato | Lasciare vuoto |
    | Client Authentication (Autenticazione client) | `Send as Basic Auth header` |

1. Il client ora dovrebbe essere simile al seguente:

   ![Esempio di client Postman][3]

1. Selezionare **Request Token** (Richiedi token).

    >[!NOTE]
    >Se viene visualizzato un messaggio di errore che indica che non è stato possibile completare il processo di OAuth 2, provare a eseguire queste operazioni:
    > * Chiudere Postman, riaprirlo e riprovare.
  
1. Scorrere verso il basso e selezionare **Use Token** (Usa token).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'autenticazione con le API di gestione, vedere [Eseguire l'autenticazione con le API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
