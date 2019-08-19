---
title: Configurare l'autenticazione di Azure Active Directory - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: a77a41500a9c22aa25d3de396e73a5b2e4c0c419
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033896"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Configurare l'app del servizio app per l'uso dell'accesso di Azure Active Directory

> [!NOTE]
> A questo punto, AAD V2 (incluso MSAL) non è supportato per servizi app Azure e funzioni di Azure. Verificare la disponibilità di aggiornamenti.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo descrive come configurare i servizi app di Azure per usare Azure Active Directory come provider di autenticazione.

## <a name="express"> </a>Configurazione con impostazioni rapide

1. Nel [portale di Azure] passare alla pagina dell'applicazione dei servizi app. Nel riquadro di spostamento a sinistra selezionare **Autenticazione/Autorizzazione**.
2. Se l'opzione **Autenticazione/Autorizzazione** non è abilitata, selezionare **Attivato**.
3. Selezionare **Azure Active Directory** e quindi **Rapida** in **Modalità di gestione**.
4. Selezionare **OK** per registrare l'applicazione dei servizi app in Azure Active Directory. Viene creata una nuova registrazione per l'app. Se invece si intende scegliere la registrazione di un'app esistente, fare clic su **Seleziona un'app esistente** e quindi cercare il nome di una registrazione di app creata in precedenza all'interno del tenant.
   Fare clic sulla registrazione di app per selezionarla e fare clic su **OK**. Quindi, fare clic su **OK** nella pagina Impostazioni di Azure Active Directory.
   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
5. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Azure Active Directory, impostare **Azione da eseguire quando la richiesta non è autenticata** su **Accedi con Azure Active Directory**. Per poter usare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.

> [!CAUTION]
> La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** può essere preferibile, con l'app che avvia manualmente l'accesso, come descritto [qui](overview-authentication-authorization.md#authentication-flow).

6. Fare clic su **Save**.

## <a name="advanced"> </a>Configurazione con impostazioni avanzate

È anche possibile specificare manualmente le impostazioni di configurazione. Questa è la soluzione migliore se il tenant di Azure Active Directory che si intende usare è diverso dal tenant con cui si accede ad Azure. Per completare la configurazione, è innanzitutto necessario creare una registrazione in Azure Active Directory. È quindi necessario fornire alcune informazioni di registrazione al servizio app.

### <a name="register"> </a>Registrare l'applicazione dei servizi app con Azure Active Directory

1. Accedere al [portale di Azure] e passare all'app del servizio app. Copiare l'**URL** dell'app. L'URL verrà usato per configurare la registrazione dell'app di Azure Active Directory.
2. Passare ad **Active Directory**, selezionare **Registrazioni per l'app**, quindi fare clic su **Registrazione nuova applicazione** nella parte superiore per avviare la registrazione della nuova app. 
3. Nella pagina **Crea** immettere un valore nel campo **Nome** per la registrazione dell'app, selezionare il tipo **App Web/API** e nella casella **URL accesso** incollare l'URL dell'applicazione copiato nel passaggio 1. Fare quindi clic su **Crea**.
4. Entro pochi secondi verrà visualizzata la registrazione della nuova app appena creata.
5. Dopo aver aggiunto la registrazione dell'app, fare clic sul nome della registrazione dell'app, fare clic su **Impostazioni** nella parte superiore e quindi su **Proprietà** 
6. Incollare l'URL dell'applicazione copiato nel passaggio 1 nella casella **URI ID app** e in **URL pagina iniziale**, quindi fare clic su **Salva**
7. A questo punto, fare clic sugli **URL di risposta**, modificare l' **URL di risposta**, incollare l'URL dell'applicazione (dal passaggio 1), quindi aggiungerlo alla fine dell'URL, */.auth/login/AAD/callback* ( `https://contoso.azurewebsites.net/.auth/login/aad/callback`ad esempio,). Fare clic su **Save**.

   > [!NOTE]
   > È possibile usare la stessa registrazione di app per più domini aggiungendo **URL di risposta**aggiuntivi. Assicurarsi di modellare ogni istanza del servizio app con la propria registrazione, in modo che disponga delle autorizzazioni e del consenso. Prendere in considerazione anche l'uso di registrazioni di app separate per slot di siti distinti. Questo consente di evitare che le autorizzazioni vengano condivise tra ambienti, in modo che un bug nel nuovo codice che si sta testando non influisca sulla produzione.
    
8. A questo punto, copiare l'**ID applicazione** dell'app per usarlo in seguito. Questo valore sarà necessario per configurare l'applicazione dei servizi app.
9. Chiudere la pagina **App registrata**. Nella pagina **Registrazioni per l'app** fare clic sul pulsante **Endpoint** nella parte superiore e quindi copiare l'URL **WS-FEDERATION SIGN-ON ENDPOINT**, rimuovendo la parte finale `/wsfed` dall'URL. Il risultato finale dovrebbe essere simile a `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. Il nome di dominio può essere diverso per un cloud sovrano. Questo nome verrà usato come URL dell'autorità di certificazione più avanti.

### <a name="secrets"> </a>Aggiungere informazioni di Azure Active Directory all'applicazione dei servizi app

1. Nel [portale di Azure] passare all'applicazione dei servizi app. Fare clic su **Autenticazione/autorizzazione**. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**. Fare clic su **Azure Active Directory** in Provider di autenticazione per configurare l'app.

    Facoltativo: per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app. Impostare **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**. Per poter usare questa opzione, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.
2. Nella configurazione dell'autenticazione di Active Directory fare clic su **Avanzate** in **Modalità di gestione**. Incollare l'ID applicazione nella casella ID client (dal passaggio 8) e incollare l'URL (dal passaggio 9) come valore del campo URL autorità di certificazione. Fare quindi clic su **OK**.
3. Nella pagina di configurazione dell'autenticazione di Active Directory fare clic su **Salva**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'applicazione dei servizi app.

## <a name="configure-a-native-client-application"></a>Configurare un'applicazione client nativa
È possibile registrare client nativi per ottenere maggiore controllo sul mapping delle autorizzazioni. Questa operazione è utile se si vuole eseguire gli accessi usando una libreria client come **Active Directory Authentication Library**.

1. Passare a **Azure Active Directory** nel [portale di Azure].
2. Nel riquadro di spostamento a sinistra selezionare **Registrazioni per l'app**. Fare clic su **New app registration** (Nuova registrazione app) nella parte superiore.
4. Nella pagina **Crea** immettere il nome della registrazione dell'app nel campo **Nome**. In **Tipo applicazione** selezionare **Nativo**.
5. Nella casella **URI di reindirizzamento** immettere l'endpoint del sito */.auth/login/done*, usando lo schema HTTPS. Questo valore deve essere simile a *https://contoso.azurewebsites.net/.auth/login/done* . Se si crea un'applicazione Windows, usare invece il [SID pacchetto](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) come URI.
5. Fare clic su **Create**(Crea).
6. Dopo aver aggiunto la registrazione dell'app, selezionarla per aprirla. Trovare l'**ID applicazione** e prendere nota del valore.
7. Fare clic su **Tutte le impostazioni** > **Autorizzazioni necessarie** > **Aggiungi** > **Selezionare un'API**.
8. Digitare il nome dell'applicazione dei servizi app registrata in precedenza per cercarlo e fare clic su **Seleziona**.
9. Selezionare **Accesso a \<nome_app>** . Quindi fare clic su **Seleziona**. Fare quindi clic su **Done**.

Ora è stata configurata un'applicazione client nativa che può accedere all'applicazione dei servizi app.

## <a name="related-content"></a>Contenuti correlati

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Portale di Azure]: https://portal.azure.com/
[alternative method]:#advanced
