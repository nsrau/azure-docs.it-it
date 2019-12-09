---
title: Usare Internet Explorer con MSAL. js | Azure
titleSuffix: Microsoft identity platform
description: Utilizzare Microsoft Authentication Library per JavaScript (MSAL. js) con il browser Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 715e92a10ca0b4cbe38119931bc66d36c3b9259a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916282"
---
# <a name="use-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Usare i browser Internet Explorer e Microsoft Edge con MSAL. js

Microsoft Authentication Library per JavaScript (MSAL. js) viene generato per [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , in modo che possa essere eseguito in Internet Explorer. Esistono, tuttavia, alcuni aspetti da sapere.

## <a name="run-an-app-in-internet-explorer"></a>Eseguire un'app in Internet Explorer
Se si intende usare MSAL. js nelle applicazioni che possono essere eseguite in Internet Explorer, è necessario aggiungere un riferimento a un riempimento di promessa prima di fare riferimento allo script MSAL. js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Questo perché Internet Explorer non supporta le promesse JavaScript in modo nativo.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debug di un'applicazione in esecuzione in Internet Explorer

### <a name="running-in-production"></a>In esecuzione nell'ambiente di produzione
La distribuzione dell'applicazione in produzione (ad esempio in app Web di Azure) normalmente funziona correttamente, purché l'utente finale abbia accettato i popup. È stato testato con Internet Explorer 11.

### <a name="running-locally"></a>Esecuzione in locale
Se si desidera eseguire ed eseguire il debug in locale dell'applicazione in esecuzione in Internet Explorer, è necessario tenere presenti le considerazioni seguenti (si supponga di voler eseguire l'applicazione come *http://localhost:1234* ):

- Internet Explorer dispone di un meccanismo di sicurezza denominato "modalità protetta", che impedisce il corretto funzionamento di MSAL. js. Tra i sintomi, dopo l'accesso, è possibile reindirizzare la pagina a http://localhost:1234/null.

- Per eseguire ed eseguire il debug dell'applicazione in locale, è necessario disabilitare questa "modalità protetta". Per questo:

    1. Fare clic su **strumenti** di Internet Explorer (icona a forma di ingranaggio).
    1. Selezionare **Opzioni Internet** e quindi la scheda **sicurezza** .
    1. Fare clic sull'area **Internet** e deselezionare **Abilita modalità protetta. è necessario riavviare Internet Explorer**. Internet Explorer avvisa che il computer non è più protetto. Fare clic su **OK**.
    1. Riavviare Internet Explorer.
    1. Eseguire ed eseguire il debug dell'applicazione.

Al termine, ripristinare le impostazioni di sicurezza di Internet Explorer.  Selezionare **impostazioni** -> **Opzioni Internet** -> **sicurezza** -> **Reimposta tutte le zone sul livello predefinito**.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sui [problemi noti quando si usa MSAL. js in Internet Explorer](msal-js-use-ie-browser.md).