---
title: Lingue e paesi supportati dall'API Ricerca Web Bing in Azure | Microsoft Docs
description: Informazioni su quali paesi e lingue sono supportati dall'API Ricerca Web Bing.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 7b62c4a4feb7144662a8fe4d692f11f1efe5db1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373305"
---
# <a name="bing-web-search-countries-and-languages"></a>Lingue e paesi di Ricerca Web Bing

L'API Ricerca Web Bing supporta numerosi paesi, molti dei quali con più di una lingua. Specificare un paese con una query serve principalmente per perfezionare i risultati della ricerca in base agli interessi in tale paese. I risultati possono anche contenere collegamenti a Bing e questi collegamenti possono localizzare l'esperienza utente di Bing in base al paese o alla lingua specifica.

È possibile specificare un paese usando il parametro di query `cc`. Se si specifica un paese, è necessario specificare anche uno o più codici di lingua usando l'intestazione HTTP `Accept-Language`. Le lingue supportate variano in base al paese e sono indicate per ogni paese nella tabella Mercati.

In alternativa, è possibile specificare il mercato usando il parametro di query `mkt` e un codice della tabella **Mercati**. Specificando un mercato, vengono contemporaneamente specificati un paese e una lingua preferita. Il parametro di query `setLang` in questo caso può essere impostato su un codice della lingua, che corrisponde in genere alla lingua specificata da `mkt` a meno che l'utente non preferisca visualizzare Bing in un'altra lingua.

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
