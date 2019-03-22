---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a9aeb2f45ec8c882c47425879ab6ef726ca94060
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203332"
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

> ### <a name="option-1-node-other-web-servers"></a>Opzione 1: Nodo / altri server web
> Verificare di avere installato [Node.js](https://nodejs.org/en/download/), quindi seguire questa procedura:
> - Creare una cartella per ospitare l'applicazione.

<p/><!-- -->

> ### <a name="option-2-visual-studio"></a>Opzione 2: Visual Studio
> Se si usa Visual Studio e si sta creando un nuovo progetto, seguire la procedura seguente per creare una nuova soluzione di Visual Studio:
> 1.    In Visual Studio:  **File > Nuovo > progetto**
> 2.    In **Visual C#\Web** selezionare **Applicazione Web ASP.NET (.NET Framework)**
> 3.    Immettere un nome per l'applicazione e fare clic su **OK**
> 4.    In **Nuova applicazione Web ASP.NET** selezionare **Vuota**


## <a name="create-your-single-page-applications-ui"></a>Creare l'interfaccia utente dell'applicazione a singola pagina
1. Creare un file `index.html` per l'applicazione a pagina singola JavaScript. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto) fare clic destro e selezionare: **Aggiungi > Nuovo elemento > pagina HTML** e denominandolo index. HTML.

2. Aggiungere il codice seguente alla pagina:
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
