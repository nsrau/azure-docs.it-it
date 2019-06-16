---
title: Supporto per la lingua - API Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Un elenco dei linguaggi naturali, paesi e aree geografiche supportate dall'API Ricerca visiva Bing. L'API Ricerca visiva Bing supporta numerosi paesi/aree, molti dei quali con più di una lingua.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 1639b8066f3c9943bc42f5151fcb456585441baf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64866238"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Lingua e regioni supportate dall'API Ricerca visiva Bing

L'API Ricerca visiva Bing supporta numerosi paesi/le aree, molti dei quali con più di una lingua. Per ogni richiesta è consigliabile includere paese/l'area e lingua dell'utente, poiché la conoscenza del mercato dell'utente consente a Bing di restituire risultati appropriati. Se l'utente non specifica il paese/l'area e la lingua, Bing fa del proprio meglio per determinarli. Poiché i risultati possono contenere collegamenti a Bing, se l'utente specifica il paese/l'area e la lingua e quindi fa clic sui collegamenti Bing, Bing è in grado di offrire un'esperienza utente localizzata nel modo più appropriato.

Per specificare la lingua e il paese/l'area, impostare il parametro di query `mkt` (mercato) su uno dei codici della tabella **Mercati** riportata di seguito. Il mercato specifica sia un paese/un'area che una lingua. Se l'utente preferisce vedere il testo visualizzato in una lingua diversa, impostare il parametro di query `setLang` sul codice lingua appropriato.

In alternativa è possibile specificare il paese/l'area usando il parametro di query `cc`. Se si specifica un paese/un'area, è necessario specificare anche uno o più codici di lingua usando l'intestazione HTTP `Accept-Language`. Le lingue supportate variano in base al paese/l'area e sono indicate per ogni paese nella tabella Mercati.



> [!NOTE]
> Al mercato si applicano le restrizioni seguenti:
>
> - Le annotazioni di riconoscimento di immagini sono disponibili solo in inglese.
> - Ricette, acquisti e approfondimenti con pagine sono disponibili solo per il mercato en-US.


## <a name="countriesregions"></a>Paesi/aree geografiche

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
