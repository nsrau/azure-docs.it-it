---
title: Dizionario dinamico - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come usare la funzionalità di dizionario dinamico dell'API Testo traduttore Servizi cognitivi di Azure.This article explains how to use the dynamic dictionary feature of the Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446704"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Come utilizzare un dizionario dinamico

Se si conosce già la traduzione che si vuole applicare a una parola o una frase, è possibile specificarla come markup all'interno della richiesta. Il dizionario dinamico è sicuro solo per i sostantivi composti come nomi propri e nomi di prodotto.

**Sintassi:**

<mstrans:dictionary translation:"traduzione della frase">frase</mstrans:dictionary>

**Requisiti:**

* Le `From` `To` lingue e devono includere l'inglese e un'altra lingua supportata. 
* È necessario `From` includere il parametro nella richiesta di conversione API anziché utilizzare la funzionalità di rilevamento automatico. 

**Esempio: en-de:**

Ingresso sorgente:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Uscita di destinazione:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Questa funzionalità funziona allo stesso modo con o senza la modalità HTML.

Utilizzare la funzione con parsimonia. Un modo migliore per personalizzare la traduzione consiste nell'utilizzare Custom Translator. Custom Translator fa un ampio uso delle probabilità statistiche e di contesto. Se si hanno o si possono creare dati di training che mostrano l'azienda o una frase nel contesto, si otterranno certamente risultati migliori. Ulteriori informazioni su Custom Translator [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)sono disponibili all'indirizzo .
