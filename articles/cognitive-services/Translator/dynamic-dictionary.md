---
title: Dizionario dinamico - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Illustra come usare la funzionalità dizionario dinamico dell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 6140361d3cffaf7938aa8f24b6f0c3d27784af88
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876012"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Come usare la funzionalità dizionario dinamico dell'API Traduzione testuale

Se si conosce già la traduzione che si vuole applicare a una parola o una frase, è possibile specificarla come markup all'interno della richiesta. Il dizionario dinamico è sicuro solo per nomi composti come nomi propri e nomi di prodotto.

**Sintassi:**

<mstrans:dictionary translation=”translation of phrase”>frase</mstrans:dictionary>

**Esempio: en-de:**

Input di origine: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

Output di destinazione: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Questa funzionalità funziona allo stesso modo con o senza la modalità HTML.

È consigliabile usarla solo in casi limitati. Il modo più appropriato e di gran lunga migliore per personalizzare una traduzione è quello di usare Custom Translator. Custom Translator fa un ampio uso delle probabilità statistiche e di contesto. Se si hanno o si possono creare dati di training che mostrano l'azienda o una frase nel contesto, si otterranno certamente risultati migliori. Altre informazioni sul traduttore personalizzato sono disponibili all'indirizzo [http://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
