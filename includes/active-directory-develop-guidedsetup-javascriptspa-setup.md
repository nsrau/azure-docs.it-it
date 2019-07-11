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
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133501"
---
## <a name="set-up-your-web-server-or-project"></a>Impostare il server Web o il progetto

> Si preferisce scaricare questo progetto di esempio? Eseguire una di queste operazioni:
> 
> - Per eseguire il progetto con un server Web locale, ad esempio Node.js, [scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Facoltativo) Per eseguire il progetto con il server IIS, [scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Per configurare quindi l'esempio di codice prima di eseguirlo, andare al [passaggio di configurazione](#register-your-application).

## <a name="prerequisites"></a>Prerequisiti

* Per eseguire l'esercitazione, è necessario un server Web locale, ad esempio [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) o l'integrazione di IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Se si usa Node.js per eseguire il progetto, installare un ambiente di sviluppo integrato (IDE), ad esempio [Visual Studio Code](https://code.visualstudio.com/download), per modificare i file di progetto.

* Le istruzioni di questa guida sono basate su Node.js e Visual Studio 2017, ma è possibile usare qualsiasi altro ambiente di sviluppo o server Web.

## <a name="create-your-project"></a>Creare il progetto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opzione 1: Node.js o altri server Web
> Verificare di avere installato [Node.js](https://nodejs.org/en/download/) e quindi seguire questa procedura:
> - Creare una cartella per ospitare l'applicazione.
>
> ### <a name="option-2-visual-studio"></a>Opzione 2: Visual Studio
> Se si usa Visual Studio e si sta creando un nuovo progetto, eseguire queste operazioni:
> 1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**.
> 1. In **Visual C#\Web** selezionare **Applicazione Web ASP.NET (.NET Framework)** .
> 1. Immettere un nome per l'applicazione e quindi fare clic su **OK**.
> 1. In **Nuova applicazione Web ASP.NET** selezionare **Vuota**.

## <a name="create-the-spa-ui"></a>Creare l'interfaccia utente SPA
1. Creare un file *index.html* per la SPA di JavaScript. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e scegliere **Aggiungi** > **Nuovo elemento** > **Pagina HTML**, quindi assegnare al file il nome *index.html*.

1. Nel file *index.html* aggiungere il codice seguente:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
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

   > [!TIP]
   > È possibile sostituire la versione di MSAL.js nello script precedente con l'ultima versione rilasciata disponibile nella pagina delle [versioni di MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
