---
title: Tutte le entità predefinite
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene gli elenchi delle entità predefinite incluse in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: 96157511c0255774a64c0f64a333fe88a37f80d6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261426"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entità per impostazioni cultura nel modello LUIS

Language Understanding offre entità predefinite. Quando un'entità predefinita è inclusa nell'applicazione, LUIS include la stima dell'entità corrispondente nella risposta dell'endpoint. Anche tutte le espressioni di esempio vengono contrassegnate con l'entità. Il comportamento delle entità predefinite **non può** essere modificato. Se non indicato diversamente, le entità predefinite sono disponibili in tutte le impostazioni locali dell'applicazione LUIS, ovvero nelle impostazioni cultura. La tabella seguente riporta le entità predefinite supportate per ogni impostazione cultura.

|Impostazioni cultura|Impostazioni cultura secondarie|Note|
|--|--|--|
|Cinese|[zh-CN](#chinese-entity-support)||
|Olandese|[nl-NL](#dutch-entity-support)||
|Inglese|[en-US (Stati Uniti)](#english-american-entity-support)||
|Francese|[fr-CA (Canada)](#french-canadian-entity-support), [fr-FR (Francia)](#french-france-entity-support), ||
|Tedesco|[de-DE](#german-entity-support)||
|Italiano|[it-IT](#italian-entity-support)||
|Giapponese|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Portoghese|[pt-BR (Brasile)](#portuguese-brazil-entity-support)||
|Spagnolo|[es-ES (Spagna)](#spanish-spain-entity-support), [es-MX (Messico)](#spanish-mexico-entity-support)||
|Turco|[turkish](#turkish-entity-support)|Nessuna entità predefinite supportate in turco|

## <a name="chinese-entity-support"></a>Supporto entità cinesi

Sono supportate le entità seguenti:

|Entità predefinita|```zh-CN``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Supporto entità olandesi

Sono supportate le entità seguenti:

|Entità predefinita|```nl-NL``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    -   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Supporto entità inglesi (Stati Uniti)

Sono supportate le entità seguenti:

|Entità predefinita|```en-US``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Supporto di entità francesi (Francia)

Sono supportate le entità seguenti:

|Entità predefinita|```fr-FR``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Supporto entità francesi (Canada)

Sono supportate le entità seguenti:

|Entità predefinita|```fr-CA``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Supporto entità tedesche

Sono supportate le entità seguenti:

|Entità predefinita|```de-DE``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Supporto entità italiane

Sono supportate le entità seguenti:

|Entità predefinita|```it-IT``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    -   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Supporto entità giapponesi

Sono supportate le entità seguenti:

|Entità predefinita|```ja-JP``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    -   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Supporto entità coreane

Sono supportate le entità seguenti:

|Entità predefinita|```ko-KR``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    -   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    -   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Supporto entità portoghesi (Brasile)

Sono supportate le entità seguenti:

|Entità predefinita|```pt-BR``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Supporto entità spagnole (Spagna)

Sono supportate le entità seguenti:

|Entità predefinita|```es-ES``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Supporto entità spagnole (Messico)

Sono supportate le entità seguenti:

|Entità predefinita|```es-MX``` |
------|:------:|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Valuta (denaro)](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    -   | 
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    -   | 
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentuale](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Vedere le note in [Entità predefinite deprecate](luis-reference-prebuilt-deprecated.md)

KeyPhrase non è disponibile in tutte le impostazioni cultura secondarie del portoghese (Brasile) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Supporto entità turche

**Non sono presenti entità predefiniti supportati in turco.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuire alle impostazioni cultura delle entità predefinite
Le entità predefinite vengono sviluppate nel progetto open source Recognizers-Text. [Contribuire](https://github.com/Microsoft/Recognizers-Text) al progetto. Questo progetto include esempi di valuta per le impostazioni cultura. 

GeographyV2 e PersonName non sono inclusi nel progetto Recognizers-Text. Per problemi relativi a queste entità predefinite, aprire una [richiesta di supporto](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [numero](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [valuta](luis-reference-prebuilt-currency.md). 
