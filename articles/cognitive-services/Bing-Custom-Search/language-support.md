---
title: Supporto per la lingua - API Ricerca personalizzata Bing
titleSuffix: Azure Cognitive Services
description: Elenco delle lingue e delle regioni supportate dall'API Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 56870a63f42c10b48cc2d8f0ae2995862be46d8f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790235"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Lingua e regioni supportate dall'API Ricerca personalizzata Bing

L'API Ricerca personalizzata Bing supporta più di una quarantina di paesi/aree, molti dei quali con più di una lingua.

Nonostante sia facoltativo, la richiesta dovrebbe specificare il parametro di query [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt), che identifica il mercato da cui devono provenire i risultati. Per un elenco di parametri di query facoltativi, vedere [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) (Parametri di query)

È possibile specificare un paese/un'area usando il parametro di query `cc`. Se si specifica un paese/un'area, è necessario specificare anche uno o più codici di lingua usando l'intestazione `Accept-Language`. Le lingue supportate variano in base al paese/all'area e sono indicate per ogni paese nella tabella **Mercati**.

L'intestazione`Accept-Language` e il parametro di query `setLang` si escludono a vicenda, quindi non specificarli entrambi. Per informazioni dettagliate, vedere [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countriesregions"></a>Paesi/aree geografiche

|Paese/area geografica|Codice|
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
|Hong Kong - R.A.S.|HK|
|India|IN|
|Indonesia|ID|
|Italia|it|
|Giappone|JP|
|Corea del Sud|KR|
|Malesia|MY|
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
|Sudafrica|ZA|
|Spagna|ES|
|Svezia|SE|
|Svizzera|CH|
|Taiwan|TW|
|Turchia|TR|
|Regno Unito|GB|
|Stati Uniti|Stati Uniti|


## <a name="markets"></a>Mercati

|Paese/area geografica|Linguaggio|Codice di mercato|
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
|Hong Kong, SAR|Cinese tradizionale|zh-HK|
|India|Inglese|en-IN|
|Indonesia|Inglese|en-ID|
|Italia|Italiano|it-IT|
|Giappone|Giapponese|ja-JP|
|Corea del Sud|Coreano|ko-KR|
|Malesia|Inglese|en-MY|
|Messico|Spagnolo|es-MX|
|Paesi Bassi|Olandese|nl-NL|
|Nuova Zelanda|Inglese|en-NZ|
|Norvegia|Norvegese|no-NO|
|Cina|Cinese|zh-CN|
|Polonia|Polacco|pl-PL|
|Portogallo|Portoghese|pt-PT|
|Filippine|Inglese|en-PH|
|Russia|Russo|ru-RU|
|Arabia Saudita|Arabo|ar-SA|
|Sudafrica|Inglese|en-ZA|
|Spagna|Spagnolo|es-ES|
|Svezia|Svedese|sv-SE|
|Svizzera|Francese|fr-CH|
|Svizzera|Tedesco|de-CH|
|Taiwan|Cinese tradizionale|zh-TW|
|Turchia|Turco|tr-TR|
|Regno Unito|Inglese|en-GB|
|Stati Uniti|Inglese|en-US|
|Stati Uniti|Spagnolo|es-US|
