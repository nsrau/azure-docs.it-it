---
title: Configurare Postman per le chiamate all'API REST di Servizi multimediali di AzureConfigure Postman for Azure Media Services v3 REST API calls
description: In questo articolo viene illustrato come configurare Postman in modo da usarlo per chiamare le API REST di Servizi multimediali di Azure (AMS).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779638"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Configurare Postman per le chiamate all'API REST di Servizi multimediali v3Configure Postman for Media Services v3 REST API calls

In questo articolo viene illustrato come configurare **Postman** in modo da usarlo per chiamare le API REST di Servizi multimediali di Azure (AMS). L'articolo illustra come importare i file di ambiente e di raccolta in **Postman**. La raccolta contiene definizioni raggruppate delle richieste HTTP che chiamano le API REST di Servizi multimediali di Azure (AMS). Il file di ambiente contiene le variabili usate dalla raccolta.

Prima di iniziare lo sviluppo, vedere Sviluppo con API di [Servizi multimediali v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- Ottenere le informazioni necessarie per [accedere alle API](access-api-cli-how-to.md)
- Installare il client REST di [Postman](https://www.getpostman.com/) per eseguire le API REST mostrate in alcune delle esercitazioni REST di AMS. 

    Si sta usando **Postman** ma si può usare qualsiasi strumento REST. Altre alternative sono: **Visual Studio Code** con il plug-in REST o **Telerik Fiddler**. 

> [!IMPORTANT]
> Esaminare [le convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Scaricare i file Postman

Clonare un repository di GitHub che contiene i file di raccolta e ambiente Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurare Postman

### <a name="configure-the-environment"></a>Configurare l'ambiente 

1. Aprire l'app **Postman**.
2. A destra dello schermo, selezionare l'opzione **Manage environment** (Gestisci ambiente).

    ![Gestire l'ambiente](./media/develop-with-postman/postman-import-env.png)
4. Dalla finestra di dialogo **Manage environment** (Gestisci ambiente), fare clic su **Import** (Importa).
2. Individuare il file `Azure Media Service v3 Environment.postman_environment.json` scaricato quando è stato clonato `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Viene aggiunto l'ambiente **Azure Media Service v3 Environment**.

    > [!Note]
    > Aggiornare le variabili di accesso con i valori ottenuti dalla sezione **Accedere all'API di Servizi multimediali** precedente.

7. Fare doppio clic sul file selezionato e immettere i valori ottenuti seguendo la procedura per l'accesso all'API.
8. Chiudere la finestra di dialogo.
9. Selezionare l'ambiente **Azure Media Service v3 Environment** nell'elenco a discesa.

    ![Scegliere l'ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurare la raccolta

1. Fare clic su **Import** (Importa) per importare il file di raccolta.
1. Individuare il file `Media Services v3.postman_collection.json` scaricato quando è stato clonato `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Scegliere il file **Media Services v3.postman_collection.json**.

    ![Importare un file](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Ottenere il token di Azure AD 

Prima di iniziare la modifica delle risorse in servizi multimediali di Microsoft Azure v3 è necessario ottenere e impostare token di Azure AD per l'autenticazione dell'entità servizio.

1. Nella finestra di sinistra dell'app Postman, seleziona "Passaggio 1: Ottieni token di autenticazione AAD".
2. Selezionare quindi "Get Azure AD Token for Service Principal Authentication" (Ottieni token Azure AD per autenticazione basata su entità servizio).
3. Fare clic su **Invia**.

    Viene inviata l'operazione **POST** seguente.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. La risposta viene restituita con il token e imposta la variabile di ambiente "AccessToken" sul valore del token.  

    ![Ottenere il token AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi 

* Se l'applicazione non riesce con "HTTP 504: Timeout gateway", assicurarsi che la variabile di percorso non sia stata impostata in modo esplicito su un valore diverso dal percorso previsto dell'account di Servizi multimediali. 
* Se viene visualizzato un errore "account non trovato", verificare anche che la proprietà location nel messaggio JSON Body sia impostata sul percorso in cui si trova l'account di Servizi multimediali. 

## <a name="see-also"></a>Vedere anche

- [Caricare file in un account di Servizi multimediali - REST](upload-files-rest-how-to.md)
- [Creare filtri con Servizi multimediali - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST basate su Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Passaggi successivi

- [Streaming di file con REST](stream-files-tutorial-with-rest.md).  
- [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video - RESTTutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md)
