---
title: Impostare la politica dei cookie dei lettori immersivi
titleSuffix: Azure Cognitive Services
description: Questo articolo ti mostrerà come impostare la politica sui cookie per il lettore Immersive.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946111"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Come impostare i criteri per i cookie per il lettore Immersive

Immersive Reader disabilita l'utilizzo dei cookie per impostazione predefinita. Se si abilita l'utilizzo dei cookie, il lettore Immersive può utilizzare i cookie per mantenere le preferenze dell'utente e tenere traccia dell'utilizzo delle funzionalità. Se abiliti l'utilizzo dei cookie nel Lettore Immersivo, considera i requisiti della Politica UE sulla conformità dei cookie. È responsabilità dell'applicazione host ottenere il consenso dell'utente necessario in conformità con la politica di conformità dei cookie dell'UE.

La politica sui cookie può essere impostata tramite le [opzioni](../reference.md#options)Lettore immersivo . Per ulteriori informazioni, vedere [Enumerazione CookiePolicy](../reference.md#cookiepolicy-enum) .

## <a name="enable-cookie-usage"></a>Abilita utilizzo cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Disabilitare l'utilizzo dei cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'[esercitazione per Node.js](../quickstart-nodejs.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Node.js
* Visualizzare l'[esercitazione per Python](../tutorial-python.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Python
* Guarda il [tutorial Swift](../tutorial-ios-picture-immersive-reader.md) per vedere cos'altro puoi fare con Immersive Reader SDK utilizzando Swift
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../reference.md)