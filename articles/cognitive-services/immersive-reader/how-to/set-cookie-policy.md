---
title: Imposta criteri dei cookie di lettura immersivi
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come impostare i criteri dei cookie per il lettore immersivo.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: ae4812a69a1e6833224dc4dedf36566167ae4796
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483415"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Come impostare i criteri dei cookie per il lettore immersivo

Per impostazione predefinita, il lettore immersivo Disabilita l'utilizzo dei cookie. Se si Abilita l'utilizzo dei cookie, il lettore immersivo può utilizzare i cookie per gestire le preferenze dell'utente e tenere traccia dell'utilizzo delle funzionalità. Se si Abilita l'utilizzo dei cookie nel lettore immersivo, prendere in considerazione i requisiti dei criteri di conformità dei cookie UE. È responsabilità dell'applicazione host ottenere il consenso dell'utente necessario in base ai criteri di conformità dei cookie dell'Unione europea.

I criteri dei cookie possono essere impostati tramite le [Opzioni](../reference.md#options)del lettore immersive.

## <a name="enable-cookie-usage"></a>Abilita utilizzo cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Disabilitare l'utilizzo di cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'[esercitazione per Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Node.js
* Visualizzare l'[esercitazione per Python](../tutorial-python.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Python
* Vedere l' [esercitazione Swift](../tutorial-ios-picture-immersive-reader.md) per informazioni sulle altre operazioni che è possibile eseguire con l'SDK di immersive Reader con Swift
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../reference.md)