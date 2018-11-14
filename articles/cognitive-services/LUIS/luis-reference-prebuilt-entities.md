---
title: Entità predefinite - LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene gli elenchi delle entità predefinite incluse in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: bc23f2e5d8304400802c74093d4d78a1fcd8cf22
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219487"
---
# <a name="entities-per-culture"></a>Entità per impostazioni cultura

Language Understanding offre entità predefinite. Quando un'entità predefinita è inclusa nell'applicazione, LUIS include la stima dell'entità corrispondente nella risposta dell'endpoint. Anche tutte le espressioni di esempio vengono contrassegnate con l'entità. Il comportamento delle entità predefinite **non può** essere modificato. Se non indicato diversamente, le entità predefinite sono disponibili in tutte le impostazioni locali dell'applicazione LUIS, ovvero nelle impostazioni cultura. La tabella seguente riporta le entità predefinite supportate per ogni impostazione cultura.

Entità predefinita   |   Inglese (Stati Uniti)<br>```En-us```   |   Francese (Francia)<br>```fr-FR```   |   Italiano (Italia)<br>```it-IT```   |   Spagnolo (Spagna)<br>```es-ES```   |   Cinese<br>```zh-CN```   |   Tedesco<br>```de-DE```   |   Portoghese (Brasile)<br>```pt-BR```   |   Giapponese (Giappone)<br>```ja-JP```   |   Coreano (Corea)<br>```ko-kr```   | Francese (Canada)<br>```fr-CA```   |   Spagnolo (Messico)<br>```es-MX```   |   Olandese (Paesi Bassi)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Età](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Valuta](luis-reference-prebuilt-currency.md):<br>dollaro<br>unità frazionarie (ad esempio: centesimi)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervallo di tempo   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimensioni](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>peso<br>informazioni (ad esempio bit/byte)<br>lunghezza (ad esempio metro)<br>velocità (ad esempio miglio all'ora)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Indirizzo di posta elettronica](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Number](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Percentuale](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   |    -   |    -   |    -   |    ✔   |    -   |    -   |    -   |   -   |   -   |   -   |   -   |
[Numero di telefono](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Vedere le note in [Entità predefinite deprecate](luis-reference-prebuilt-deprecated.md)

KeyPhrase non è disponibile in tutte le impostazioni cultura secondarie del portoghese (Brasile) - ```pt-BR```.

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuire alle impostazioni cultura delle entità predefinite
Le entità predefinite vengono sviluppate nel progetto open source Recognizers-Text. [Contribuire](https://github.com/Microsoft/Recognizers-Text) al progetto. Questo progetto include esempi di valuta per le impostazioni cultura. 

GeographyV2 e PersonName non sono inclusi nel progetto Recognizers-Text. Per problemi relativi a queste entità predefinite, aprire una [richiesta di supporto](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [numero](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [valuta](luis-reference-prebuilt-currency.md). 
