---
title: Dizionario dinamico - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Illustra come usare la funzionalità dizionario dinamico dell'API Traduzione testuale.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: d3a77ee9273e9f587b6157bb6ed2294865b1fca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514252"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Come usare un dizionario dinamico

Se si conosce già la traduzione che si vuole applicare a una parola o una frase, è possibile specificarla come markup all'interno della richiesta. Il dizionario dinamico è sicuro solo per nomi composti come nomi propri e nomi di prodotto.

**Sintassi:**

<mstrans:dictionary translation=”translation of phrase”>frase</mstrans:dictionary>

**Esempio: en-de:**

Input di origine: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

Output di destinazione: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Questa funzionalità funziona allo stesso modo con o senza la modalità HTML.

È consigliabile usarla solo in casi limitati. Il modo più appropriato e di gran lunga migliore per personalizzare una traduzione è quello di usare Custom Translator. Custom Translator fa un ampio uso delle probabilità statistiche e di contesto. Se si hanno o si possono creare dati di training che mostrano l'azienda o una frase nel contesto, si otterranno certamente risultati migliori. Altre informazioni sul traduttore personalizzato sono disponibili all'indirizzo [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
