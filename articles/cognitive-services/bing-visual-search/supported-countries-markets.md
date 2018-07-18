---
title: Lingue e paesi supportati dall'API Ricerca visiva Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Informazioni sulle lingue e sui paesi supportati dall'API Ricerca visiva Bing.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376417"
---
# <a name="bing-visual-search-countries-and-languages"></a>Lingue e paesi di Ricerca visiva Bing

L'API Ricerca visiva Bing supporta numerosi paesi, molti dei quali con più di una lingua. Per ogni richiesta è consigliabile includere paese e lingua dell'utente, poiché la conoscenza del mercato dell'utente consente a Bing di restituire risultati appropriati. Se l'utente non specifica il paese e la lingua, Bing fa del proprio meglio per determinarli. Poiché i risultati possono contenere collegamenti a Bing, se l'utente specifica il paese e la lingua e quindi fa clic sui collegamenti Bing, Bing è in grado di offrire un'esperienza utente localizzata nel modo più appropriato.

Per specificare la lingua e il paese, impostare il parametro di query `mkt` (mercato) su uno dei codici della tabella **Mercati** riportata di seguito. Il mercato specifica sia un paese che una lingua. Se l'utente preferisce vedere il testo visualizzato in una lingua diversa, impostare il parametro di query `setLang` sul codice lingua appropriato.

In alternativa è possibile specificare il paese utilizzando il parametro di query `cc`. Se si specifica un paese, è necessario specificare anche uno o più codici di lingua usando l'intestazione HTTP `Accept-Language`. Le lingue supportate variano in base al paese e sono indicate per ogni paese nella tabella Mercati.



> [!NOTE]
> Al mercato si applicano le restrizioni seguenti:
> 
> - Le annotazioni di riconoscimento di immagini sono disponibili solo in inglese. 
> - Ricette, acquisti e approfondimenti con pagine sono disponibili solo per il mercato en-US. 


## <a name="countries"></a>Paesi

|Paese|Codice|
|-------|----|
|Argentina|AR|
|Australia|AU|
|Austria|AT|
|Belgio|BE|
|Brasile|BR|
|Canada|CA|
|Cile|CL|
|Danimarca|DK|
|Finlandia|FI|
|Francia|VF|
|Germania|DE|
|RAS di Hong Kong|HK|
|India|IN|
|Indonesia|ID|
|Italia|IT|
|Giappone|JP|
|Corea del Sud|KR|
|Malaysia|MY|
|Messico|MX|
|Paesi Bassi|NL|
|Nuova Zelanda|NZ|
|Norvegia|NO|
|Cina|CN|
|Polonia|PL|
|Portogallo|PT|
|Filippine|PH|
|Russia|RU|
|Arabia Saudita|SA|
|Sud Africa|ZA|
|Spagna|ES|
|Svezia|SE|
|Svizzera|CH|
|Taiwan|TW|
|Turchia|TR|
|Regno Unito|GB|
|Stati Uniti|Stati Uniti|


## <a name="markets"></a>Mercati

|Paese|Linguaggio|Codice di mercato|
|-------|--------|-----------|
|Argentina|Spagnolo|es-AR|
|Australia|Inglese|en-AU|
|Austria|Tedesco|de-AT|
|Belgio|Olandese|nl-BE|
|Belgio|Francese|fr-BE|
|Brasile|Portoghese|pt-BR|
|Canada|Inglese|en-CA|
|Canada|Francese|fr-CA|
|Cile|Spagnolo|es-CL|
|Danimarca|Danese|da-DK|
|Finlandia|Finlandese|fi-FI|
|Francia|Francese|fr-FR|
|Germania|Tedesco|de-DE|
|RAS di Hong Kong|Cinese tradizionale|zh-HK|
|India|Inglese|en-IN|
|Indonesia|Inglese|en-ID|
|Italia|Italiano|it-IT|
|Giappone|Giapponese|ja-JP|
|Corea del Sud|Coreano|ko-KR|
|Malaysia|Inglese|en-MY|
|Messico|Spagnolo|es-MX|
|Paesi Bassi|Olandese|nl-NL|
|Nuova Zelanda|Inglese|en-NZ|
|Cina|Cinese|zh-CN|
|Polonia|Polacco|pl-PL|
|Portogallo|Portoghese|pt-PT|
|Filippine|Inglese|en-PH|
|Russia|Russo|ru-RU|
|Arabia Saudita|Arabo|ar-SA|
|Sud Africa|Inglese|en-ZA|
|Spagna|Spagnolo|es-ES|
|Svezia|Svedese|sv-SE|
|Svizzera|Francese|fr-CH|
|Svizzera|Tedesco|de-CH|
|Taiwan|Cinese tradizionale|zh-TW|
|Turchia|Turco|tr-TR|
|Regno Unito|Inglese|en-GB|
|Stati Uniti|Inglese|it-IT|
|Stati Uniti|Spagnolo|es-US|
