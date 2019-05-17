---
title: Supporto per la lingua - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Un elenco dei linguaggi naturali, paesi e aree geografiche supportate dall'API Ricerca notizie Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: e657c4678c76e8ff667c1a3f30409fc157f52d16
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798251"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Lingua e regioni supportate dall'API Ricerca Web Bing

L'API Ricerca Web Bing supporta numerosi paesi o aree, molti dei quali con più di una lingua. Specificando un paese o un'area con una query è possibile perfezionare i risultati della ricerca in base agli interessi relativi a tale paese o area. I risultati possono includere collegamenti a Bing e questi collegamenti possono localizzare l'esperienza utente di Bing in base al paese, all'area o alla lingua specificata.

È possibile specificare un paese o un'area usando il parametro di query `cc`. Quando viene specificato un paese o un'area, è necessario indicare uno o più codici di lingua con l'[intestazione `Accept-Language`](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Usare la [tabella Mercati](#markets) per un elenco delle lingue supportate nei singoli mercati.

In alternativa, è possibile specificare il mercato usando il parametro di query `mkt` e un codice della tabella **Mercati**. Specificando un mercato, vengono contemporaneamente specificati un paese o un'area e una lingua preferita. È possibile impostare in modo esplicito la lingua con il parametro di query `setLang`.

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
|Hong Kong - R.A.S.|Cinese tradizionale|zh-HK|
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

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento per l'API Ricerca immagini Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
