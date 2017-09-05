---
title: Impostazione guidata di JS SPA per Azure AD v2 - Impostazione| Microsoft Docs
description: Come le applicazioni SPA di Javascript possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: a399d35358ecf188070e699f4d975a415bd4649d
ms.contentlocale: it-it

---
## <a name="setting-up-your-web-server-or-project"></a>Impostazione del server Web o del progetto

> Si preferisce scaricare questo progetto di esempio? 
> - [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> oppure
> - [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) per un server Web locale, ad esempio Python
>
> E quindi passare direttamente al [passaggio di configurazione](#create-an-application-express) per configurare il codice di esempio prima di eseguirlo.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire l'installazione guidata, è necessario un server Web locale, ad esempio [Python http.server](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core) o l'integrazione di IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/). 

Le istruzioni di questa guida sono basate su Python e Visual Studio 2017, ma è possibile usare qualsiasi altro ambiente di sviluppo o server Web.

## <a name="create-your-project"></a>Creare il progetto 

> ### <a name="option-1-visual-studio"></a>Opzione 1: Visual Studio 
> Se si usa Visual Studio e si sta creando un nuovo progetto, seguire la procedura seguente per creare una nuova soluzione di Visual Studio:
> 1.    In Visual Studio: `File` > `New` > `Project`
> 2.    In `Visual C#\Web` selezionare `ASP.NET Web Application (.NET Framework)`
> 3.    Assegnare un nome all'applicazione e fare clic su *OK*
> 4.    In `New ASP.NET Web Application` selezionare `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Opzione 2: Python / altri server Web
> Verificare di avere installato [Python](https://www.python.org/downloads/), quindi attenersi ai passaggi riportati di seguito:
> - Creare una cartella host dell'applicazione.


## <a name="create-your-single-page-applications-ui"></a>Creare l'interfaccia utente dell'applicazione a singola pagina
1.  Creare un file *index.html* per la SPA di JavaScript. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e selezionare `Add` > `New Item` > `HTML page`, denominandolo index.html
2.  Aggiungere il codice seguente alla pagina:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````

