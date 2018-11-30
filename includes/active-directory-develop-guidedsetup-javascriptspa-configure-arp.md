---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 9a9f3cd2668f0e78441548592f38ade8ddc0fe46
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331125"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario configurare l'URL di reindirizzamento delle informazioni di registrazione dell'applicazione e quindi aggiungere l'ID applicazione all'applicazione JavaScript SPA.

### <a name="configure-redirect-url"></a>Configurare l'URL di reindirizzamento

Configurare il campo `Redirect URL` con l'URL della pagina index.html in base al server Web, quindi fare clic su *Aggiorna*.

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento
> Seguire questi passaggi per ottenere l'URL di reindirizzamento:
> 1. In **Esplora soluzioni** selezionare il progetto e controllare la finestra **Proprietà**. Se la finestra **Proprietà** non viene visualizzata, premere **F4**.
> 2. Copiare il valore da **URL** negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Incollare il valore come **URL di reindirizzamento** nella parte superiore della pagina e quindi selezionare **Aggiorna**.

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Impostazione dell'URL di reindirizzamento per Node
> Per Node.js, è possibile impostare la porta del server Web nel file *server.js*. Questa esercitazione usa la porta 30662 per riferimento, ma è possibile usare qualsiasi altra porta disponibile. Seguire le istruzioni riportate di seguito per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione:<br/>
> Impostare `http://localhost:30662/` come **URL di reindirizzamento** nella parte superiore della pagina o usare `http://localhost:[port]/` se si usa una porta TCP personalizzata (dove *[port]* è il numero di porta TCP personalizzata) e quindi fare clic su **Aggiorna**.

### <a name="configure-your-javascript-spa-application"></a>Configurare l'applicazione JavaScript SPA

1. Nel file `index.html` creato durante la configurazione del progetto aggiungere le informazioni di registrazione dell'applicazione. Aggiungere il codice seguente all'inizio tra i tag `<script></script>` nel corpo del file `index.html`:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
