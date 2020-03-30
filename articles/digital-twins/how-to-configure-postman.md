---
title: Come configurare Postman - Azure Digital Twins Documenti Microsoft
description: Informazioni su come configurare e usare Postman per testare le API di Azure Digital Twins.Learn how to configure and use Postman to test the Azure Digital Twins APIs.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297162"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Come configurare Postman per Gemelli digitali di Azure

Questo articolo illustra come configurare il client REST di Postman per testare e interagire con le API di gestione di Gemelli digitali di Azure. In particolare, illustra le operazioni seguenti:

* Come configurare un'applicazione di Azure Active Directory per usare il flusso di concessione implicita OAuth 2.0.
* Come usare il client REST di Postman per inviare richieste HTTP di token alle API di gestione.
* Come usare Postman per inviare richieste POST multipart alle API di gestione.

## <a name="postman-summary"></a>Riepilogo di Postman

Iniziare a usare Gemelli digitali di Azure tramite uno strumento client REST, ad esempio [Postman](https://www.getpostman.com/), per preparare l'ambiente di test locale. Il client Postman consente di creare rapidamente richieste HTTP complesse. Scaricare la versione desktop del client Postman accedendo a [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) è uno strumento di test REST che inserisce le principali funzionalità di richiesta HTTP in un'utile GUI desktop e basata su plug-in.

Tramite il client Postman, gli sviluppatori di soluzioni possono specificare il tipo di richiesta HTTP (*POST*, *GET*, *UPDATE*, *PATCH*e *DELETE*), l'endpoint API da chiamare e utilizzare TLS. Postman supporta anche l'aggiunta di intestazioni della richiesta HTTP, parametri, dati del modulo e corpi.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurare Azure Active Directory per usare il flusso di concessione implicita OAuth 2.0

1. Seguire i passaggi [della Guida introduttiva](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) per creare e configurare un'applicazione Azure Active Directory.Follow the steps in the Quickstart to create and configure an Azure Active Directory application. In alternativa, puoi riutilizzare una registrazione dell'app esistente.

    [![Configurare un nuovo URI di reindirizzamento post-inman](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. A questo punto, `https://www.getpostman.com/oauth2/callback`aggiungere un URI di **reindirizzamento** a .

1. Selezionare la casella di controllo Token di accesso **concedibili** > **impliciti** per consentire l'utilizzo del flusso di concessione implicita OAuth 2.0. Selezionare **Configura**, quindi **Salva**.

1. Copiare **l'ID client** dell'app Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Ottenere un token OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Impostare e configurare Postman per ottenere un token di Azure Active Directory.Set up and configure Postman to obtain an Azure Active Directory token. In seguito, eseguire una richiesta HTTP autenticata a Gemelli digitali usando il token acquisito:

1. Verificare che l'**URL di autorizzazione** sia corretto. Deve essere nel formato seguente:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nome  | Sostituire con | Esempio |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nome del tenant o dell'organizzazione. Usare il nome descrittivo anziché **l'ID tenant** alfanumerico della registrazione dell'app Azure Active Directory. | `microsoft` |

1. Passare a [www.getpostman.com](https://www.getpostman.com/) per scaricare l'app.

1. Vogliamo fare richiesta GET. Selezionare la scheda **Autorizzazione** , selezionare OAuth 2.0 e quindi **selezionare Ottieni nuovo token**di accesso .

    | Campo  | valore |
    |---------|---------|
    | Grant Type (Tipo di concessione) | `Implicit` |
    | Callback URL (URL callback) | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL (URL autorizzazione) | Utilizzare **l'URL di autorizzazione** del **passaggio 1** |
    | ID client | Usare **l'ID applicazione** per l'app Azure Active Directory creata o riutilizzata dalla sezione precedenteUse the Application ID for the Azure Active Directory app that was created or reused from the previous section |
    | Scope | Lasciare vuoto |
    | State | Lasciare vuoto |
    | Client Authentication (Autenticazione client) | `Send as Basic Auth header` |

1. Il client ora dovrebbe essere visualizzato come:

    [![Esempio di token client Postman](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selezionare **Request Token** (Richiedi token).
  
1. Scorrere verso il basso e selezionare **Use Token** (Usa token).

## <a name="make-a-multipart-post-request"></a>Effettuare una richiesta POST multipart

Dopo aver completato i passaggi precedenti, configurare Postman per eseguire una richiesta POST multipart HTTP autenticata:

1. Nella scheda **Intestazioni** aggiungere una chiave di intestazione `multipart/mixed`della richiesta HTTP **Content-Type** con valore .

   [![Specificare il tipo di contenuto multipart/mixed](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serializzare i dati non di testo nel file. I dati JSON verranno salvati come un file JSON.
1. Nella scheda **Corpo** `form-data`selezionare . 
1. Aggiungere ogni file assegnando un `File`nome di **chiave,** selezionando .
1. Quindi, selezionare ogni file tramite il pulsante **Scegliere un file**.

   [![Esempio di corpo del modulo del modulo del cliente Postman](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Il client di Postman non richiede che ai blocchi multipart sia assegnata manualmente un'informazione **Content-Type** oppure **Content-Disposition**.
   > * Non è necessario specificare tali intestazioni per ogni parte.
   > * Selezionare `multipart/mixed` o un altro **Content-Type** appropriato per l'intera richiesta.

1. Infine, selezionare **Invia** per inviare la richiesta HTTP POST in più parti. Un codice `200` di `201` stato di o indica una richiesta riuscita. Il messaggio di risposta appropriato verrà visualizzato nell'interfaccia client.

1. Convalidare i dati della richiesta HTTP POST chiamando l'endpoint API:Validate your HTTP POST request data by calling the API endpoint: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle API di gestione di Gemelli digitali e su come usarle, consultare [How to use Azure Digital Twins management APIs](how-to-navigate-apis.md)(Come usare le API di gestione di Gemelli digitali di Azure).

- Usare richieste multipart per [aggiungere BLOB alle entità Gemelli digitali di Azure](./how-to-add-blobs.md).

- Per informazioni sull'autenticazione con le API di gestione, vedere [Eseguire l'autenticazione con le API](./security-authenticating-apis.md).
