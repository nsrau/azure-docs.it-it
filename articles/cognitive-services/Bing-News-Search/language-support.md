---
title: Supporto per la lingua - API Ricerca notizie Bing
titleSuffix: Azure Cognitive Services
description: Un elenco dei linguaggi naturali, paesi e aree geografiche supportate dall'API Ricerca notizie Bing.
services: cognitive-services
author: MikeDodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: v-gedod
ms.openlocfilehash: a8f7ccdb199f934011b6d64d813eb36cc9dff952
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804549"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Lingua e regioni supportate dall'API Ricerca notizie Bing

L'API Ricerca notizie Bing supporta numerosi paesi/aree, molti dei quali con più di una lingua. Specificare un paese/un'area con una query serve principalmente per perfezionare i risultati della ricerca in base agli interessi in tale paese/area. I risultati possono anche contenere collegamenti a Bing e questi collegamenti possono localizzare l'esperienza utente di Bing in base al paese/all'area o alla lingua specifica.

È possibile specificare un paese/un'area usando il parametro di query `cc`. Se si specifica un paese/un'area, è necessario specificare anche uno o più codici di lingua usando l'intestazione HTTP `Accept-Language`. Le lingue supportate variano in base al paese/all'area e sono indicate per ogni paese nella tabella Mercati.

In alternativa, è possibile specificare il mercato usando il parametro di query `mkt` e un codice della tabella **Mercati**. Specificando un mercato, vengono contemporaneamente specificati un paese/un'area e una lingua preferita. Il parametro di query `setLang` in questo caso può essere impostato su un codice della lingua, che corrisponde in genere alla lingua specificata da `mkt` a meno che l'utente non preferisca visualizzare Bing in un'altra lingua.

## <a name="supported-markets-for-news-search-endpoint"></a>Mercati supportati per l'endpoint di Ricerca notizie

Per l'endpoint `/news/search`, la tabella seguente elenca i valori dei codici di mercato che è possibile usare per specificare il parametro di query `mkt`. Bing restituisce il contenuto solo per questi mercati. L'elenco è soggetto a variazioni.  

Per un elenco di codici di paese/area che è possibile specificare nel parametro di query `cc`, vedere [Codici paese](#countrycodes).  

|Paese/Area geografica|Linguaggio|Codice di mercato|  
|---------------------|--------------|-----------------|
|Danimarca|Danese|da-DK|
|Austria|Tedesco|de-AT|
|Svizzera|Tedesco|de-CH|
|Germania|Tedesco|de-DE|
|Australia|Inglese|en-AU|
|Canada|Inglese|en-CA|
|Regno Unito|Inglese|en-GB|
|Indonesia|Inglese|en-ID|
|Irlanda|Inglese|en-IE|
|India|Inglese|en-IN|
|Malaysia|Inglese|en-MY|
|Nuova Zelanda|Inglese|en-NZ|
|Filippine|Inglese|en-PH|
|Singapore|Inglese|en-SG|
|Stati Uniti|Inglese|it-IT|
|Inglese|generale|en-WW|
|Inglese|generale|en-XA|
|Sud Africa|Inglese|en-ZA|
|Argentina|Spagnolo|es-AR|
|Cile|Spagnolo|es-CL|
|Spagna|Spagnolo|es-ES|
|Messico|Spagnolo|es-MX|
|Stati Uniti|Spagnolo|es-US|
|Spagnolo|generale|es-XL|
|Finlandia|Finlandese|fi-FI|  
|Francia|Francese|fr-BE|
|Canada|Francese|fr-CA|
|Belgio|Olandese|nl-BE|
|Svizzera|Francese|fr-CH|
|Francia|Francese|fr-FR|  
|Italia|Italiano|it-IT|
|RAS di Hong Kong|Cinese tradizionale|zh-HK|  
|Taiwan|Cinese tradizionale|zh-TW|
|Giappone|Giapponese|ja-JP|  
|Corea del Sud|Coreano|ko-KR|  
|Paesi Bassi|Olandese|nl-NL|  
|Repubblica Popolare Cinese|Cinese|zh-CN|  
|Brasile|Portoghese|pt-BR|
|Russia|Russo|ru-RU|  
|Svezia|Svedese|sv-SE|  
|Turchia|Turco|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Mercati supportati per l'endpoint delle notizie
Per l'endpoint `/news`, la tabella seguente elenca i valori dei codici di mercato che è possibile usare per specificare il parametro di query `mkt`. Bing restituisce il contenuto solo per questi mercati. L'elenco è soggetto a variazioni.  

Per un elenco di codici di paese/area che è possibile specificare nel parametro di query `cc`, vedere [Codici paese](#countrycodes).  

|Paese/Area geografica|Linguaggio|Codice di mercato|  
|---------------------|--------------|-----------------|
|Danimarca|Danese|da-DK|
|Germania|Tedesco|de-DE|
|Australia|Inglese|en-AU|
|Regno Unito|Inglese|en-GB|
|Stati Uniti|Inglese|it-IT|
|Inglese|generale|en-WW|
|Cile|Spagnolo|es-CL|
|Messico|Spagnolo|es-MX|
|Stati Uniti|Spagnolo|es-US|
|Finlandia|Finlandese|fi-FI|  
|Canada|Francese|fr-CA|
|Francia|Francese|fr-FR|  
|Italia|Italiano|it-IT|
|Brasile|Portoghese|pt-BR|
|Repubblica Popolare Cinese|Cinese|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Mercati supportati per l'endpoint delle notizie di tendenza
Per l'endpoint `/news/trendingtopics`, la tabella seguente elenca i valori dei codici di mercato che è possibile usare per specificare il parametro di query `mkt`. Bing restituisce il contenuto solo per questi mercati. L'elenco è soggetto a variazioni.  

Per un elenco di codici di paese/area che è possibile specificare nel parametro di query `cc`, vedere [Codici paese](#countrycodes).  

|Paese/Area geografica|Linguaggio|Codice di mercato|  
|---------------------|--------------|-----------------|
|Germania|Tedesco|de-DE|
|Australia|Inglese|en-AU|
|Regno Unito|Inglese|en-GB|
|Stati Uniti|Inglese|it-IT|
|Canada|Inglese|en-CA|
|India|Inglese|en-IN|
|Francia|Francese|fr-FR|
|Canada|Francese|fr-CA|
|Brasile|Portoghese|pt-BR|
|Repubblica Popolare Cinese|Cinese|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Codici paese  

I seguenti sono i codici di paese/area che è possibile specificare nel parametro di query `cc`. L'elenco è soggetto a variazioni.  

|Paese/Area geografica|Codice paese|  
|---------------------|------------------|  
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
|Repubblica Popolare Cinese|CN|  
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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli endpoint dell'API Ricerca notizie Bing, vedere [News Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) (Informazioni di riferimento sull'API Ricerca notizie v7).
