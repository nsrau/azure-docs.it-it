---
title: Dizionario dinamico dell'API Traduzione testuale Microsoft | Microsoft Docs
description: Illustra come usare la funzionalità dizionario dinamico dell'API Traduzione testuale Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378413"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Illustra come usare la funzionalità dizionario dinamico dell'API Traduzione testuale Microsoft

Se si conosce già la traduzione che si vuole applicare a una parola o una frase, è possibile specificarla come markup all'interno della richiesta. Il dizionario dinamico è sicuro solo per nomi composti come nomi propri e nomi di prodotto. 

**Sintassi:** 

<mstrans:dictionary translation=”translation of phrase”>frase</mstrans:dictionary>

**Esempio: en-de:**

Input di origine: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

Output di destinazione: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Questa funzionalità funziona allo stesso modo con o senza la modalità HTML. 

È consigliabile usarla solo in casi limitati. Il modo più appropriato e di gran lunga migliore per personalizzare una traduzione è quello di usare l'hub di Microsoft Translator. L'hub fa un ampio uso delle probabilità statistiche e di contesto. Se si ha la possibilità di creare dati di training che mostrano l'azienda o una frase nel contesto, si otterranno certamente risultati migliori. Altre informazioni sull'hub sono disponibili all'indirizzo [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com).

