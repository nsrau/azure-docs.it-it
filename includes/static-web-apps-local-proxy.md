---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594400"
---
#### <a name="local-proxy"></a>Proxy locale

È possibile configurare un proxy per l'estensione Live Server di Visual Studio Code che reindirizza tutte le richieste a `/api` all'endpoint API in esecuzione all'indirizzo `http://127.0.0.1:7071/api`.

1. Aprire il file _.vscode/settings.json_.

1. Aggiungere le impostazioni seguenti per specificare un proxy per Live Server.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   È consigliabile salvare questa configurazione nel file delle impostazioni del progetto, anziché nel file delle impostazioni utente.

   L'uso delle impostazioni del progetto garantisce che il proxy non venga applicato a tutti gli altri progetti aperti in Visual Studio Code.
