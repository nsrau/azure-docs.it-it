---
title: Dizionario dinamico - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Illustra come usare la funzionalità dizionario dinamico dell'API Traduzione testuale.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436019"
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
