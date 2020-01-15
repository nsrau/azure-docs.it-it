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
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946111"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Come impostare i criteri dei cookie per il lettore immersivo

Per impostazione predefinita, il lettore immersivo Disabilita l'utilizzo dei cookie. Se si Abilita l'utilizzo dei cookie, il lettore immersivo può utilizzare i cookie per gestire le preferenze dell'utente e tenere traccia dell'utilizzo delle funzionalità. Se si Abilita l'utilizzo dei cookie nel lettore immersivo, prendere in considerazione i requisiti dei criteri di conformità dei cookie UE. È responsabilità dell'applicazione host ottenere il consenso dell'utente necessario in base ai criteri di conformità dei cookie dell'Unione europea.

I criteri dei cookie possono essere impostati tramite le [Opzioni](../reference.md#options)del lettore immersive. Per ulteriori informazioni, vedere [CookiePolicy enum](../reference.md#cookiepolicy-enum) .

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

* Visualizzare la [Guida introduttiva di node. js](../quickstart-nodejs.md) per vedere le altre operazioni che è possibile eseguire con l'SDK di immersive Reader usando node. js
* Visualizzare l'[esercitazione per Python](../tutorial-python.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Python
* Vedere l' [esercitazione Swift](../tutorial-ios-picture-immersive-reader.md) per informazioni sulle altre operazioni che è possibile eseguire con l'SDK di immersive Reader con Swift
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../reference.md)