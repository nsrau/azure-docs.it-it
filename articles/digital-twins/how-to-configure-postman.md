---
title: Come configurare Postman in Gemelli digitali di Azure | Microsoft Docs
description: Come configurare Postman per Gemelli digitali di Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: v-adgera
ms.openlocfilehash: 797dfc44b9897920f9fd74346ee01e4b157a4ce8
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967790"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Come configurare Postman per Gemelli digitali di Azure

Questo articolo illustra come configurare il client REST di Postman per testare e interagire con le API di gestione di Gemelli digitali di Azure. In particolare, illustra le operazioni seguenti:

* Come configurare un'applicazione di Azure Active Directory per usare il flusso di concessione implicita OAuth 2.0.
* Come usare il client REST di Postman per inviare richieste HTTP di token alle API di gestione.
* Come usare Postman per inviare richieste POST multipart alle API di gestione.

## <a name="postman-summary"></a>Riepilogo di Postman

Iniziare a usare Gemelli digitali di Azure tramite uno strumento client REST, ad esempio [Postman](https://www.getpostman.com/), per preparare l'ambiente di test locale. Il client Postman consente di creare rapidamente richieste HTTP complesse. Scaricare la versione desktop del client Postman accedendo a [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) è uno strumento di test REST che inserisce le principali funzionalità di richiesta HTTP in un'utile GUI desktop e basata su plug-in. 

Tramite il client Postman gli sviluppatori di soluzioni possono specificare il tipo di richiesta HTTP (*POST*, *GET*, *UPDATE*, *PATCH* e *DELETE*), l'endpoint dell'API da chiamare e l'uso di SSL. Postman supporta anche l'aggiunta di intestazioni della richiesta HTTP, parametri, dati del modulo e corpi.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurare Azure Active Directory per usare il flusso di concessione implicita OAuth 2.0

Configurare l'app di Azure Active Directory per usare il flusso di concessione implicita OAuth 2.0.

1. Seguire i passaggi di [questo avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) per creare un'applicazione di Azure AD di tipo nativo. In alternativa è possibile riusare una registrazione di app nativa esistente.

1. In **Autorizzazioni necessarie** selezionare **Aggiungi** e immettere **Gemelli digitali di Azure** in **Aggiungi accesso all'API**. Se la ricerca non individua l'API, cercare invece **Azure Smart Spaces**. Quindi selezionare **Concedi autorizzazioni > Autorizzazioni delegate** e **Fine**.

    ![Aggiunta dell'API per la registrazione di app in Azure Active Directory](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Fare clic su **Manifesto** per aprire il manifesto dell'applicazione per l'app. Impostare *oauth2AllowImplicitFlow* su `true`.

      ![Flusso implicito di Azure Active Directory][1]

1. Configurare un **URL di risposta** su `https://www.getpostman.com/oauth2/callback`.

      ![URL di risposta di Azure Active Directory][2]

1. Copiare e conservare l'**ID applicazione** dell'app di Azure Active Directory. Viene usato nei passaggi seguenti.

## <a name="obtain-an-oauth-20-token"></a>Ottenere un token OAuth 2.0

Successivamente installare e configurare Postman per ottenere un token di Azure Active Directory. In seguito, eseguire una richiesta HTTP autenticata a Gemelli digitali usando il token acquisito:

1. Passare a [www.getpostman.com](https://www.getpostman.com/) per scaricare l'app.
1. Verificare che l'**URL di autorizzazione** sia corretto. Deve essere nel formato seguente:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | NOME  | Sostituire con | Esempio |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Il nome del tenant o dell'organizzazione | `microsoft` |

1. Selezionare la scheda **Authorization** (Autorizzazione), **OAuth 2.0** e quindi **Get New Access Token** (Ottieni nuovo token di accesso).

    | Campo  | Value |
    |---------|---------|
    | Tipo di concessione | `Implicit` |
    | Callback URL (URL callback) | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL (URL autorizzazione) | Usare l'**URL di autorizzazione** ottenuto al passaggio 2 |
    | ID client | Usare l'**ID applicazione** per l'app di Azure Active Directory creata o riconfigurata nella sezione precedente |
    | `Scope` | Lasciare vuoto |
    | Stato | Lasciare vuoto |
    | Autenticazione client | `Send as Basic Auth header` |

1. Il client ora dovrebbe essere visualizzato come:

   ![Esempio di client Postman][3]

1. Selezionare **Request Token** (Richiedi token).

    >[!TIP]
    >Se viene visualizzato un messaggio di errore che indica che non è stato possibile completare il processo di OAuth 2, provare a eseguire queste operazioni:
    > * Chiudere Postman, riaprirlo e riprovare.
  
1. Scorrere verso il basso e selezionare **Use Token** (Usa token).

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Effettuare una richiesta POST multipart

Dopo aver completato i passaggi precedenti, configurare Postman per eseguire una richiesta POST multipart HTTP autenticata:

1. Nella scheda **Intestazione** aggiungere una chiave dell'intestazione della richiesta HTTP **Content-Type** con valore `multipart/mixed`.

   ![Tipo di contenuto multipart/misto][4]

1. Serializzare i dati non di testo nel file. I dati JSON verranno salvati come un file JSON.
1. Nella scheda **Corpo** aggiungere ogni file assegnando un nome **chiave**, selezionando `file` o `text`.
1. Quindi, selezionare ogni file tramite il pulsante **Scegliere un file**.

   ![Esempio di client Postman][5]

   >[!NOTE]
   > * Il client di Postman non richiede che ai blocchi multipart sia assegnata manualmente un'informazione **Content-Type** oppure **Content-Disposition**.
   > * Non è necessario specificare tali intestazioni per ogni parte.
   > * Selezionare `multipart/mixed` o un altro **Content-Type** appropriato per l'intera richiesta.

1. Infine, fare clic su **Invia** per inviare la richiesta POST HTTP multipart.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle API di gestione di Gemelli digitali e su come usarle, consultare [How to use Azure Digital Twins management APIs](how-to-navigate-apis.md)(Come usare le API di gestione di Gemelli digitali di Azure).

- Usare richieste multipart per [aggiungere BLOB alle entità Gemelli digitali di Azure](./how-to-add-blobs.md).

- Per informazioni sull'autenticazione con le API di gestione, vedere [Eseguire l'autenticazione con le API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
[4]: media/how-to-configure-postman/content-type.png
[5]: media/how-to-configure-postman/form-body.png