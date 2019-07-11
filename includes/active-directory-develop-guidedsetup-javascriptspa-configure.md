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
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133229"
---
## <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure AD che si vuole usare.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Nell'elenco a discesa nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e quindi impostare il valore sull'URL dell'applicazione in base al server Web. 

   Per informazioni sull'impostazione e sul recupero dell'URL di reindirizzamento in Visual Studio e Node.js, vedere le prossime due sezioni.

1. Selezionare **Registra**.
1. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
1. Per questo avvio rapido è necessario abilitare il [flusso di concessione implicita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
1. In **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e di accesso sono obbligatori perché l'app deve consentire l'accesso agli utenti e chiamare un'API.
1. Selezionare **Salva**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Impostare l'URL di reindirizzamento per Node.js
> Per Node.js, è possibile impostare la porta del server Web nel file *server.js*. Questa esercitazione usa la porta 30662 per riferimento, ma è possibile usare qualsiasi altra porta disponibile. 
>
> Per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione, tornare al riquadro **Registrazione dell'applicazione** ed eseguire una delle operazioni seguenti:
>
> - Impostare *`http://localhost:30662/`* come **URL di reindirizzamento**.
> - Se si usa una porta TCP personalizzata, usare *`http://localhost:<port>/`* (dove *\<porta>* è il numero della porta TCP personalizzata).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Impostare l'URL di reindirizzamento per Visual Studio
> Per ottenere l'URL di reindirizzamento per Visual Studio, eseguire le operazioni seguenti:
> 1. In **Esplora soluzioni** selezionare il progetto.
>
>    Si aprirà la finestra **Proprietà**. Se la finestra non si apre, premere **F4**.
>
>    ![Finestra Proprietà del progetto JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copiare il valore del campo **URL**.
 
> 1. Tornare al riquadro **Registrazione dell'applicazione** e incollare il valore copiato come **URL di reindirizzamento**.

#### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

1. Nel file *index.html* creato durante la configurazione del progetto aggiungere le informazioni di registrazione dell'applicazione. Nella parte iniziale del file, tra i tag `<script></script>`, aggiungere il codice seguente:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Dove:
    - *\<Enter_the_Application_Id_here>* è il valore di **ID applicazione (client)** dell'applicazione registrata.
    - Il valore *\<Enter_the_Tenant_info_here>* deve essere impostato su una delle opzioni seguenti:
       - Se l'applicazione supporta *Account solo in questa directory organizzativa*, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, *contoso.microsoft.com*).
       - Se l'applicazione supporta *Account in qualsiasi directory organizzativa*, sostituire questo valore con **organizations**.
       - Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con **common**. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con **consumers**.
