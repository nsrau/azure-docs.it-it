---
title: Supporto della localizzazione | Mappe di Microsoft Azure
description: Questo articolo illustra le lingue supportate per i servizi in Mappe di Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0d3adc4bc49379a9ec3408ab76b913a096840dbb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127894"
---
# <a name="localization-support-in-azure-maps"></a>Supporto della localizzazione in Mappe di Azure

Mappe di Azure supporta diverse lingue e viste in base al paese/area geografica. Questo articolo illustra le lingue e le viste supportate per aiutare nell'implementazione di Mappe di Azure.


## <a name="azure-maps-supported-languages"></a>Lingue supportate in Mappe di Azure

I servizi disponibili in Mappe di Azure sono stati localizzati in varie lingue. Nella tabella seguente sono elencati i codici della lingua supportata per ogni servizio.  
  

| ID         | Nome                   |  Mappe | Ricerca | Routing. | Weather | Eventi imprevisti del traffico | Controllo mappa JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabo                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bengalese (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (India)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bosniaco                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Basco                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Catalano                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Cinese (semplificato)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Cinese (Hong Kong - R.A.S.)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Cinese (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Croato               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Ceco                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Danese                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Olandese (Belgio)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Olandese (Paesi Bassi)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Inglese (Australia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Inglese (Nuova Zelanda)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Inglese (Gran Bretagna) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| it-IT      | Inglese (Stati Uniti)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estone               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filippino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finlandese                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francese                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francese (Canada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galiziano               |       |    ✓   |         |         |                   |                |
| de-DE      | Tedesco                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Greco                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Ebraico                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Ungherese              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Islandese              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonesiano             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Giapponese               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazako                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Spagnolo (America Latina) |       |    ✓   |         |         |                   |                |
| lv-LV      | Lettone                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedone             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malese (alfabeto latino)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norvegese bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Mappa con esonimi: lingue ufficiali per tutte le aree con lo specifico sistema di caratteri, se disponibile |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Mappa con esonimi: esonimi latini. Verrà usato il sistema di caratteri dell'alfabeto latino, se disponibile |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polacco                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portoghese (Brasile)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portoghese (Portogallo)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Punjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbo (alfabeto cirillico)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Serbo (alfabeto latino)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Slovacco             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Sloveno              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spagnolo                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spagnolo (Messico)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Svedese                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (India)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (India)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ucraino               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbeco                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Visualizzazioni supportate in Mappe di Azure

> [!Note]
> Il 1° agosto 2019, Mappe di Azure è stato rilasciato nei paesi/aree geografiche seguenti:
>  * Argentina
>  * India
>  * Marocco
>  * Pakistan
>
> Dopo il 1° agosto 2019, il parametro **View** definirà il contenuto delle mappe restituite per la nuova area geografica/il nuovo paese elencato in precedenza. Il parametro **View** di Mappe di Azure (anche noto come "parametro dell’area geografica dell’utente") è un codice paese ISO-3166 a due lettere che mostrerà le mappe corrette per il paese/l’area geografica specificando quale set di contenuti geopoliticamente contestati verrà restituito tramite i servizio di Mappe di Azure, compresi i confini e le etichette visualizzati sulla mappa. 

Assicurarsi di impostare il parametro **View** come richiesto per le API REST e gli SDK, che vengono usati dai servizi.
>  
>
>  **API REST:**
>  
>  Assicurarsi di aver configurato il parametro View come richiesto. Il parametro View specifica il set di contenuto geopoliticamente contestato che viene restituito tramite i servizi di Mappe di Azure. 
>
>  Servizi REST di Mappe di Azure interessati:
>    
>    * Get Map Tile
>    * Get Map Image 
>    * Get Search Fuzzy
>    * Get Search POI
>    * Get Search POI Category
>    * Get Search Nearby
>    * Get Search Address
>    * Get Search Address Structured
>    * Get Search Address Reverse
>    * Get Search Address Reverse Cross Street
>    * Post Search Inside Geometry
>    * Post Search Address Batch Preview
>    * Post Search Address Reverse Batch Preview
>    * Post Search Along Route
>    * Post Search Fuzzy Batch Preview
>
>    
>  **SDK:**
>
>  Assicurarsi di aver configurato il parametro **View** come richiesto e di disporre della versione più recente di Web SDK e Android SDK. SDK interessati:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Per impostazione predefinita, il parametro View è impostato su **Unified**, anche se non è stato definito nella richiesta. Determinare la posizione degli utenti. Quindi, impostare il parametro **View** correttamente per tale posizione. In alternativa, è possibile impostare "View = auto", che restituirà i dati della mappa in base all'indirizzo IP della richiesta.  Il parametro **View** parametro in Mappe di Azure deve essere usato in conformità alle leggi applicabili, incluse le leggi sul mapping del paese/area geografica in cui sono disponibili mappe, immagini e altri dati e contenuti di terze parti a cui si è autorizzati ad accedere tramite Mappe di Azure.


La tabella seguente contiene una matrice che indica le visualizzazioni supportate.

| Visualizzazione         | Descrizione                            |  Mappe | Ricerca | Controllo mappa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirati Arabi Uniti (visualizzazione araba)    |   ✓   |        |     ✓          |
| AR           | Argentina (visualizzazione argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (visualizzazione araba)                 |   ✓   |        |     ✓          |
| IN           | India (visualizzazione indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraq (visualizzazione araba)                    |   ✓   |        |     ✓          |
| JO           | Giordania (visualizzazione araba)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (visualizzazione araba)                  |   ✓   |        |     ✓          |
| LB           | Libano (visualizzazione araba)                 |   ✓   |        |     ✓          |
| MA           | Marocco (visualizzazione marocchina)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (visualizzazione araba)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (visualizzazione pakistana)              |   ✓   |    ✓    |     ✓          |
| PS           | Autorità Palestinese (visualizzazione araba)    |   ✓   |        |     ✓          |
| QA           | Qatar (visualizzazione araba)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudita (visualizzazione araba)            |   ✓   |        |     ✓          |
| SY           | Siria (visualizzazione araba)                   |   ✓   |        |     ✓          |
| YE           | Yemen (visualizzazione araba)                   |   ✓   |        |     ✓          |
| Auto         | Restituisce i dati della mappa in base all'indirizzo IP della richiesta.|   ✓   |    ✓   |     ✓          |
| Unificata      | Visualizzazione unificata (altri)                  |   ✓   |   ✓     |     ✓          |
