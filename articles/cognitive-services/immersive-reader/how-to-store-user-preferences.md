---
title: Archiviare le preferenze utente
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come archiviare le preferenze dell'utente.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ddae4a99964e438c003fe0ff0db91c5808fa7631
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761108"
---
# <a name="how-to-store-user-preferences"></a>Come archiviare le preferenze utente

Questo articolo illustra come archiviare le impostazioni dell'interfaccia utente, note formalmente come **Preferenze dell'** utente, tramite le opzioni [-Preferenze](./reference.md#options) e [-onPreferencesChanged](./reference.md#options) immersive Reader SDK.

Quando l'opzione [CookiePolicy](./reference.md#cookiepolicy-options) SDK è impostata su *Enabled*, l'applicazione immersive Reader archivia le **Preferenze dell'utente** (dimensione del testo, colore del tema, carattere e così via) nei cookie, che sono locali per un browser e un dispositivo specifici. Ogni volta che l'utente avvia il lettore immersivo sullo stesso browser e dispositivo, si aprirà con le preferenze dell'utente dall'ultima sessione del dispositivo. Tuttavia, se l'utente apre il lettore immersivo in un altro browser o dispositivo, le impostazioni verranno inizialmente configurate con le impostazioni predefinite del lettore immersivo e l'utente dovrà impostare di nuovo le preferenze e così via per ogni dispositivo usato. Le `-preferences` `-onPreferencesChanged` Opzioni dell'SDK e Immersive Reader consentono alle applicazioni di eseguire il roaming delle preferenze di un utente in vari browser e dispositivi, in modo che l'utente abbia un'esperienza coerente ovunque usino l'applicazione.

Per prima cosa, specificando l' `-onPreferencesChanged` opzione di callback SDK quando si avvia l'applicazione immersiva Reader, il lettore immersivo invierà una `-preferences` stringa all'applicazione host ogni volta che l'utente modifica le preferenze durante la sessione di lettura immersiva. L'applicazione host è quindi responsabile dell'archiviazione delle preferenze utente nel proprio sistema. Quindi, quando lo stesso utente avvia di nuovo il lettore immersivo, l'applicazione host può recuperare le preferenze dell'utente dalla risorsa di archiviazione e fornirle come `-preferences` opzione di stringa SDK quando viene avviata l'applicazione immersiva Reader, in modo da ripristinare le preferenze dell'utente.

Questa funzionalità può essere usata come alternativa per archiviare le **Preferenze dell'utente** nel caso in cui l'uso di cookie non sia auspicabile o fattibile.

> [!CAUTION]
> **Importante** Non tentare di modificare a livello di codice i valori della `-preferences` stringa inviata da e verso l'applicazione immersiva Reader, perché questo potrebbe causare un comportamento imprevisto che può comportare un'esperienza utente ridotta per i clienti. Le applicazioni host non devono mai assegnare un valore personalizzato a o modificare la `-preferences` stringa. Quando si usa l' `-preferences` opzione String, usare solo il valore esatto restituito dall'opzione di `-onPreferencesChanged` callback.

## <a name="how-to-enable-storing-user-preferences"></a>Come abilitare l'archiviazione delle preferenze utente

il parametro [launchAsync](./reference.md#launchasync) dell'SDK del lettore immersivo `options` contiene il `-onPreferencesChanged` callback. Questa funzione verrà chiamata ogni volta che l'utente modifica le proprie preferenze. Il `value` parametro contiene una stringa che rappresenta le preferenze correnti dell'utente. Questa stringa viene quindi archiviata per l'utente dall'applicazione host.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Come caricare le preferenze utente nel lettore immersivo

Passare le preferenze dell'utente al lettore immersivo usando l' `-preferences` opzione. Un esempio banale per archiviare e caricare le preferenze dell'utente è il seguente:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)