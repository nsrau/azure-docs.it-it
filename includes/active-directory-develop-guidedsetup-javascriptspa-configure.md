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
ms.openlocfilehash: ec9eba4766da1afbbee568374de1ce06dc92ab2b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203325"
---
## <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com/) per registrare un'applicazione.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app (anteprima) > Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e impostare il valore per l'URL dell'applicazione in base al proprio server Web. Per le istruzioni per impostare e ottenere l'URL di reindirizzamento in Visual Studio e Node, vedere le sezioni seguenti.
1. Al termine, selezionare **Registra**.
1. Nella pagina **Panoramica**  dell'app prendere nota del valore del campo **ID applicazione (client)**.
1. Per questo avvio rapido è necessario abilitare il [flusso di concessione implicita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). Nel riquadro di spostamento a sinistra dell'applicazione registrata selezionare **Autenticazione**.
1. Nelle **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e di accesso sono obbligatori perché l'app deve consentire l'accesso agli utenti e chiamare un'API.
1. Selezionare **Salva**.

> #### <a name="setting-the-redirect-url-for-node"></a>Impostazione dell'URL di reindirizzamento per Node
> Per Node.js, è possibile impostare la porta del server Web nel file *server.js*. Questa esercitazione usa la porta 30662 per riferimento, ma è possibile usare qualsiasi altra porta disponibile. Seguire le istruzioni riportate di seguito per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione:<br/>
> - Tornare alla *registrazione dell'applicazione* e impostare `http://localhost:30662/` come `Redirect URL` o usare `http://localhost:[port]/` se si usa una porta TCP personalizzata (dove *[port]* è il numero di porta TCP personalizzata).

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento
> Seguire questi passaggi per ottenere l'URL di reindirizzamento:
> 1. In **Esplora soluzioni** selezionare il progetto e controllare la finestra **Proprietà**. Se la finestra **Proprietà** non viene visualizzata, premere **F4**.
> 2. Copiare il valore da **URL** negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Tornare alla *registrazione dell'applicazione* e impostare il valore come **URL di reindirizzamento**.

#### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

1. Nel file `index.html` creato durante la configurazione del progetto aggiungere le informazioni di registrazione dell'applicazione. Aggiungere il codice seguente all'inizio tra i tag `<script></script>` nel corpo del file `index.html`:

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Sostituire <code>Enter the application Id here</code> con l'ID applicazione appena registrato.
</li>
</ol>
