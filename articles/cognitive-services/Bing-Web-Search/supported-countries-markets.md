---
title: Paesi, aree e lingue supportati dall'API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su quali paesi/aree e lingue sono supportati dall'API Ricerca Web Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: b954814d1c26e8afd24e0bb13e74c493f0fd2c55
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125120"
---
# <a name="countries-regions-and-languages-supported-by-the-bing-web-search-api"></a>Paesi, aree e lingue supportati dall'API Ricerca Web Bing

L'API Ricerca Web Bing supporta numerosi paesi o aree, molti dei quali con più di una lingua. Specificando un paese o un'area con una query è possibile perfezionare i risultati della ricerca in base agli interessi relativi a tale paese o area. I risultati possono includere collegamenti a Bing e questi collegamenti possono localizzare l'esperienza utente di Bing in base al paese, all'area o alla lingua specificata.

È possibile specificare un paese o un'area usando il parametro di query `cc`. Quando viene specificato un paese o un'area, è necessario indicare uno o più codici di lingua con l'[intestazione `Accept-Language`](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Usare la [tabella Mercati](#Markets) per un elenco delle lingue supportate nei singoli mercati.

In alternativa, è possibile specificare il mercato usando il parametro di query `mkt` e un codice della tabella **Mercati**. Specificando un mercato, vengono contemporaneamente specificati un paese o un'area e una lingua preferita. È possibile impostare in modo esplicito la lingua con il parametro di query `setLang`.

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
|Norvegia|Norvegese|no-NO|
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
