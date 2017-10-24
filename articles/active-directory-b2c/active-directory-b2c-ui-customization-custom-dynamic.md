---
title: 'Azure Active Directory B2C: personalizzare l''interfaccia utente di Azure AD B2C dinamicamente usando criteri personalizzati'
description: "Come supportare più esperienze di personalizzazione con contenuto HTML/CSS che cambia in modo dinamico in fase di esecuzione"
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
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C: configurare l'interfaccia utente con contenuto dinamico usando criteri personalizzati
I criteri personalizzati di Azure Active Directory B2C consentono di inviare elementi tramite un parametro in una stringa di query. Tale parametro passa nell'endpoint HTML e può modificare dinamicamente il contenuto della pagina. È possibile ad esempio modificare l'immagine di sfondo della pagina di accesso o di iscrizione B2C in base a un parametro passato dall'applicazione Web o per dispositivi mobili. 

## <a name="prerequisites"></a>Prerequisiti
Questo articolo descrive come personalizzare l'interfaccia utente di Azure AD B2C con **contenuto dinamico** usando criteri personalizzati. Per iniziare a eseguire questa operazione usando criteri personalizzati, leggere l'articolo [Azure Active Directory B2C: configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>
>  L'articolo [Azure Active Directory B2C: configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato](active-directory-b2c-ui-customization-custom.md) descrive i concetti di base della personalizzazione dell'interfaccia utente di Azure AD B2C con criteri personalizzati:
> * Funzionalità di personalizzazione dell'interfaccia utente della pagina.
> * Strumento che consente di testare la funzionalità di personalizzazione dell'interfaccia utente della pagina usando il contenuto di esempio.
> * Elementi principali dell'interfaccia utente in ogni tipo di pagina.
> * Procedure consigliate per l'utilizzo di questa funzionalità.

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>Aggiunta di un collegamento ai modelli HTML5/CSS per il percorso utente

Nei criteri personalizzati una definizione del contenuto indica l'URI della pagina HTML5 usato per un passaggio dell'interfaccia utente specifico, ad esempio le pagine di iscrizione o di accesso. I criteri di base definiscono l'aspetto predefinito puntando a un URI di file HTML5 (che fa riferimento al file CSS relativo). Nei criteri di estensione è possibile modificare l'aspetto sovrascrivendo l'elemento LoadUri per tale file HTML5. Le definizioni del contenuto includono pertanto URL a contenuto esterno definito tramite la creazione di file HTML5/CSS, in base alle esigenze. 

La sezione `ContentDefinitions` contiene una serie di elementi XML `ContentDefinition`. L'attributo ID dell'elemento `ContentDefinition` specifica il tipo di pagine correlato alla definizione del contenuto. In tal modo verrà usato il contesto in cui è presente un modello HTML5/CSS personalizzato. La tabella seguente descrive il set di ID di definizioni del contenuto, riconosciuti dal motore di framework dell'esperienza di gestione delle identità, e il tipo di pagine correlate a tali ID.

| ID definizione del contenuto | Modello HTML5 predefinito| Descrizione | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Pagina di selezione del provider di identità**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di accesso. Si tratta tipicamente di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selezione del provider di identità per l'iscrizione**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di iscrizione. Si tratta di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina Password dimenticata**. Questa pagina contiene un modulo che l'utente deve compilare per avviare la reimpostazione della password.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di accesso dell'account locale**. Questa pagina contiene un modulo per eseguire l'accesso con un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo per eseguire l'iscrizione a un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo di iscrizione che gli utenti devono completare usando un account esistente da un provider di identità basato su social network. Questa pagina è simile alla pagina di iscrizione dell'account di social network descritta in precedenza, ad eccezione dei campi di immissione della password. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo che gli utenti possono usare per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social network, ad eccezione dei campi di immissione della password. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina gestisce sia l'iscrizione che l'accesso degli utenti che possono usare i provider di identità aziendali, i provider di identità basati su social network, ad esempio Facebook o Google +, o gli account locali.  |

## <a name="serving-dynamic-content"></a>Gestione del contenuto dinamico
L'articolo precedente [Azure Active Directory B2C: configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato](active-directory-b2c-ui-customization-custom.md) descrive il caricamento dei file HTML5 in Archiviazione BLOB di Azure. Tali file HTML5 sono statici ed eseguono il rendering dello stesso contenuto HTML per ogni richiesta. In questo articolo si usa l'app Web ASP.Net, che può accettare parametri di stringa di query e rispondere di conseguenza. Questa procedura dettagliata è costituita dai passaggi seguenti:
* Creare un'applicazione Web ASP.NET Core che ospita il modello HTML5 
* Aggiungere il modello HTML5 _unified.cshtml_ personalizzato 
* Pubblicare l'app Web in Servizio app di Azure 
* Impostare CORS per l'app Web
* Eseguire l'override degli elementi `LoadUri` in modo da puntare al file HTML5

## <a name="step-1-create-an-aspnet-web-app"></a>Passaggio 1: Creare un'app Web ASP.NET

1.  In Visual Studio creare un progetto selezionando **File > Nuovo > Progetto**.
2.  Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Web > Applicazione Web ASP.NET Core (.NET Core)**.
3.  Assegnare un nome all'applicazione, ad esempio Contoso.AADB2C.UI, e quindi selezionare **OK**.

    ![Creare un nuovo progetto di Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  Selezionare il modello **Applicazione Web**
5.  Verificare che l'autenticazione sia impostata su **Nessuna autenticazione**

    ![Selezionare il modello API Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  Fare clic su **OK** per creare il progetto

## <a name="step-2-create-mvc-view"></a>Passaggio 2: Creare una visualizzazione MVC
### <a name="step-21-download-b2c-built-in-html5-template"></a>Passaggio 2.1 Scaricare codice B2C compilato nel modello HTML5
Il modello HTML5 personalizzato si basa sul codice B2C compilato nel modello HTML5. È possibile scaricare il file [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) oppure eseguire il download dal [pacchetto Starter](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Usare questo file HTML5 per una pagina di iscrizione o di accesso unificata.

### <a name="step-22-add-mvc-view"></a>Passaggio 2.2 Aggiungere una visualizzazione MVC
1. Fare clic con il pulsante destro del mouse sulla cartella Visualizzazioni/Home e quindi scegliere **Aggiungi > Nuovo elemento**.
 ![Aggiungere un nuovo elemento MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. Nella finestra di dialogo **Aggiungi nuovo elemento - Contoso.AADB2C.UI** selezionare **Web > ASP > NET**
3. Toccare **Pagina visualizzazione MVC**
4. Nella casella **Nome** modificare il nome in _unified.cshtml_.
5. Fare clic su **Aggiungi**
 ![Aggiungere una visualizzazione MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)
6.  Se non è già aperto, fare doppio clic sul file per aprirlo. Cancellare il contenuto del file _unified.cshtml_.
7. A scopo dimostrativo, il riferimento alla pagina di layout è stato rimosso. Aggiungere il frammento di codice seguente al file _unified.cshtml_

    ```C#
    @{
        Layout = null;
    }
    ```

8. Aprire il file _unified.cshtml_ scaricato dal codice AAD B2C compilato nel modello HTML5.
9. Trovare i simboli `@` e sostituirli con `@@`
10. Copiare il contenuto del file e incollarlo sotto la definizione del layout. Il codice dovrebbe essere analogo al seguente: ![File unified.cshtml dopo l'aggiunta del file HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>Passaggio 2.3 Modificare l'immagine di sfondo
10. Individuare l'elemento `<img>` con ID `background_background_image` e sostituire l'elemento `src` con _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ o con una qualsiasi immagine di sfondo desiderata.
![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>Passaggio 2.4 Aggiungere la visualizzazione al controller MVC
Aprire **Controllers\HomeController.cs** e aggiungere il metodo seguente. 
```C
public IActionResult unified()
{
    return View();
}
```
Questo codice specifica che il metodo deve usare un file di modello di visualizzazione per eseguire il rendering di una risposta nel browser. Poiché il nome del file di modello di visualizzazione non viene specificato in modo esplicito, per impostazione predefinita MVC usa il file di visualizzazione _unified.cshtml_ presente nella cartella /Visualizzazioni/Home.

Dopo aver aggiunto il metodo _unified_, il codice dovrebbe essere analogo al seguente: ![Modificare il controller per eseguire il rendering della visualizzazione](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

Eseguire il debug dell'app Web e quindi verificare che la pagina _unified_ sia accessibile. Ad esempio, `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>Passaggio 2.5 Pubblicare in Azure
1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.UI** e quindi scegliere **Pubblica**.

    ![Pubblicare in Servizio app di Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  Verificare che **Servizio app di Microsoft Azure** sia selezionato e scegliere **Pubblica**.

    ![Creare un nuovo Servizio app di Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    La finestra di dialogo **Crea servizio app** consente di creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

> [!NOTE]
>
>Per altre informazioni sulle modalità di pubblicazione, vedere [Creare un'app Web ASP.NET in Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  In **Nome app Web** digitare un nome univoco per l'app (i caratteri validi sono `a-z`, `0-9` e `-`). L'URL dell'app Web è `http://<app_name>.azurewebsites.NET`, dove `<app_name>` è il nome dell'app Web. È possibile accettare il nome generato automaticamente, che è univoco.

    Selezionare **Crea** per avviare la creazione delle risorse di Azure.

    ![Specificare le proprietà del servizio app](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  Dopo che la procedura guidata è stata completata, l'app Web ASP.NET viene pubblicata in Azure e quindi avviata nel browser predefinito. Copiare l'URL della pagina _unified_, ad esempio _https://<app_name>.azurewebsites.net/home/unified_

## <a name="step-3-configure-cors-in-azure-app-service"></a>Passaggio 3: Configurare CORS in Servizio app di Azure
1. In un browser passare al [portale di Azure](https://portal.azure.com/)

2. Fare clic su **Servizi app**e quindi sul nome app per le API.

    ![Selezionare un'app per le API nel portale](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. Nella sezione **Impostazioni** scorrere verso il basso, trovare la sezione **API** e quindi fare clic su **CORS**.

    ![Selezionare le impostazioni CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. Nella casella di testo immettere l'URL o gli URL da cui consentire le chiamate JavaScript
oppure immettere un asterisco (*) per specificare che tutti i domini di origine vengono accettati.

5. Fare clic su **Salva**.

    ![Fare clic su Salva.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

Dopo aver fatto clic su Salva, l'app per le API accetterà le chiamate JavaScript dagli URL specificati. 

## <a name="step-4-html5-template-validation"></a>Passaggio 4: Convalidare il modello HTML5
Il modello HTML5 è pronto per essere usato,  ma non è ancora disponibile in alcun elemento `ContentDefinition`. Prima di aggiungere l'elemento `ContentDefinition` ai criteri personalizzati, è necessario eseguire queste operazioni:
* Assicurarsi che il contenuto sia conforme a HTML5 e accessibile
* Assicurarsi che il server di contenuti sia abilitato per CORS.

>[!NOTE]
>
>Per verificare che CORS sia abilitato per il sito su cui si ospita il contenuto e per testare le richieste CORS, è possibile usare il sito http://test-cors.org/. 

* Verificare che il contenuto gestito sia protetto tramite **HTTPS**.
* Usare **URL assoluti**, ad esempio https://yourdomain/content per tutti i collegamenti e per tutti i contenuti e immagini CSS.

## <a name="step-5-configure-your-content-definition"></a>Passaggio 5: Configurare la definizione del contenuto
Per configurare l'elemento `ContentDefinition`
1.  Aprire il file di base dei criteri, ad esempio TrustFrameworkBase.xml.
2.  Trovare l'elemento `<ContentDefinitions>` e copiare l'intero contenuto del nodo `<ContentDefinitions>`.
3.  Aprire il file di estensione, ad esempio TrustFrameworkExtensions.xml, e trovare l'elemento `<BuildingBlocks>`. Se l'elemento non esiste, aggiungerne uno.
4.  Incollare l'intero contenuto del nodo `<ContentDefinitions>` copiato come figlio dell'elemento `<BuildingBlocks>`. 
5.  Trovare il nodo `<ContentDefinition>` che include `Id="api.signuporsignin"` nel codice XML copiato.
6.  Modificare il valore di `LoadUri` da _~/tenant/default/unified_ a _https://app_name.azurewebsites.net/home/unified_. I criteri personalizzati dovrebbero essere simili ai seguenti: ![Definizione del contenuto](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Passaggio 6: Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire **Azure AD B2C**
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Aprire **Tutti i criteri**.
4.  Selezionare **Carica criteri**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Passaggio 7: Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >
    >    Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. 
    >    Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).
    >

2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  A questo punto dovrebbe essere possibile visualizzare il codice HTML5 personalizzato con lo sfondo creato in precedenza. ![Criteri di iscrizione o di accesso](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>Passaggio 8: Aggiungere contenuto dinamico
In questa sezione viene modificato lo sfondo in base a un parametro di stringa di query denominato _campaignId_. L'applicazione relying party (app Web o per dispositivi mobili) invia il parametro ad AAD B2C. I criteri leggono il parametro e ne inviano il valore al modello HTML5. 


### <a name="step-81-adding-content-definition-parameter"></a>Passaggio 8.1 Aggiungere un parametro di definizione del contenuto

Per aggiungere l'elemento `ContentDefinitionParameters`:
1.  Aprire il file SignUpOrSignin dei criteri, ad esempio SignUpOrSignin.xml.
2.  Trovare il nodo `<UserJourneyBehaviors>`. 
4.  Aggiungere il frammento di codice XML seguente in `<UserJourneyBehaviors>`. 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>Passaggio 8.2 Modificare il codice per accettare il parametro di stringa di query e sostituire l'immagine di sfondo di conseguenza
In questa sezione viene modificato il metodo _unified_ di HomeController in modo che accetti il parametro campaignId. Il metodo ne controlla quindi il valore e imposta la variabile `ViewData["background"]` di conseguenza.

1. Aprire **Controllers\HomeController.cs** e modificare il metodo `unified` con il frammento di codice seguente:

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

2. Individuare l'elemento `<img>` con ID `background_background_image` e sostituire `src` con `@ViewData["background"]`.

    ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>Passaggio 8.3 Caricare le modifiche e pubblicare i criteri
1. Pubblicare il progetto di Visual Studio in Servizio app di Azure.
2. Caricare i criteri SignUpOrSignin.xml in AAD B2C.
3.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**. L'immagine di sfondo visualizzata dovrebbe essere uguale a quella precedente.
4. Copiare l'URL dalla barra degli indirizzi del browser.
5. Aggiungere il parametro di stringa di query _campaignId_ all'URI. Aggiungere ad esempio `&campaignId=hawaii`, come illustrato nella figura seguente. ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. Premere **Invio** per visualizzare la pagina con le Hawaii come sfondo. ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. A questo punto modificare il valore in *Tokyo* e quindi premere **Invio**. La pagina presenta ora Tokio come sfondo. ![Modificare lo sfondo della pagina](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Passaggio 9: Modificare il resto del percorso utente
Se si passa alla pagina di accesso e si fa clic sul collegamento **Iscriversi adesso**, viene visualizzato lo sfondo predefinito, non quello definito dall'utente. Questo comportamento è dovuto al fatto che è stata modificata solo la pagina di iscrizione o di accesso, ma è necessario modificare anche il resto delle definizioni di contenuto per l'asserzione autonoma. A tale scopo, eseguire questi passaggi:
* Nel Passaggio 2:
    * Scaricare il file **selfasserted**.
    * Copiare il contenuto del file.
    * Creare una nuova visualizzazione **selfasserted**.
    * Aggiungere **selfasserted** al controller **Home**.
* Nel Passaggio 4: 
    * Nei criteri di estensione trovare il nodo `<ContentDefinition>` che include `Id="api.selfasserted"`, `Id="api.localaccountsignup"` e `Id="api.localaccountpasswordreset"`.
    *  Impostare l'attributo `LoadUri` sull'URI selfasserted.
* Nel Passaggio 8.2 modificare il codice per accettare i parametri di stringa di query. 
* Caricare i criteri TrustFrameworkExtensions.xml e verificare che superino la convalida.
* Eseguire il test dei criteri e fare clic su **Iscriversi adesso** per visualizzare il risultato.

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facoltativo] Scaricare i file e il codice dei criteri completi.
* Anziché usare i file di esempio, per creare lo scenario è consigliabile usare i file di criteri personalizzati dopo aver completato la procedura di introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* È possibile scaricare il codice completo in [Sample visual studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization) (Soluzione di Visual Studio di esempio per riferimento)




