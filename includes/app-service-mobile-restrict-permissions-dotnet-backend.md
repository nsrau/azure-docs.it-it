---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180640"
---
Per impostazione predefinita, le API in un back-end dell'app per dispositivi mobili possono essere richiamate in modo anonimo. È necessario limitare l'accesso solo ai client autenticati.  

* **Back-end Node. js nuovamente fine (tramite il Portale di Azure)** :  

    Nelle impostazioni dell'app per dispositivi mobili fare clic su **Tabelle semplici** e selezionare la tabella. Fare clic su **Modifica autorizzazioni**, selezionare **Authenticated access only** (Solo accesso con autenticazione) per tutte le autorizzazioni e quindi fare clic su **Salva**.
* **Back-end. NET (C#)** :  

    Nel progetto server passare a **Controller** > **TodoItemController.cs**. Aggiungere l'attributo `[Authorize]` alla classe **TodoItemController**, come indicato di seguito. Per limitare l'accesso solo a metodi specifici, è inoltre possibile applicare questo attributo solo a tali metodi anziché alla classe. Pubblicare di nuovo il progetto server.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end Node.js (tramite codice Node.js)** :  

    Per richiedere l'autenticazione per l'accesso alla tabella, aggiungere la riga seguente allo script del server Node.js:

        table.access = 'authenticated';

    Per altre informazioni, vedere [come: Richiedere l'autenticazione per l'accesso alle tabelle](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Per informazioni su come scaricare il progetto di codice di avvio rapido dal sito, vedere [come: Scaricare il progetto di un codice di avvio rapido di un back-end di Node. js tramite Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
