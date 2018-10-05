---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 3274e5929985b2a78c5b463622be6cdbd7320d79
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060335"
---
## <a name="setting-up-your-web-server-or-project"></a>Impostazione del server Web o del progetto

> Si preferisce scaricare questo progetto di esempio?
> - [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) per un server Web locale, ad esempio Node
>
> oppure
> - [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> E quindi passare direttamente al [passaggio di configurazione](#register-your-application) per configurare l'esempio di codice prima di eseguirlo.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire l'esercitazione, è necessario un server Web locale, ad esempio [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) o l'integrazione di IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/).

Le istruzioni di questa guida sono basate su Node.js e Visual Studio 2017, ma è possibile usare qualsiasi altro ambiente di sviluppo o server Web.

## <a name="create-your-project"></a>Creare il progetto

> ### <a name="option-1-node-other-web-servers"></a>Opzione 1: Node/altri server Web
> Verificare di avere installato [Node.js](https://nodejs.org/en/download/), quindi seguire questa procedura:
> - Creare una cartella per ospitare l'applicazione.

<p/><!-- -->

> ### <a name="option-2-visual-studio"></a>Opzione 2: Visual Studio
> Se si usa Visual Studio e si sta creando un nuovo progetto, seguire la procedura seguente per creare una nuova soluzione di Visual Studio:
> 1.    In Visual Studio: **File > Nuovo > Progetto**
> 2.    In **Visual C#\Web** selezionare **Applicazione Web ASP.NET (.NET Framework)**
> 3.    Immettere un nome per l'applicazione e fare clic su **OK**
> 4.    In **Nuova applicazione Web ASP.NET** selezionare **Vuota**


## <a name="create-your-single-page-applications-ui"></a>Creare l'interfaccia utente dell'applicazione a singola pagina
1.  Creare un file `index.html` per l'applicazione a pagina singola JavaScript. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e scegliere: **Aggiungi > Nuovo elemento > Pagina HTML**, denominandolo index.html.

2.  Aggiungere il codice seguente alla pagina:
```html
<!DOCTYPE html>
<html>
<head>
        <title>Quickstart for MSAL JS</title>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.js"></script>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
</head>
<body>
        <h2>Welcome to MSAL.js Quickstart</h2><br/>
        <h4 id="WelcomeMessage"></h4>
        <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
        <pre id="json"></pre>
        <script>
            //JS code
        </script>
</body>
</html>
```
