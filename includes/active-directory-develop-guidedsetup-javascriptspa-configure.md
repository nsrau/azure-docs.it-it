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
ms.openlocfilehash: 07aac49e7aed7c95863a2058a9de3d1e8f2cd1ad
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060998"
---
## <a name="register-your-application"></a>Registrare l'applicazione

È possibile creare un'applicazione in più modi. Selezionarne uno:

### <a name="option-1-register-your-application-express-mode"></a>Opzione 1: Registrare l'applicazione (modalità Rapida)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:

1.  Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica.
3.  Assicurarsi che l'opzione per l'**installazione guidata** sia selezionata.
4.  Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice.

### <a name="option-2-register-your-application-advanced-mode"></a>Opzione 2: Registrare l'applicazione (modalità avanzata)

1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione.
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica.
3. Assicurarsi che l'opzione per l'**installazione guidata** sia deselezionata.
4.  Fare clic su **Aggiungi piattaforma** e quindi selezionare **Web**.
5. Aggiungere l'elemento **URL di reindirizzamento** corrispondente all'URL dell'applicazione basato sul server Web. Per le istruzioni per impostare e ottenere l'URL di reindirizzamento in Visual Studio e Node, vedere le sezioni seguenti.
6. Selezionare **Salva**.

> #### <a name="setting-redirect-url-for-node"></a>Impostazione dell'URL di reindirizzamento per Node
> Per Node.js, è possibile impostare la porta del server Web nel file *server.js*. Questa esercitazione usa la porta 30662 come riferimento, ma è possibile usare qualsiasi altra porta disponibile. In ogni caso, seguire le istruzioni riportate di seguito per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione:<br/>
> - Tornare al *portale di registrazione delle applicazioni* e impostare `http://localhost:30662/` come `Redirect URL` o usare `http://localhost:[port]/` se si usa una porta TCP personalizzata (dove *[port]* è il numero di porta TCP personalizzata) e fare clic su "Salva"

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento
> Seguire questi passaggi per ottenere l'URL di reindirizzamento:
> 1.    In **Esplora soluzioni** selezionare il progetto e controllare la finestra **Proprietà**. Se la finestra **Proprietà** non viene visualizzata, premere **F4**.
> 2.    Copiare il valore da **URL** negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Tornare al *portale di registrazione dell'applicazione*, incollare il valore come **URL di reindirizzamento** e selezionare **Salva**


#### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

1.  Nel file `index.html` creato durante la configurazione del progetto aggiungere le informazioni di registrazione dell'applicazione. Aggiungere il codice seguente all'inizio tra i tag `<script></script>` nel corpo del file `index.html`:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Sostituire <code>Enter the application Id here</code> con l'ID applicazione appena registrato.
</li>
</ol>
