---
title: Configurare la traduzione
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come configurare le varie opzioni per la traduzione.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486934"
---
# <a name="how-to-configure-translation"></a>Come configurare la traduzione

Questo articolo illustra come configurare le diverse opzioni per la traduzione nel lettore immersivo.

## <a name="configure-translation-language"></a>Configura lingua di traduzione

Il `options` parametro contiene tutti i flag che possono essere utilizzati per configurare la traduzione. Impostare il `language` parametro sulla lingua in cui si desidera tradurre. Vedere il [supporto linguistico](./language-support.md) per l'elenco completo delle lingue supportate.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Traduci automaticamente il documento al caricamento

Impostare `autoEnableDocumentTranslation` su `true` per abilitare la conversione automatica dell'intero documento quando viene caricato il lettore immersivo.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Abilita automaticamente traduzione Word

Impostare `autoEnableWordTranslation` su `true` per abilitare la traduzione a parola singola.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)