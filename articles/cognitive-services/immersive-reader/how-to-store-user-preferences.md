---
title: Preferenze utente archivio
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come archiviare le preferenze dell'utente.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486922"
---
# <a name="how-to-store-user-preferences"></a>Come archiviare le preferenze utente

Questo articolo illustra come archiviare le preferenze dell'utente. Questa funzionalità è concepita come un mezzo alternativo per archiviare le preferenze dell'utente nel caso in cui l'uso di cookie non sia auspicabile o fattibile.

## <a name="how-to-enable-storing-user-preferences"></a>Come abilitare l'archiviazione delle preferenze utente

Il `options` parametro contiene il `onPreferencesChanged` callback. Questa funzione verrà chiamata ogni volta che l'utente modifica le preferenze, ad esempio modificando le dimensioni del testo, il colore del tema, il tipo di carattere e così via. Il `value` parametro contiene una stringa che rappresenta le preferenze correnti dell'utente. Questa stringa può essere archiviata usando qualsiasi meccanismo preferibile.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Come caricare le preferenze utente nel lettore immersivo

Passare le preferenze dell'utente al Reader immersivo usando il `preferences` parametro. Un esempio banale per archiviare e caricare le preferenze dell'utente è il seguente:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)