---
title: Azure AD piattaforma universale di Windows (UWP XAML) Introduzione | Documenti Microsoft
description: Compilare app di Windows Store che si integrano con Azure AD per la registrazione e per chiamare le API protette di Azure AD tramite il protocollo OAuth.
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2282a59c9dd5d5d76a5b3e19f602e9d3dcc0b4ef
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-windows-universal-platform-uwpxaml-getting-started"></a>Azure AD piattaforma universale di Windows (UWP XAML) Guida introduttiva
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> I progetti di Windows Store 8.1 e della versione precedente non sono supportati in Visual Studio 2017.  Per altre informazioni, vedere [Selezione della piattaforma e compatibilità di Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Quando si sviluppano app per Windows Store, Azure Active Directory (Azure AD) semplifica e facilita l'autenticazione degli utenti con gli account Active Directory. Grazie all'integrazione con Azure AD, un'app può usare in modo sicuro qualsiasi API Web protetta da Azure AD, quali ad esempio le API di Office 365 o l'API di Azure.

Per le app desktop di Windows Store che devono accedere a risorse protette, Azure AD offre Active Directory Authentication Library (ADAL). L'unica funzione di ADAL è di semplificare l'acquisizione dei token di accesso da parte delle app. Per far capire quanto sia semplice, questo articolo illustra come compilare un'app di Windows Store denominata DirectorySearcher che:

* Ottiene i token di accesso per chiamare l'API Graph di Azure AD con il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Cerca in una directory gli utenti con un determinato nome dell'entità utente (UPN).
* Disconnette gli utenti.

## <a name="before-you-get-started"></a>Prima di iniziare
* Scaricare la [struttura di progetto](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) oppure l'[esempio completato](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Ognuno è una soluzione di Visual Studio 2015.
* È necessario disporre anche di un tenant di Azure AD in cui creare gli utenti e registrare l'app. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

Quando si è pronti, attenersi alle procedure descritte nelle tre sezioni seguenti.

## <a name="step-1-register-the-directorysearcher-app"></a>Passaggio 1. Registrare l'app DirectorySearcher
Per consentire all'app di ottenere i token, sarà prima necessario registrarla nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD. Ecco come:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra superiore fare clic sull'account. Nell'elenco **Directory** selezionare quindi il tenant di Active Directory in cui si vuole registrare l'app.
3. Fare clic su **Altri servizi** nel riquadro a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Seguire le istruzioni per creare un'**Applicazione client nativa**.
  * Il **nome** descrive l'app agli utenti.
  * L'**URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD usa per restituire le risposte dei token. Immettere per ora un valore segnaposto, ad esempio **http://DirectorySearcher**. Si sostituirà il valore in un secondo momento.
6. Dopo aver completato la registrazione, Azure AD assegna automaticamente all'app un ID applicazione univoco. Copiare il valore nella scheda **Applicazione**, poiché sarà necessario in un secondo momento.
7. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**.
8. Per l'app di **Azure Active Directory**, selezionare **Microsoft Graph** come API.
9. In **Autorizzazioni delegate** aggiungere l'autorizzazione **Accesso alla directory come utente connesso**. Ciò consente all'app di eseguire query nell'API Graph per gli utenti.

## <a name="step-2-install-and-configure-adal"></a>Passaggio 2. Installare e configurare ADAL
Ora che si dispone di un'app in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità. Per abilitare ADAL alla comunicazione con Azure AD, fornire alcune informazioni sulla registrazione dell'app.

1. Aggiungere ADAL al progetto DirectorySearcher usando la Console di Gestione pacchetti.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. Nel progetto DirectorySearcher aprire MainPage.xaml.cs.
3. Sostituire i valori nell'area **Config Values** con i valori immessi nel portale di Azure. Il codice fa riferimento a questi valori ogni volta che usa ADAL.
  * Il *tenant* è il dominio del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
  * Il *clientId* è l'ID client dell'app. Lo si copia dal portale.
4. A questo punto è necessario individuare l'URI di callback per l'app di Windows Store. Impostare un punto di interruzione in questa riga del metodo `MainPage` :
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Compilare la soluzione assicurandosi che vengano ripristinati tutti i riferimenti del pacchetto. Se mancano pacchetti, aprire Gestione pacchetti NuGet e ripristinarli.
6. Eseguire l'app e prendere nota del valore di `redirectUri` quando viene raggiunto il punto di interruzione. Il valore dovrebbe essere simile al seguente:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. Tornare nella scheda **Impostazioni** dell'app nel portale di Azure e sostituire il valore di **RedirectUri** con questo valore.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Passaggio 3. Usare ADAL per ottenere i token da Azure AD
Il principio alla base di ADAL è che ogni volta che l'app ha bisogno di un token di accesso, deve solo chiamare `authContext.AcquireToken(…)` e ADAL fa il resto.  

1. Inizializzare la classe `AuthenticationContext` dell'app, che è la classe principale di ADAL. Questa azione passa ad ADAL le coordinate necessarie per comunicare con Azure AD e gli indica come memorizzare i token nella cache.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Individuare il metodo `Search(...)`, che viene richiamato quando l'utente sceglie il pulsante **Cerca** nell'interfaccia utente dell'app. Questo metodo invia una richiesta GET all'API Graph di Azure AD per eseguire una query sugli utenti il cui UPN inizia con il termine di ricerca specificato. Per eseguire una query nell'API Graph, includere un token di accesso nell'intestazione **Authorization** della richiesta. È qui che entra in gioco ADAL.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Quando l'app richiede un token mediante la chiamata a `AcquireTokenAsync(...)`, ADAL tenta di restituire un token senza chiedere le credenziali all'utente. Se ADAL determina che l'utente deve eseguire l'accesso per ottenere un token, visualizza una finestra di dialogo di accesso, raccoglie le credenziali dell'utente e restituisce un token al termine dell'autenticazione. Se per qualsiasi motivo ADAL non può restituire un token, lo stato di *AuthenticationResult* sarà un errore.
3. È ora di usare il token di accesso appena acquisito. Nel metodo `Search(...)` associare il token alla richiesta get dell'API Graph nell'intestazione **Authorization**:

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. È possibile usare l'oggetto `AuthenticationResult` anche per visualizzare informazioni sull'utente nell'app, ad esempio l'ID dell'utente:

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. È inoltre possibile usare ADAL per disconnettere gli utenti dall'app. Quando l'utente fa clic sul pulsante **Sign Out** (Disconnetti), è opportuno assicurarsi che la chiamata successiva a `AcquireTokenAsync(...)` mostri una vista di accesso. Con ADAL, questa azione è facile quanto cancellare la cache dei token:

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>Passaggi successivi
È stata compilata un'app di Windows Store in grado di autenticare gli utenti, di chiamare in modo sicuro le API Web usando OAuth 2.0 e di ottenere le informazioni di base sull'utente.

Se non è stato ancora popolato il tenant con degli utenti, ora è possibile farlo.
1. Eseguire l'app DirectorySearcher e accedere usando le credenziali di uno di questi utenti.
2. Cercare altri utenti in base al relativo UPN.
3. Chiudere l'app e rieseguirla. Si noti che la sessione dell'utente non è stata modificata.
4. Disconnettersi facendo clic con il pulsante destro del mouse per visualizzare la barra in basso e quindi accedere nuovamente con le credenziali di un altro utente.

ADAL consente di incorporare facilmente nell'app tutte queste funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, visualizzazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti. È necessario conoscere una sola chiamata API, `authContext.AcquireToken*(…)`.

Per riferimento, scaricare l'[esempio completato](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip), che non contiene i valori di configurazione immessi durante la procedura sopra indicata.

Ora è possibile passare ad altri scenari relativi alle identità. Provare ad esempio [Proteggere un'API Web .NET con Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
