---
title: Dizionario dinamico - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come usare la funzionalità di dizionario dinamico dei servizi cognitivi di Azure API Traduzione testuale.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837347"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Come usare un dizionario dinamico

Se si conosce già la traduzione che si vuole applicare a una parola o una frase, è possibile specificarla come markup all'interno della richiesta. Il dizionario dinamico è sicuro solo per nomi composti come nomi propri e nomi di prodotto.

**Sintassi:**

< msTrans: Dictionary Translation = "translation of phrase" > phrase </msTrans: Dictionary >

**Requisiti**

* Le lingue `From` e `To` devono essere diverse. 
* È necessario includere il parametro `From` nella richiesta di traduzione dell'API invece di usare la funzionalità di rilevamento automatico. 

**Esempio: en-de:**

Input di origine: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

Output di destinazione: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Questa funzionalità funziona allo stesso modo con o senza la modalità HTML.

È consigliabile usarla solo in casi limitati. Il modo più appropriato e di gran lunga migliore per personalizzare una traduzione è quello di usare Custom Translator. Custom Translator fa un ampio uso delle probabilità statistiche e di contesto. Se si hanno o si possono creare dati di training che mostrano l'azienda o una frase nel contesto, si otterranno certamente risultati migliori. Altre informazioni sul traduttore personalizzato sono disponibili all'indirizzo [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
