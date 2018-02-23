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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con REST

Quando si usa l'autenticazione di Azure AD con Servizi multimediali di Azure, è possibile eseguire l'autenticazione in uno di due modi:

- L'**autenticazione utente** consente di eseguire l'autenticazione di una persona che usa l'app per interagire con le risorse di Servizi multimediali di Azure. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 
- L'**autenticazione basata su un'entità servizio** consente di eseguire l'autenticazione di un servizio. Le applicazioni che usano in genere questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio app Web, app per le funzioni, app per la logica, API o microservizi.

    Questa esercitazione mostra come usare l'autenticazione dell'**entità servizio** di Azure AD per accedere all'API AMS con REST. 

    > [!NOTE]
    > L'**entità servizio** è la procedura consigliata per la maggior parte delle applicazioni che si connettono a Servizi multimediali di Azure. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Ottenere le informazioni di autenticazione dal portale di Azure
> * Ottenere il token di accesso usando Postman
> * Testare l'API **Assets** usando il token di accesso


> [!IMPORTANT]
> Attualmente Servizi multimediali supporta il modello di autenticazione dei servizi di Controllo di accesso di Azure. L'autenticazione di Controllo di accesso, tuttavia, verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire al più presto la migrazione al modello di autenticazione di Azure AD.

## <a name="prerequisites"></a>prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
- Rivedere l'argomento di carattere generale [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md).
- Installare il client REST di [Postman](https://www.getpostman.com/) per eseguire le API REST mostrate in questo articolo. 

    In questa esercitazione si sta usando **Postman** ma si può usare qualsiasi strumento REST. Tra le alternative vi sono: **Visual Studio Code** con il plug-in REST o **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Ottenere le informazioni di autenticazione dal portale di Azure

### <a name="overview"></a>Panoramica

Per accedere alle API di Servizi multimediali, è necessario raccogliere i punti dati seguenti.

|Impostazione|Esempio|DESCRIZIONE|
|---|-------|-----|
|Dominio del tenant di Azure Active Directory|microsoft.onmicrosoft.com|Azure AD come endpoint STS (servizio token di sicurezza) viene creato tramite il formato seguente: https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Azure AD emette un token JWT per accedere alle risorse (token di accesso).|
|Endpoint API REST|https://amshelloworld.restv2.westus.media.azure.net/api/|Si tratta dell'endpoint verso il quale vengono eseguite tutte le chiamate all'API REST di Servizi multimediali nell'applicazione,|
|ID client (ID applicazione)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID applicazione (cliente) Azure AD. L'ID client è obbligatorio per ottenere il token di accesso. |
|Client Secret|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Chiavi (segreto client) dell'applicazione Azure AD. Il segreto client è obbligatorio per ottenere il token di accesso.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Ottenere informazioni di autenticazione di AAD dal portale di Azure

Per ottenere le informazioni, seguire questa procedura:

1. Accedere al [Portale di Azure](http://portal.azure.com).
2. Passare all'istanza di Servizi multimediali di Azure.
3. Selezionare **Accesso all'API**.
4. Fare clic su **Connettersi all'API Servizi multimediali di Azure con l'entità servizio**.

    ![Accesso all'API](./media/connect-with-rest/connect-with-rest01.png)

5. Selezionare un'**applicazione Azure AD** esistente o crearne una nuova come indicato di seguito.

    > [!NOTE]
    > Perché la richiesta REST di Servizi multimediali di Azure abbia esito positivo, l'utente chiamante deve avere il ruolo di **Collaboratore** o **Proprietario** per l'account di Servizi multimediali al quale sta tentando di accedere. Se si verifica un'eccezione che indica "Errore del server remoto: (401) Non autorizzato", vedere il [controllo di accesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Se è necessario creare una nuova app AD, seguire questa procedura:
    
    1. Premere **Crea nuova**.
    2. Immettere un nome.
    3. Premere nuovamente **Crea nuova**.
    4. Premere **Salva**.

    ![Accesso all'API](./media/connect-with-rest/new-app.png)

    La nuova app viene visualizzata nella pagina.

6. Ottenere l'**ID client** (ID applicazione).
    
    1. Selezionare l'applicazione.
    2. Ottenere l'**ID Client** dalla finestra a destra. 

    ![Accesso all'API](./media/connect-with-rest/existing-client-id.png).

7.  Ottenere la **chiave** (segreto client) dell'applicazione. 

    1. Fare clic sul pulsante **Gestisci applicazione**. Si noti che le informazioni dell'ID client si trovano in **ID applicazione**. 
    2. Premere **Chiavi**.
    
        ![Accesso all'API](./media/connect-with-rest/manage-app.png)
    3. Generare la chiave dell'applicazione (segreto client) compilando **DESCRIZIONE** e **SCADENZA** e premendo **Salva**.
    
        Dopo aver premuto il pulsante **Salva** viene visualizzato il valore della chiave. Copiare il valore della chiave prima di chiudere il pannello.

    ![Accesso all'API](./media/connect-with-rest/connect-with-rest03.png)

È possibile aggiungere valori per i parametri di connessione Active Directory nel file web.config o app.config da usare successivamente nel codice.

> [!IMPORTANT]
> La **chiave Ccient** è un segreto importante e deve quindi essere protetta in modo appropriato in un insieme di credenziali delle chiavi oppure crittografata durante la produzione.

## <a name="get-the-access-token-using-postman"></a>Ottenere il token di accesso usando Postman

Questa sezione mostra come usare **Postman** per eseguire un'API REST che restituisce un token di connessione JWT (token di accesso). Per chiamare le API REST di Servizi multimediali, è necessario aggiungere l'intestazione "Authorization" alle chiamate e aggiungere il valore "Bearer *your_access_token*" a ciascuna chiamata, come mostrato nella prossima sezione dell'esercitazione. 

1. Aprire **Postman**.
2. Selezionare **POST**.
3. Immettere l'URL che include il nome di tenant usando il formato seguente: il nome del tenant deve terminare con **.onmicrosoft.com** e l'URL con **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Selezionare la scheda **Intestazioni**.
5. Immettere le informazioni di **Intestazioni** usando la griglia dati "Chiave/Valore". 

    ![Griglia dati](./media/connect-with-rest/headers-data-grid.png)

    In alternativa, fare clic sul collegamento **Modifica in blocco** a destra della finestra di Postman e incollare il codice seguente.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Premere la scheda **Corpo**.
7. Immettere le informazioni sul corpo usando la griglia dati "Chiave/Valore". Sostituire i valori ID client e segreto. 

    ![Griglia dati](./media/connect-with-rest/data-grid.png)

    In alternativa, fare clic su **Modifica in blocco** a destra della finestra di Postman e incollare il corpo seguente. Sostituire i valori ID client e segreto:

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Fare clic su **Invia**.

    ![ottenere un token](./media/connect-with-rest/connect-with-rest04.png)

La risposta restituita contiene il **token di accesso** da usare per accedere alle API di Servizi multimediali di Azure.

## <a name="test-the-assets-api-using-the-access-token"></a>Testare l'API **Assets** usando il token di accesso

Questa sezione mostra come accedere alle API **Assets** tramite **Postman**.

1. Aprire **Postman**.
2. Selezionare **GET**.
3. Incollare l'endpoint API REST (ad esempio https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selezionare la scheda **Autorizzazione**. 
5. Selezionare **Token di connessione**.
6. Incollare il token creato nella sezione precedente.

    ![ottenere un token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > L'esperienza utente di Postman potrebbe essere diversa tra PC e Mac. Se la versione Mac non presenta l'opzione "Token di connessione" nel menu a discesa della sezione **Autenticazione**, è necessario aggiungere manualmente l'intestazione **Autorizzazione** nel client Mac.

   ![Intestazione di autenticazione](./media/connect-with-rest/auth-header.png)

7. Selezionare **Intestazioni**.
5. Fare clic sul collegamento **Modifica in blocco** a destra della finestra di Postman.
6. Incollare le intestazioni seguenti:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Fare clic su **Invia**.

La risposta restituita contiene le risorse presenti nell'account.

## <a name="next-steps"></a>Passaggi successivi

* Provare questo codice di esempio in [Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln) (Autenticazione di Azure AD per l'accesso a Servizi multimediali di Microsoft Azure: entrambi tramite REST)
* [Caricare file con .NET](media-services-dotnet-upload-files.md)
