---
title: Usare Internet Explorer (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni sull'uso di Microsoft Authentication Library per JavaScript (msal) con i browser Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873916"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problemi noti nei browser Microsoft Edge e Internet Explorer con msal. js

Microsoft Authentication Library per JavaScript (msal) viene generato per [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) in modo che possa essere eseguita in Internet Explorer. Esistono, tuttavia, alcuni aspetti da conoscere.

## <a name="run-an-app-in-internet-explorer"></a>Eseguire un'app in Internet Explorer
Se si prevede di usare msal. js in applicazioni eseguibili in Internet Explorer, è necessario aggiungere un riferimento a un oggetto promise polyfill prima fa riferimento allo script di msal. js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Questo avviene perché Internet Explorer non supporta la promessa JavaScript in modo nativo.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debug di un'applicazione in esecuzione in Internet Explorer

### <a name="running-in-production"></a>In esecuzione nell'ambiente di produzione
Distribuzione dell'applicazione nell'ambiente di produzione (ad esempio nelle App Web di Azure) in genere funziona bene, purché che l'utente finale ha accettato i popup. Abbiamo testato con Internet Explorer 11.

### <a name="running-locally"></a>In esecuzione in locale
Se si desidera eseguire ed eseguire il debug in locale l'applicazione in esecuzione in Internet Explorer, è necessario tenere presente le considerazioni seguenti (si supponga di voler eseguire l'applicazione come *http://localhost:1234* ):

- Internet Explorer ha un meccanismo di sicurezza denominato "modalità protetta", che impedisce di msal. js di funzioni correttamente. Tra i sintomi, dopo l'accesso, la pagina può essere reindirizzata a http://localhost:1234/null.

- Per eseguire il debug dell'applicazione in locale, è necessario disabilitare questa modalità"protetta". A tale scopo:

    1. Fare clic su Internet Explorer **strumenti** (l'icona a forma di ingranaggio).
    1. Selezionare **Opzioni Internet** e quindi la **sicurezza** scheda.
    1. Fare clic sui **Internet** zona e deselezionare **abilita la modalità protetta (richiede il riavvio di Internet Explorer)** . Internet Explorer avvisa che il computer non è più protetta. Fare clic su **OK**.
    1. Riavviare Internet Explorer.
    1. Eseguire e il debug dell'applicazione.

Al termine, ripristinare le impostazioni di sicurezza di Internet Explorer.  Selezionare **le impostazioni** -> **Opzioni Internet** -> **sicurezza** -> **reimpostare tutte le zone al livello predefinito**.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [problemi noti quando si usa msal. js in Internet Explorer](msal-js-use-ie-browser.md).