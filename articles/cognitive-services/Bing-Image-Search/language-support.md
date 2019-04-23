---
title: Supporto per la lingua - API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su quali paesi/aree e lingue sono supportati dall'API Ricerca immagini Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: a4c315d2696b081a4f2ac646d097e76ff9562380
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009481"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Lingua e regioni supportate dall'API Ricerca immagini Bing

L'API Ricerca immagini Bing supporta più di una quarantina di paesi/aree, molti dei quali con più di una lingua. Specificare un paese/un'area con una query serve principalmente per perfezionare i risultati della ricerca in base agli interessi in tale paese/area. I risultati possono anche contenere collegamenti a Bing e questi collegamenti possono localizzare l'esperienza utente di Bing in base al paese/all'area o alla lingua specifica.

Per specificare la lingua e il paese/l'area, impostare il parametro di query `mkt` (mercato) su uno dei codici della tabella **Mercati** riportata di seguito. Il mercato specifica sia un paese/un'area che una lingua. Se l'utente preferisce vedere il testo visualizzato in una lingua diversa, impostare il parametro di query `setLang` sul codice lingua appropriato.

In alternativa è possibile specificare il paese/l'area usando il parametro di query `cc`. Se si specifica un paese/un'area, è necessario specificare anche uno o più codici di lingua usando l'intestazione HTTP `Accept-Language`. Le lingue supportate variano in base al paese/all'area e sono indicate per ogni paese nella tabella Mercati.

> [!NOTE]
> L'API delle immagini di tendenza supporta attualmente solo i mercati seguenti:
> - en-US (inglese, Stati Uniti)
> - en-CA (inglese, Canada)
> - en-AU (inglese, Australia)
> - zh-CN (cinese, Cina)

## <a name="countries"></a>Paesi

|Paese/Area geografica|Codice|
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

|Paese/Area geografica|Linguaggio|Codice di mercato|
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
|Stati Uniti|Inglese|en-US|
|Stati Uniti|Spagnolo|es-US|

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli endpoint dell'API Ricerca notizie Bing, vedere le [informazioni di riferimento relative all'API Ricerca notizie Bing versione 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
