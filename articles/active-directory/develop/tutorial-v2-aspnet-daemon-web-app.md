---
title: Creare un daemon multi-tenant che usa l'endpoint di Microsoft Identity Platform
description: Questa esercitazione illustra come chiamare un'API Web ASP.NET protetta da Azure Active Directory da un'applicazione desktop di Windows (WPF). Il client WPF autentica un utente, richiede un token di accesso e chiama l'API Web.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: d130a962c14415c417eedecd6ae26af1131b2e86
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997021"
---
# <a name="build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Creare un daemon multi-tenant che usa l'endpoint di Microsoft Identity Platform

Questa esercitazione illustra come usare Microsoft Identity Platform per accedere ai dati dei clienti aziendali Microsoft in un processo non interattivo e di lunga durata. Il daemon di esempio usa la [concessione di credenziali client OAuth2](v2-oauth2-client-creds-grant-flow.md) per acquisire un token di accesso. Il daemon usa il token per chiamare [Microsoft Graph](https://graph.microsoft.io) e accedere ai dati dell'organizzazione.

L'app viene creata come applicazione MVC ASP.NET. Usa il middleware OWIN OpenID Connect per concedere l'accesso agli utenti.  

Il componente "daemon" in questo esempio è un controller API, `SyncController.cs`. Quando viene chiamato, il controller recupera da Microsoft Graph un elenco di utenti del tenant di Azure Active Directory (Azure AD) del cliente. `SyncController.cs` viene attivato da una chiamata AJAX nell'applicazione Web. Usa [Microsoft Authentication Library (MSAL) per .NET](msal-overview.md) per acquisire un token di accesso per Microsoft Graph.

Per un'applicazione daemon console più semplice, vedere l'[argomento di avvio rapido sul daemon .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenario

Poiché l'app è multi-tenant per i clienti aziendali Microsoft, deve fornire un modo per "iscriversi" o "connettere" l'applicazione ai dati aziendali. Durante il flusso di connessione, un amministratore dell'azienda concede prima di tutto le *autorizzazioni dell'applicazione* direttamente all'app affinché possa accedere ai dati aziendali in modo non interattivo, senza la presenza di un utente connesso. La maggior parte della logica di questo esempio mostra come ottenere questo flusso di connessione usando l'endpoint del [consenso dell'amministratore](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) di Identity Platform.

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Per altre informazioni sui concetti di questo esempio, leggere la [documentazione relativa al protocollo di credenziali client per l'endpoint di Identity Platform](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire l'esempio in questo argomento di avvio rapido, è necessario avere:

- [Visual Studio 2017 o 2019](https://visualstudio.microsoft.com/downloads/).
- Un tenant di Azure AD. Per altre informazioni, vedere [Come ottenere un tenant di Azure AD](quickstart-create-new-tenant.md).
- Uno o più account utente nel tenant di Azure AD. Questo esempio non funziona con un account Microsoft (in precedenza account Windows Live). Se è stato effettuato l'accesso al [portale di Azure](https://portal.azure.com) con un account Microsoft e non è mai stato creato un account utente nella directory, è necessario eseguire questa operazione ora.

## <a name="clone-or-download-this-repository"></a>Clonare o scaricare questo repository

Dalla shell o dalla riga di comando immettere questo comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

In alternativa [scaricare l'esempio in un file ZIP](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Registrare l'applicazione di esempio con il tenant di Azure AD

L'esempio contiene un unico progetto. Per registrarlo, è possibile:

- Seguire le procedure riportate in [Registrare l'esempio con il tenant di Azure Active Directory](#register-the-sample-application-with-your-azure-ad-tenant) e [Configurare l'esempio per l'uso del tenant di Azure AD](#choose-the-azure-ad-tenant).
- Usare gli script di PowerShell per:
  - Creare *automaticamente* le applicazioni di Azure AD e gli oggetti correlati (password, autorizzazioni, dipendenze).
  - Modificare i file di configurazione dei progetti di Visual Studio.

Se si vuole usare questa automazione:

1. In Windows eseguire PowerShell e passare alla radice della directory clonata.
1. Eseguire questo comando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Eseguire lo script per creare l'applicazione di Azure AD e configurare di conseguenza il codice dell'applicazione di esempio:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Altre modalità di esecuzione degli script sono descritte in [Script di creazione app](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Aprire la soluzione in Visual Studio e selezionare **Avvia** per eseguire il codice.

Se non si vuole usare l'automazione, seguire i passaggi delle sezioni seguenti.

### <a name="choose-the-azure-ad-tenant"></a>Scegliere il tenant di Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account è presente in più tenant di Azure AD, selezionare il proprio profilo nel menu situato nella parte superiore della pagina e quindi selezionare **Cambia directory**.
1. Impostare la sessione del portale sul tenant di Azure AD desiderato.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrare l'app client (dotnet-web-daemon-v2)

1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per gli sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app. Ad esempio, immettere **dotnet-web-daemon-v2**.
   - Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione**.
   - Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere gli URI di reindirizzamento seguenti:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Se sono presenti più URI di reindirizzamento, è necessario aggiungerli dalla scheda **Autenticazione** dopo che l'app sarà stata creata correttamente.
1. Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Sarà necessario per configurare il file di configurazione di Visual Studio per questo progetto.
1. Nell'elenco delle pagine per l'app selezionare **Autenticazione**. Quindi:
   - Nella sezione **Impostazioni avanzate** impostare **URL di disconnessione** su **https://localhost:44316/Account/EndSession** .
   - Nella sezione **Impostazioni avanzate** > **Concessione implicita** selezionare **Token di accesso** e **Token ID**. Per questo esempio è necessario abilitare il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md) per consentire all'utente di accedere e chiamare un'API.
1. Selezionare **Salva**.
1. Nella sezione **Segreti client** della pagina **Certificati e segreti** selezionare **Nuovo segreto client**. Quindi:

   1. Immettere una descrizione della chiave, ad esempio **segreto dell'app**.
   1. Selezionare una durata della chiave scegliendo **Tra 1 anno**, **Tra 2 anni**o **Non scade mai**.
   1. Fare clic sul pulsante **Aggiungi**. 
   1. Quando viene visualizzato il valore della chiave, copiarlo e conservarlo in un posto sicuro. Questa chiave sarà necessaria in seguito per configurare il progetto in Visual Studio. Non verrà visualizzata di nuovo né sarà recuperabile in altri modi.
1. Nell'elenco delle pagine per l'app selezionare **Autorizzazioni API**. Quindi:
   1. Selezionare il pulsante **Aggiungi un'autorizzazione**.
   1. Verificare che la scheda **API Microsoft** sia selezionata.
   1. Nella sezione **API Microsoft più usate** selezionare **Microsoft Graph**.
   1. Nella sezione **Autorizzazioni dell'applicazione** verificare che siano selezionate le autorizzazioni corrette: **User.Read.All**.
   1. Selezionare il pulsante **Aggiungi autorizzazioni**.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurare l'esempio per l'uso del tenant di Azure AD

Nei passaggi seguenti **ClientID** corrisponde a "ID applicazione" o **AppId**.

Aprire la soluzione in Visual Studio per configurare i progetti.

### <a name="configure-the-client-project"></a>Configurare il progetto client

Se sono stati usati gli script di installazione, saranno state applicate automaticamente le modifiche seguenti.

1. Aprire il file **UserSync\Web.Config**.
1. Trovare la chiave dell'app **ida:ClientId**. Sostituire il valore esistente con l'ID dell'applicazione **dotnet-web-daemon-v2** copiato dal portale di Azure.
1. Trovare la chiave dell'app **ida:ClientSecret**. Sostituire il valore esistente con la chiave salvata durante la creazione dell'app **dotnet-web-daemon-v2** nel portale di Azure.

## <a name="run-the-sample"></a>Eseguire l'esempio

Pulire la soluzione, ricompilarla ed eseguire l'applicazione UserSync, quindi accedere come amministratore nel tenant di Azure AD. Se non si dispone di un tenant di Azure AD per il test, è possibile [seguire queste istruzioni](quickstart-create-new-tenant.md) per ottenerne uno.

Quando si accede, l'app chiede prima di tutto l'autorizzazione per consentire l'accesso e per leggere il profilo utente. Con questo consenso si conferma all'app di essere un utente aziendale.

![Consenso dell'utente](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

L'app prova quindi a sincronizzare un elenco di utenti del tenant di Azure AD, tramite Microsoft Graph. Se il tentativo non riesce, chiede all'amministratore del tenant di connettere il tenant all'app.

L'app chiede quindi l'autorizzazione per leggere l'elenco di utenti nel tenant.

![Consenso dell'amministratore](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Dopo aver concesso l'autorizzazione, si viene disconnessi dall'app. La disconnessione assicura che tutti i token di accesso esistenti per Microsoft Graph vengano rimossi dalla cache dei token. Quando si esegue di nuovo l'accesso, il nuovo token ottenuto avrà le autorizzazioni necessarie per effettuare chiamate a Microsoft Graph.


Quando si concede l'autorizzazione, l'app può eseguire query per trovare gli utenti in qualsiasi punto. Per verificarlo, selezionare il pulsante **Sincronizza utenti** e aggiornare l'elenco di utenti. Provare ad aggiungere o a rimuovere un utente e quindi a risincronizzare l'elenco. Si noti però che l'app sincronizza solo la prima pagina di utenti.

## <a name="about-the-code"></a>Informazioni sul codice

Il codice pertinente per questo esempio si trova nei file seguenti:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: accesso iniziale. In particolare, le azioni sul controller hanno un attributo **Authorize**, che forza l'accesso dell'utente. L'applicazione usa il [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) per far accedere l'utente.
- **Controllers\SyncController.cs**: sincronizzazione dell'elenco di utenti con l'archivio in memoria locale.
- **Controllers\UserController.cs**: visualizzazione dell'elenco di utenti dall'archivio in memoria locale.
- **Controllers\AccountController.cs**: acquisizione delle autorizzazioni dall'amministratore del tenant tramite l'endpoint di consenso dell'amministratore.

## <a name="re-create-the-sample-app"></a>Ricreare l'app di esempio

1. In Visual Studio creare un nuovo progetto di **applicazione Web ASP.NET (.NET Framework)** in **Visual C#** . 
1. Nella schermata successiva scegliere il modello di progetto **MVC**. Aggiungere anche la cartella e i riferimenti principali per **API Web**, perché in seguito verrà aggiunto un controller API Web. Lasciare la modalità di autenticazione del progetto selezionata come impostazione predefinita: **Nessuna autenticazione**.
1. Selezionare il progetto nella finestra **Esplora soluzioni** e premere **F4**. 
1. Nelle proprietà del progetto impostare **SSL abilitato** su **True**. Prendere nota del valore di **URL SSL**. Sarà necessario quando si configura la registrazione di questa applicazione nel portale di Azure.
1. Aggiungere i seguenti pacchetti NuGet del middleware OWIN ASP.NET: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. Nella cartella **App_Start**:
   1. Creare una classe denominata **Startup.Auth.cs**. 
   1. Rimuovere **.App_Start** dal nome dello spazio dei nomi. 
   1. Sostituire il codice della classe **Startup** con il codice dello stesso file dell'app di esempio.       
   Assicurarsi di inserire l'intera definizione della classe. La definizione cambia da **public class Startup** a **public partial class Startup**.
1. In **Startup.Auth.cs** risolvere i riferimenti mancanti aggiungendo istruzioni **using** come suggerito da IntelliSense di Visual Studio.
1. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi**, quindi selezionare **Classe**.
1. Nella casella di ricerca immettere **OWIN**. Viene visualizzata l'opzione **Classe di avvio di OWIN**. Selezionarla e assegnare alla classe il nome **Startup.cs**.
1. In **Startup.cs** sostituire il codice della classe **Startup** con il codice dello stesso file dell'app di esempio. Anche in questo caso, la definizione cambia da **public class Startup** a **public partial class Startup**.
1. Nella cartella **Models** aggiungere una nuova classe denominata **MsGraphUser.cs**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiungere una nuova istanza di **Controller MVC 5 - Vuoto** denominata **AccountController**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiungere una nuova istanza di **Controller MVC 5 - Vuoto** denominata **UserController**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiungere una nuova istanza di **Controller Web API 2 - Vuoto** denominata **SyncController**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Per l'interfaccia utente, nella cartella **Views\Account** aggiungere tre istanze di **visualizzazioni vuote (senza modello)** denominate **GrantPermissions**, **Index** e **UserMismatch**. Aggiungerne una denominata **Index** nella cartella **Views\User**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiornare **Shared\_Layout.cshtml** e **Home\Index.cshtml** per collegare correttamente le varie visualizzazioni tra loro.

## <a name="deploy-the-sample-to-azure"></a>Distribuire l'esempio in Azure

Questo progetto include i progetti di app Web e API Web. Per distribuirli nei siti Web di Azure, seguire questi passaggi per ognuno:

1. Creare un sito Web di Azure.
1. Pubblicare l'app Web e le API Web nel sito Web.
1. Aggiornare i client per chiamare il sito Web invece di IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Creare e pubblicare dotnet-web-daemon-v2 in un sito Web di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nell'angolo in alto a sinistra della schermata selezionare **Crea una risorsa**.
1. Selezionare **Web** > **App Web** e quindi assegnare un nome al sito Web, ad esempio **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Selezionare le informazioni per la **sottoscrizione**, il **gruppo di risorse** e il **piano e la località del servizio app**. Per **Sistema operativo** scegliere **Windows** e per **Pubblica** scegliere **Codice**.
1. Selezionare **Crea** e attendere che venga creato il servizio app.
1. Quando si riceve la notifica **Distribuzione riuscita**, selezionare **Vai alla risorsa** per passare al servizio app appena creato.
1. Dopo aver creato il sito Web, individuarlo nel **Dashboard** e selezionarlo per aprire la schermata **Panoramica** del servizio app.
1. Nella scheda **Panoramica** del servizio app scaricare il profilo di pubblicazione selezionando il collegamento **Recupera profilo di pubblicazione**, quindi salvarlo. È possibile usare altri meccanismi di distribuzione, ad esempio dal controllo del codice sorgente.
1. Passare a Visual Studio e quindi:
   1. Passare al progetto **dotnet-web-daemon-v2**. 
   1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e quindi scegliere **Pubblica**.
   1. Selezionare **Importa profilo** sulla barra inferiore e importare il profilo di pubblicazione scaricato in precedenza.
1. Selezionare **Configura**.
1. Nella scheda **Connessione** aggiornare l'URL di destinazione in modo che usi "https". Ad esempio, usare [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Selezionare **Avanti**.
1. Nella scheda **Impostazioni** verificare che l'opzione **Abilita autenticazione a livello aziendale** sia deselezionata.  
1. Selezionare **Salva**. Selezionare **Pubblica** nella schermata principale.

Visual Studio pubblicherà il progetto e aprirà automaticamente un browser all'URL corrispondente. Se viene visualizzata la pagina Web predefinita del progetto, la pubblicazione ha avuto esito positivo.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aggiornare la registrazione dell'applicazione nel tenant di Azure AD per dotnet-web-daemon-v2

1. Tornare al [portale di Azure](https://portal.azure.com).
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app**.
1. Selezionare l'applicazione **dotnet-web-daemon-v2**.
1. Nella pagina **Autenticazione** per l'applicazione aggiornare i campi **URL di disconnessione** con l'indirizzo del servizio. Ad esempio, usare [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Nel menu **Personalizzazione** aggiornare il valore di **URL pagina iniziale** impostandolo sull'indirizzo del servizio. Ad esempio, usare [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Salvare la configurazione.
1. Aggiungere lo stesso URL nell'elenco di valori del menu **Autenticazione** > **URI di reindirizzamento**. Se sono presenti più URL di reindirizzamento, assicurarsi che vi sia una nuova voce che usa l'URI del servizio app per ognuno di essi.

## <a name="community-help-and-support"></a>Assistenza e supporto della community

Usare [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) per ottenere supporto dalla community.
Inviare prima le proprie domande a Stack Overflow ed esplorare i post esistenti per vedere se qualcuno ha già posto la stessa domanda.
Assicurarsi di aggiungere alle domande o ai commenti i tag "adal", "msal" e "dotnet".

Se nell'esempio si trova un bug, segnalarlo nella pagina di [problemi di GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se in MSAL.NET si trova un bug, segnalarlo nella pagina di [problemi di GitHub MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Per inviare suggerimenti, visitare la pagina [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sui diversi [flussi di autenticazione e scenari applicativi](authentication-flows-app-scenarios.md) supportati da Microsoft Identity Platform.

Per altre informazioni, vedere la documentazione concettuale seguente:

- [Tenancy in Azure Active Directory](single-and-multi-tenant-apps.md)
- [Informazioni sulle esperienze di consenso per applicazioni Azure AD](application-consent-experience.md)
- [Consentire l'accesso a qualsiasi utente di Azure Active Directory usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md)
- [Informazioni sul consenso dell'utente e dell'amministratore](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](app-objects-and-service-principals.md)
- [Guida introduttiva: Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)
- [Guida introduttiva: Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md)
- [Acquisizione di un token per un'applicazione con flussi di credenziali client](msal-client-applications.md)

Per un'applicazione daemon console multi-tenant più semplice, vedere l'[argomento di avvio rapido sul daemon .NET Core](quickstart-v2-netcore-daemon.md).
