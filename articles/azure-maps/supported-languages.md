---
title: Supporto per la localizzazione | Mappe Microsoft Azure
description: In questo articolo verranno illustrate le lingue supportate per i servizi in Microsoft Azure maps.
author: philmea
ms.author: philmea
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b3733dfc32dae21ddcf4c5f73cddf9ad6b7fc59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334078"
---
# <a name="localization-support-in-azure-maps"></a>Supporto della localizzazione nelle mappe di Azure

Azure Maps supporta diversi linguaggi e visualizzazioni in base al paese/area geografica. Questo articolo fornisce le lingue e le visualizzazioni supportate per la guida dell'implementazione di Azure maps.


## <a name="azure-maps-supported-languages"></a>Lingue supportate in Mappe di Azure

Le mappe di Azure sono state localizzate in diversi linguaggi tra i servizi. Nella tabella seguente sono elencati i codici della lingua supportata per ogni servizio.  
  

| ID         | Nome                   |  Mappe | Ricerca | Routing | Weather | Eventi imprevisti del traffico | Controllo mappa JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabo                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bengalese (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bengali (India)         |       |       |         |     ✓    |                   |                |
| BS-BA      | Bosniaco                 |       |       |         |     ✓    |                   |                |
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
| fil-ph     | Filippino               |       |       |         |     ✓    |                   |                |
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
| sr-Cyrl-RS | Serbo (alfabeto cirillico)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | Serbo (alfabeto latino)        |       |       |         |     Sr-Latn    |                   |                |
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


## <a name="azure-maps-supported-views"></a>Viste supportate di Azure Maps

> [!Note]
> Il 1 ° agosto 2019, Azure Maps è stato rilasciato nei paesi/aree geografiche seguenti:
>  * Argentina
>  * India
>  * Marocco
>  * Pakistan
>
> Dopo il 1 ° agosto 2019, il parametro **View** definirà il contenuto della mappa restituito per le nuove aree/Paesi elencati sopra. Il parametro di **visualizzazione** mappe di Azure, noto anche come "parametro dell'area utente", è un codice paese a due lettere ISO-3166 che mostra le mappe corrette per quel paese/area geografica specificando il set di contenuto geopoliticomente contestato che viene restituito tramite i servizi Maps di Azure, inclusi i bordi e le etichette visualizzati sulla mappa. 

Assicurarsi di impostare il parametro di **visualizzazione** come richiesto per le API REST e gli SDK, usati dai servizi.
>  
>
>  **API REST:**
>  
>  Assicurarsi di aver configurato il parametro di visualizzazione come richiesto. Il parametro View specifica il set di contenuto geopoliticomente contestato che viene restituito tramite i servizi di Azure maps. 
>
>  Servizi REST di Azure Maps interessati:
>    
>    * Ottenere il riquadro Mappa
>    * Ottieni immagine mappa 
>    * Ottenere la ricerca fuzzy
>    * Ottenere il PDI di ricerca
>    * Ottenere la categoria del PDI di ricerca
>    * Ottenere la ricerca nelle vicinanze
>    * Ottenere l'indirizzo di ricerca
>    * Ottenere l'indirizzo di ricerca strutturato
>    * Get Search Address Reverse
>    * Ottenere l'indirizzo di ricerca inverso tra le strade
>    * Post Search Inside Geometry
>    * Anteprima del batch di indirizzi post-ricerca
>    * Post-ricerca-anteprima batch inversa dell'indirizzo
>    * Post-ricerca lungo la route
>    * Post Search fuzzy batch Preview
>
>    
>  **SDK**
>
>  Assicurarsi di aver configurato il parametro di **visualizzazione** come richiesto e di avere la versione più recente di Web SDK e Android SDK. SDK interessati:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Per impostazione predefinita, il parametro di visualizzazione è impostato su **unificato**, anche se non è stato definito nella richiesta. Determinare la posizione degli utenti. Quindi, impostare il parametro **View** correttamente per tale percorso. In alternativa, è possibile impostare ' View = auto ', che restituirà i dati della mappa in base all'indirizzo IP della richiesta.  Il parametro **View** in Maps di Azure deve essere usato in conformità alle leggi applicabili, incluse le leggi sul mapping del paese in cui sono disponibili mappe, immagini e altri dati e contenuti di terze parti a cui si è autorizzati ad accedere tramite le mappe di Azure.


Nella tabella seguente vengono fornite le visualizzazioni supportate.

| Visualizzazione         | Descrizione                            |  Mappe | Ricerca | controllo mappa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirati Arabi Uniti (visualizzazione araba)    |   ✓   |        |     ✓          |
| AR           | Argentina (vista Argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (visualizzazione araba)                 |   ✓   |        |     ✓          |
| IN           | India (vista indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraq (visualizzazione araba)                    |   ✓   |        |     ✓          |
| JO           | Giordania (visualizzazione araba)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (visualizzazione araba)                  |   ✓   |        |     ✓          |
| LB           | Libano (visualizzazione araba)                 |   ✓   |        |     ✓          |
| MA           | Marocco (visualizzazione marocchina)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (visualizzazione araba)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (vista pakistana)              |   ✓   |    ✓    |     ✓          |
| PS           | Autorità palestinese (visualizzazione araba)    |   ✓   |        |     ✓          |
| QA           | Qatar (visualizzazione araba)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudita (visualizzazione araba)            |   ✓   |        |     ✓          |
| SY           | Siria (visualizzazione araba)                   |   ✓   |        |     ✓          |
| YE           | Yemen (visualizzazione araba)                   |   ✓   |        |     ✓          |
| Auto         | Restituisce i dati della mappa in base all'indirizzo IP della richiesta.|   ✓   |    ✓   |     ✓          |
| Unificata      | Visualizzazione unificata (altri)                  |   ✓   |   ✓     |     ✓          |
