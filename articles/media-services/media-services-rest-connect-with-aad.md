---
title: Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con REST | Microsoft Docs
description: Informazioni su come accedere all'API Servizi multimediali di Azure con l'autenticazione di Azure Active Directory tramite REST.
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con REST

Quando si usa l'autenticazione di Azure AD con Servizi multimediali di Azure, è possibile eseguire l'autenticazione in uno di due modi:

- L'**autenticazione utente** consente di eseguire l'autenticazione di una persona che usa l'app per interagire con le risorse di Servizi multimediali di Azure. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 
- L'**autenticazione basata su un'entità servizio** consente di eseguire l'autenticazione di un servizio. Le applicazioni che usano in genere questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio app Web, app per le funzioni, app per la logica, API o microservizi.

    In questa esercitazione viene illustrato come utilizzare Azure AD **dell'entità servizio** autenticazione per accedere a AMS API REST. 

    > [!NOTE]
    > **Entità servizio** è consigliata per la maggior parte delle applicazioni che si connettono a servizi multimediali di Azure. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Ottenere le informazioni di autenticazione dal portale di Azure
> * Ottenere il token di accesso usando Postman
> * Test di **asset** API usando il token di accesso


> [!IMPORTANT]
> Attualmente Servizi multimediali supporta il modello di autenticazione dei servizi di Controllo di accesso di Azure. L'autenticazione di Controllo di accesso, tuttavia, verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire al più presto la migrazione al modello di autenticazione di Azure AD.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [Creare un account di servizi multimediali di Azure tramite il portale di Azure](media-services-portal-create-account.md).
- Esaminare il [accesso alle API di servizi multimediali Azure con panoramica dell'autenticazione AAD](media-services-use-aad-auth-to-access-ams-api.md) articolo.
- Installare il [Postman](https://www.getpostman.com/) client REST per eseguire le API REST illustrati in questo articolo. 

    In questa esercitazione, siamo durante **Postman** ma qualsiasi strumento REST, la soluzione appropriata. Sono disponibili altre alternative: **codice di Visual Studio** con il plug-in REST o **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Ottenere le informazioni di autenticazione dal portale di Azure

### <a name="overview"></a>Panoramica

Per accedere alle API di servizi multimediali, è necessario raccogliere i dati seguenti.

|Impostazione|Esempio|DESCRIZIONE|
|---|-------|-----|
|Dominio del tenant di Azure Active Directory|Microsoft.onmicrosoft.com|Azure AD come un endpoint di servizio (token di sicurezza) viene creato utilizzando il seguente formato: https://login.microsoftonline.com/ {your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Azure AD rilascia un token JWT per accedere alle risorse (un token di accesso).|
|Endpoint API REST|https://amshelloworld.restv2.westus.Media.Azure.NET/API/|Si tratta dell'endpoint verso il quale vengono eseguite tutte le chiamate all'API REST di Servizi multimediali nell'applicazione,|
|ID client (ID applicazione)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD ID dell'applicazione (client). Per ottenere il token di accesso, è necessario l'ID client. |
|Client Secret|mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Azure Active Directory le chiavi dell'applicazione (segreto client). Il segreto client è necessaria per ottenere il token di accesso.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Ottenere informazioni di autenticazione di Azure ad dal portale di Azure

Per ottenere le informazioni, seguire questi passaggi:

1. Accedere al [Portale di Azure](http://portal.azure.com).
2. Passare all'istanza del sistema AMS.
3. Selezionare **l'accesso all'API**.
4. Fare clic su **Connect per l'API di servizi multimediali di Azure con l'entità servizio**.

    ![Accesso all'API](./media/connect-with-rest/connect-with-rest01.png)

5. Selezionare un oggetto esistente **applicazione Azure AD** o crearne uno nuovo (mostrato sotto).

    > [!NOTE]
    > Per la richiesta REST multimediali di Azure abbia esito positivo, l'utente chiamante deve avere un **collaboratore** o **proprietario** ruolo per l'account di servizi multimediali sta tentando di accedere. Se si verifica un'eccezione che indica "il server remoto ha restituito un errore: non autorizzato (401)," vedere [il controllo degli accessi](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Se è necessario creare una nuova app di Active Directory, seguire questi passaggi:
    
    1. Premere **creare nuovi**.
    2. Immettere un nome.
    3. Premere **Crea nuovo** nuovamente.
    4. Premere **Salva**.

    ![Accesso all'API](./media/connect-with-rest/new-app.png)

    La nuova app viene visualizzato nella pagina.

6. Ottenere il **ID Client** (ID applicazione).
    
    1. Selezionare l'applicazione.
    2. Ottenere il **ID Client** dalla finestra a destra. 

    ![Accesso all'API](./media/connect-with-rest/existing-client-id.png).

7.  Ottenere l'applicazione **chiave** (segreto client). 

    1. Fare clic su di **Gestisci applicazione** pulsante (si noti che le informazioni di ID Client sono in **ID applicazione**). 
    2. Premere **chiavi**.
    
        ![Accesso all'API](./media/connect-with-rest/manage-app.png)
    3. Generare la chiave dell'applicazione (segreto client), compilando **descrizione** e **EXPIRES** e premendo **salvare**.
    
        Una volta il **salvare** pulsante, viene visualizzato il valore della chiave. Copiare il valore della chiave prima di chiudere il pannello.

    ![Accesso all'API](./media/connect-with-rest/connect-with-rest03.png)

È possibile aggiungere valori per parametri di connessione Active Directory nel file Web. config o App. config, per un utilizzo successivo nel codice.

> [!IMPORTANT]
> Il **chiave Client** è un importante segreto e devono essere correttamente protetti in un insieme di credenziali chiave o crittografati nell'ambiente di produzione.

## <a name="get-the-access-token-using-postman"></a>Ottenere il token di accesso usando Postman

In questa sezione viene illustrato come utilizzare **Postman** per l'esecuzione di un'API REST che restituisce un Token di connessione di JWT (token di accesso). Per chiamare le API REST di servizi multimediali, è necessario aggiungere l'intestazione "Authorization" per le chiamate e aggiungere il valore di "portatore *your_access_token*" per ogni chiamata (come illustrato nella sezione successiva di questa esercitazione). 

1. Aprire **Postman**.
2. Selezionare **POST**.
3. Immettere l'URL che include il nome di tenant usando il formato seguente: il nome del tenant deve terminare con **. c o m** e l'URL deve terminare con **/token oauth2**: 

    https://Login.microsoftonline.com/ {your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Selezionare il **intestazioni** scheda.
5. Immettere il **intestazioni** informazioni utilizzando la griglia di dati "Chiave/valore". 

    ![Griglia dei dati](./media/connect-with-rest/headers-data-grid.png)

    In alternativa, fare clic su **modifica in blocco** link a destra della finestra Postman e incollare il codice seguente.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Premere il **corpo** scheda.
7. Immettere le informazioni sul corpo utilizzando la griglia di dati "Chiave/valore" (sostituire l'ID client e i valori dei segreti). 

    ![Griglia dei dati](./media/connect-with-rest/data-grid.png)

    In alternativa, fare clic su **modifica in blocco** a destra della finestra Postman e incollare il seguente corpo (sostituire l'ID client e i valori dei segreti):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Fare clic su **Invia**.

    ![Ottenere un token](./media/connect-with-rest/connect-with-rest04.png)

La risposta restituita contiene il **token di accesso** che si desidera utilizzare per accedere a qualsiasi APIs AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Test di **asset** API usando il token di accesso

In questa sezione viene illustrato come accedere il **asset** API tramite **Postman**.

1. Aprire **Postman**.
2. Selezionare **GET**.
3. Incollare l'endpoint dell'API REST (ad esempio, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selezionare il **autorizzazione** scheda. 
5. Selezionare **Token di connessione**.
6. Incollare il token che è stato creato nella sezione precedente.

    ![Ottenere un token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > L'esperienza utente Postman potrebbero essere diverso tra PC e Mac. Se la versione Mac non è "Token di connessione" **autenticazione** a discesa di sezione, è necessario aggiungere il **autorizzazione** testata manualmente il client Mac.

   ![Intestazione di autenticazione](./media/connect-with-rest/auth-header.png)

7. Selezionare **intestazioni**.
5. Fare clic su **modifica in blocco** link a destra della finestra di Postman.
6. Incollare le intestazioni seguenti:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Fare clic su **Invia**.

La risposta restituita contiene le risorse presenti nell'account.

## <a name="next-steps"></a>Passaggi successivi

* Provare questo codice di esempio in [autenticazione di Azure AD per l'accesso di Azure Media Services: sia tramite l'API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Caricare file con .NET](media-services-dotnet-upload-files.md)
