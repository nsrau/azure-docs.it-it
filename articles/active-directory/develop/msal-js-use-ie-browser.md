---
title: Problemi in Internet Explorer (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Utilizzare microsoft Authentication Library per JavaScript (MSAL.js) con il browser Internet Explorer.
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
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695858"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemi noti nei browser Internet Explorer e Microsoft Edge (MSAL.js)

Microsoft Authentication Library per JavaScript (MSAL.js) viene generato per [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) in modo che possa essere eseguito in Internet Explorer. Ci sono, tuttavia, alcune cose da sapere.

## <a name="run-an-app-in-internet-explorer"></a>Eseguire un'app in Internet Explorer
Se si intende utilizzare MSAL.js nelle applicazioni che possono essere eseguite in Internet Explorer, sarà necessario aggiungere un riferimento a un polyfill promessa prima di fare riferimento allo script MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Ciò è dovuto al fatto che Internet Explorer non supporta le promesse JavaScript in modo nativo.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debug di un'applicazione in esecuzione in Internet Explorer

### <a name="running-in-production"></a>Esecuzione in produzione
La distribuzione dell'applicazione nell'ambiente di produzione (ad esempio nelle app Web di Azure) funziona normalmente correttamente, purché l'utente finale abbia accettato popup. L'abbiamo testato con Internet Explorer 11.

### <a name="running-locally"></a>Esecuzione in locale
Se si desidera eseguire ed eseguire il debug in locale dell'applicazione in esecuzione in Internet Explorer, *http://localhost:1234*è necessario tenere presenti le considerazioni seguenti (presupporre che si desidera eseguire l'applicazione come ):

- Internet Explorer dispone di un meccanismo di sicurezza denominato "modalità protetta", che impedisce il corretto funzionamento di MSAL.js. Tra i sintomi, dopo l'accesso, la http://localhost:1234/nullpagina può essere reindirizzata a .

- Per eseguire ed eseguire il debug dell'applicazione in locale, è necessario disabilitare questa "modalità protetta". Per questo:

    1. Fare clic su **Strumenti** di Internet Explorer (icona a forma di ingranaggio).
    1. Selezionare **Opzioni Internet,** quindi la scheda **Protezione.**
    1. Fare clic sull'area **Internet** e **deselezionare Abilita modalità protetta (richiede il riavvio**di Internet Explorer). Internet Explorer avverte che il computer non è più protetto. Fare clic su **OK**.
    1. Riavviare Internet Explorer.
    1. Eseguire ed eseguire il debug dell'applicazione.

Al termine, ripristinare le impostazioni di protezione di Internet Explorer.  Selezionare **Impostazioni** -> **Impostazioni Protezione** -> **Security** -> Internet**Ripristina tutte le aree al livello predefinito**.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sui [problemi noti quando si utilizza MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).