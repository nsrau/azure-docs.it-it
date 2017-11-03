---
title: 'Azure Active Directory B2C: Personalizzare l''interfaccia utente di Azure AD B2C dinamicamente usando criteri personalizzati'
description: "Supportare più esperienze di personalizzazione con contenuto HTML5/CSS che cambia in modo dinamico in fase di esecuzione."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: fffb6c82b2e04976c420fba07bbcf967ffd25929
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Configurare l'interfaccia utente con contenuto dinamico usando criteri personalizzati
I criteri personalizzati di Azure Active Directory B2C (Azure AD B2C) consentono di inviare un parametro in una stringa di query. Passando il parametro all'endpoint HTML, è possibile modificare dinamicamente il contenuto della pagina. È ad esempio possibile modificare l'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C in base a un parametro passato dall'applicazione Web o per dispositivi mobili. 

## <a name="prerequisites"></a>Prerequisiti
Questo articolo descrive come personalizzare l'interfaccia utente di Azure AD B2C con *contenuto dinamico* usando criteri personalizzati. Per iniziare, vedere [Configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>L'articolo di Azure AD B2C, [Configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato](active-directory-b2c-ui-customization-custom.md), descrive i concetti di base seguenti:
> * Funzionalità di personalizzazione dell'interfaccia utente della pagina.
> * Strumenti essenziali per testare la funzionalità di personalizzazione dell'interfaccia utente della pagina usando il contenuto di esempio.
> * Elementi principali dell'interfaccia utente per ogni tipo di pagina.
> * Procedure consigliate per l'applicazione di questa funzionalità.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Aggiungere un collegamento ai modelli HTML5/CSS per il percorso utente

Nei criteri personalizzati una definizione del contenuto indica l'URI della pagina HTML5 usato per un passaggio dell'interfaccia utente specifico, ad esempio per le pagine di accesso o di iscrizione. I criteri di base definiscono l'aspetto predefinito puntando a un URI di file HTML5 (in CSS). Nei criteri di estensione è possibile modificare l'aspetto eseguendo l'override dell'elemento LoadUri per il file HTML5. Le definizioni del contenuto includono URL a contenuto esterno definito tramite la creazione di file HTML5/CSS, in base alle esigenze. 

La sezione `ContentDefinitions` contiene una serie di elementi XML `ContentDefinition`. L'attributo ID dell'elemento `ContentDefinition` specifica il tipo di pagina correlato alla definizione del contenuto. Ciò significa che l'elemento definisce il contesto che verrà applicato da un modello HTML5/CSS personalizzato. La tabella seguente descrive il set di ID di definizioni del contenuto, riconosciuti dal motore IEF (Identity Experience Framework, Framework dell'esperienza di gestione delle identità) e i tipi di pagina correlati.

| ID definizione del contenuto | Modello HTML5 predefinito| Descrizione | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Pagina di selezione del provider di identità**. Questa pagina elenca i provider di identità tra cui gli utenti possono scegliere durante la procedura di accesso. Si tratta in genere di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selezione del provider di identità per l'iscrizione**. Questa pagina elenca i provider di identità tra cui gli utenti possono scegliere durante la procedura di iscrizione. Si tratta di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina Password dimenticata**. Questa pagina contiene un modulo che gli utenti devono compilare per avviare la reimpostazione della password.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di accesso dell'account locale**. Questa pagina contiene un modulo per eseguire l'accesso con un account locale basato su un indirizzo di posta elettronica o un nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo per eseguire l'iscrizione a un account locale basato su un indirizzo di posta elettronica o un nome utente. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo che gli utenti devono completare quando si iscrivono usando un account esistente di un provider di identità basato su social network. Questa pagina è simile alla pagina di iscrizione dell'account di social networking illustrata in precedenza, a eccezione dei campi di immissione della password. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo a cui gli utenti possono accedere per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social networking, a eccezione dei campi di immissione della password. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina gestisce il processo di iscrizione e quello di accesso degli utenti. Gli utenti possono usare provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook o Google +, o account locali.  |

## <a name="serving-dynamic-content"></a>Gestione del contenuto dinamico
L'articolo [Configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato](active-directory-b2c-ui-customization-custom.md) descrive il caricamento dei file HTML5 in Archiviazione BLOB di Azure. Tali file HTML5 sono statici ed eseguono il rendering dello stesso contenuto HTML per ogni richiesta. 

In questo articolo si usa un'app Web ASP.NET, che può accettare parametri di stringa di query e rispondere di conseguenza. 

Questa procedura dettagliata è costituita dai passaggi seguenti:
* Creare un'applicazione Web ASP.NET Core che ospita i modelli HTML5. 
* Aggiungere un modello HTML5 personalizzato, _unified.cshtml_. 
* Pubblicare l'app Web in Servizio app di Azure. 
* Impostare la condivisione di risorse tra le origini (CORS) per l'app Web.
* Eseguire l'override degli elementi `LoadUri` in modo da puntare al file HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Passaggio 1: Creare un'app Web ASP.NET

1. In Visual Studio creare un progetto selezionando **File** > **Nuovo** > **Progetto**.

2. Nella finestra **Nuovo progetto** selezionare **Visual C#** > **Web** > **Applicazione Web ASP.NET Core (.NET Core)**.

3. Assegnare un nome all'applicazione, ad esempio *Contoso.AADB2C.UI*, e quindi selezionare **OK**.

    ![Creare un nuovo progetto di Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Selezionare il modello **Applicazione Web**.

5. Impostare l'autenticazione su **Nessuna autenticazione**.

    ![Selezionare il modello Applicazione Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Selezionare **OK** per creare il progetto.

## <a name="step-2-create-mvc-view"></a>Passaggio 2: Creare una visualizzazione MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Passaggio 2.1: Scaricare il modello HTML5 integrato di B2C
Il modello HTML5 personalizzato si basa sul modello HTML5 integrato di Azure AD B2C. È possibile scaricare il [file unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) oppure eseguire il download del modello dal [pacchetto iniziale](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Questo file HTML5 può essere usato per una pagina di iscrizione o di accesso unificata.

### <a name="step-22-add-the-mvc-view"></a>Passaggio 2.2: Aggiungere una visualizzazione MVC
1. Fare clic con il pulsante destro del mouse sulla cartella Views/Home e quindi scegliere **Aggiungi** > **Nuovo elemento**.

    ![Aggiungere un nuovo elemento MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Nella finestra **Aggiungi nuovo elemento - Contoso.AADB2C.UI** selezionare **Web > ASP.NET**.

3. Selezionare **Pagina visualizzazione MVC**.

4. Nella casella **Nome** modificare il nome in **unified.cshtml**.

5. Selezionare **Aggiungi**.

    ![Aggiungere una visualizzazione MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Se il file *unified.cshtml* non è già aperto, fare doppio clic su di esso per aprirlo e quindi cancellarne i contenuti.

7. Per questa procedura dettagliata, viene rimosso il riferimento alla pagina di layout. Aggiungere il frammento di codice seguente al file _unified.cshtml_:

    ```C#
    @{
        Layout = null;
    }
    ```

8. Aprire il file _unified.cshtml_ scaricato dal modello HTML5 integrato di Azure AD B2C.

9. Sostituire la chiocciola singola (@) con una chiocciola doppia (@@).

10. Copiare il contenuto del file e incollarlo sotto la definizione del layout. Il codice dovrebbe essere simile al seguente:

    ![File unified.cshtml dopo l'aggiunta del codice HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Passaggio 2.3: Modificare l'immagine di sfondo

Individuare l'elemento `<img>` contenente il valore `ID` *background_background_image* e quindi sostituire il valore `src` con **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** o qualsiasi altra immagine di sfondo da usare.

![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Passaggio 2.4: Aggiungere la visualizzazione al controller MVC

1. Aprire **Controllers\HomeController.cs** e aggiungere il metodo seguente: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Questo codice specifica che il metodo deve usare un file di modello di *visualizzazione* per eseguire il rendering di una risposta nel browser. Poiché il nome del file di modello di *visualizzazione* non viene specificato in modo esplicito, per impostazione predefinita MVC usa il file di visualizzazione _unified.cshtml_ presente nella cartella */Views/Home*.
    
    Dopo aver aggiunto il metodo _unified_, il codice dovrebbe essere simile al seguente:
    
    ![Modificare il controller per eseguire il rendering della visualizzazione](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Eseguire il debug dell'app Web e verificare che la pagina _unified_ sia accessibile (ad esempio, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Passaggio 2.5: Pubblicare in Azure
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.UI** e quindi scegliere **Pubblica**.

    ![Pubblicare in Servizio app di Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Selezionare il riquadro **Servizio app di Microsoft Azure** e quindi **Pubblica**.

    ![Creare un nuovo Servizio app di Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Verrà visualizzata la finestra **Crea servizio app**. Qui è possibile iniziare a creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

    > [!NOTE]
    > Per altre informazioni sulla pubblicazione, vedere [Creare un'app Web ASP.NET in Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Nella casella **Nome app Web** digitare un nome univoco per l'app. I caratteri validi sono a-z, A-Z, 0-9 e il trattino (-). L'URL dell'app Web è `http://<app_name>.azurewebsites.NET`, dove `<app_name>` è il nome dell'app Web. È possibile accettare il nome generato automaticamente, che è univoco.

4. Selezionare **Crea** per avviare la creazione delle risorse di Azure.

    ![Specificare le proprietà del servizio app](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Al termine del processo di creazione, la procedura guidata pubblica l'app Web ASP.NET in Azure e quindi avvia l'app nel browser predefinito.

5. Copiare l'URL della pagina _unified_ (ad esempio, _https://<nome_app>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Passaggio 3: Configurare CORS in Servizio app di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Servizi app** e quindi selezionare il nome dell'app per le API.

    ![Selezionare l'app per le API nel portale di Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Nella sezione **Impostazioni**, in **API**, selezionare **CORS**.

    ![Selezionare le impostazioni CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Nella finestra **CORS**, nella casella **Origini consentite**, eseguire una di queste operazioni:

    * Immettere l'URL o gli URL da cui consentire le chiamate JavaScript.
    * Immettere un asterisco (*) per specificare che vengono accettati tutti i domini di origine.

4. Selezionare **Salva**.

    ![Finestra CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Dopo aver selezionato **Salva**, l'app per le API accetterà le chiamate JavaScript dagli URL specificati. 

## <a name="step-4-html5-template-validation"></a>Passaggio 4: Convalidare il modello HTML5
Il modello HTML5 è pronto per l'uso. Non è tuttavia disponibile nel codice `ContentDefinition`. Prima di poter aggiungere `ContentDefinition` ai criteri personalizzati, verificare che:
* Il contenuto sia conforme a HTML5 e accessibile.
* Il server di contenuti sia abilitato per CORS.

    >[!NOTE]
    >Per verificare che CORS sia abilitato per il sito in cui si ospita il contenuto e che il sito possa testare le richieste CORS, passare al sito Web [test-cors.org](http://test-cors.org/). 

* Il contenuto gestito sia protetto tramite **HTTPS**.
* Vengano usati *URL assoluti*, ad esempio *https://dominio/contenuto* per tutti i collegamenti, i contenuti CSS e le immagini.

## <a name="step-5-configure-your-content-definition"></a>Passaggio 5: Configurare la definizione del contenuto
Per configurare `ContentDefinition`, eseguire questa procedura:
1. Aprire il file di base dei criteri, ad esempio *TrustFrameworkBase.xml*.

2. Cercare l'elemento `<ContentDefinitions>` e quindi copiare l'intero contenuto del nodo `<ContentDefinitions>`.

3. Aprire il file di estensione, ad esempio *TrustFrameworkExtensions.xml*, e quindi cercare l'elemento `<BuildingBlocks>`. Se l'elemento non esiste, aggiungerlo.

4. Incollare l'intero contenuto del nodo `<ContentDefinitions>` copiato come figlio dell'elemento `<BuildingBlocks>`. 

5. Cercare il nodo `<ContentDefinition>` che contiene `Id="api.signuporsignin"` nel codice XML copiato.

6. Modificare il valore di `LoadUri` da _~/tenant/default/unified_ a _https://<nome_app>.azurewebsites.net/home/unified_.  
    I criteri personalizzati dovrebbero essere simili ai seguenti:
    
    ![Definizione del contenuto](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Passaggio 6: Caricare i criteri nel tenant
1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi selezionare **Azure AD B2C**.

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Selezionare **Tutti i criteri**.

4. Selezionare **Carica criteri**.

5. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.

6. Caricare il file *TrustFrameworkExtensions.xml* e assicurarsi che superi la convalida.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Passaggio 7: Testare i criteri personalizzati tramite Esegui adesso
1. Selezionare **Impostazioni di Azure AD B2C** e quindi **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >Il comando Esegui adesso richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza, quindi selezionare **Esegui adesso**.  
    Dovrebbe essere possibile vedere il codice HTML5 personalizzato con lo sfondo creato in precedenza.

    ![Criteri di iscrizione o di accesso](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Passaggio 8: Aggiungere contenuto dinamico
Modificare lo sfondo in base a un parametro di stringa di query denominato _campaignId_. L'applicazione relying party (app Web o per dispositivi mobili) invia il parametro ad Azure AD B2C. I criteri leggono il parametro e ne inviano il valore al modello HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Passaggio 8.1: Aggiungere un parametro di definizione del contenuto

Aggiungere l'elemento `ContentDefinitionParameters` eseguendo questa procedura:
1. Aprire il file *SignUpOrSignin* dei criteri, ad esempio *SignUpOrSignin.xml*.

2. Cercare il nodo `<DefaultUserJourney>`. 

3. Nel nodo `<DefaultUserJourney>` aggiungere il frammento di codice XML seguente:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Passaggio 8.2: Modificare il codice per accettare un parametro di stringa di query e sostituire l'immagine di sfondo
Modificare il metodo `unified` di HomeController in modo che accetti il parametro campaignId. Il metodo controlla quindi il valore del parametro e imposta la variabile `ViewData["background"]` di conseguenza.

1. Aprire il file *Controllers\HomeController.cs* e quindi modificare il metodo `unified` aggiungendo il frammento di codice seguente:

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Individuare l'elemento `<img>` con ID `background_background_image` e sostituire il valore `src` con `@ViewData["background"]`.

    ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>Passaggio 8.3: Caricare le modifiche e pubblicare i criteri
1. Pubblicare il progetto di Visual Studio in Servizio app di Azure.

2. Caricare i criteri *SignUpOrSignin.xml* in Azure AD B2C.

3. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza, quindi selezionare **Esegui adesso**.  
    L'immagine di sfondo visualizzata dovrebbe essere uguale a quella precedente.

4. Copiare l'URL dalla barra degli indirizzi del browser.

5. Aggiungere il parametro di stringa di query _campaignId_ all'URI. Aggiungere ad esempio `&campaignId=hawaii`, come illustrato nella figura seguente:

    ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Premere **INVIO** per visualizzare l'immagine di sfondo delle Hawaii.

    ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Modificare il valore in *Tokyo* e quindi premere **INVIO**.  
    Nel browser viene visualizzato lo sfondo di Tokyo.

    ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Passaggio 9: Modificare il resto del percorso utente
Se si seleziona il collegamento **Iscriviti ora** nella pagina di accesso, il browser visualizza l'immagine di sfondo predefinita e non quella specificata. Questo comportamento è dovuto al fatto che è stata modificata solo la pagina di iscrizione o di accesso. Per modificare il resto delle definizioni di contenuto autocertificate:
1. Tornare al "Passaggio 2" ed eseguire questa procedura:

    a. Scaricare il file *selfasserted*.

    b. Copiare il contenuto del file.

    c. Creare una nuova visualizzazione, *selfasserted*.

    d. Aggiungere *selfasserted* al controller **Home**.

2. Tornare al "Passaggio 4" ed eseguire questa procedura: 

    a. Nei criteri di estensione trovare il nodo `<ContentDefinition>` che contiene `Id="api.selfasserted"`, `Id="api.localaccountsignup"` e `Id="api.localaccountpasswordreset"`.

    b. Impostare l'attributo `LoadUri` sull'URI *selfasserted*.

3. Tornare al "Passaggio 8.2" e modificare il codice per accettare i parametri di stringa di query, ma questa volta per la funzione *selfasserted*. 

4. Caricare i criteri *TrustFrameworkExtensions.xml* e assicurarsi che superino la convalida.

5. Eseguire il test dei criteri e selezionare **Iscriviti ora** per visualizzare il risultato.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facoltativo) Scaricare il codice e i file dei criteri completi
* Dopo aver completato la procedura [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), è consigliabile usare file di criteri personalizzati per definire scenari specifici. Per riferimento, sono disponibili [file di criteri di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* È possibile scaricare il codice completo dalla [soluzione di Visual Studio di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




