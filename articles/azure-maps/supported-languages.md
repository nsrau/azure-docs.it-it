---
title: Supporto per la localizzazione Mappe di Microsoft Azure
description: In this article, you'll learn about supported languages for the services in Microsoft Azure Maps.
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
# <a name="localization-support-in-azure-maps"></a>Supporto della localizzazione in Azure MapsLocal support in Azure Maps

Mappe di Azure supporta varie lingue e visualizzazioni in base al paese o all'area geografica. Questo articolo fornisce le lingue e le visualizzazioni supportate per guidare l'implementazione di Mappe di Azure.This article provides the supported languages and views to guide your Azure Maps implementation.


## <a name="azure-maps-supported-languages"></a>Lingue supportate in Mappe di Azure

Mappe di Azure sono state localizzate in diverse lingue nei suoi servizi. Nella tabella seguente sono elencati i codici della lingua supportata per ogni servizio.  
  

| ID         | Nome                   |  Mappe | Ricerca | Routing. | Weather | Eventi imprevisti del traffico | Controllo mappa JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabo                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bengalese (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bengali (India)         |       |       |         |     ✓    |                   |                |
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


## <a name="azure-maps-supported-views"></a>Visualizzazioni supportate di Azure MapsAzure Maps supported views

> [!Note]
> Il 1 agosto 2019, Azure Maps è stato rilasciato nei seguenti paesi/aree geografiche:
>  * Argentina
>  * India
>  * Marocco
>  * Pakistan
>
> Dopo il 1 agosto 2019, il parametro **View** definirà il contenuto della mappa restituito per le nuove regioni/paesi sopra elencati. Il parametro **di visualizzazione** di Azure Maps (denominato anche parametro dell'area utente") è un codice paese ISO-3166 di due lettere che mostrerà le mappe corrette per tale paese/area geografica specificando quale set di contenuto geopoliticamente contestato viene restituito tramite i servizi di Azure Maps, inclusi i bordi e le etichette visualizzati sulla mappa. 

Assicurarsi di impostare il parametro **View** come richiesto per le API REST e gli SDK in uso nei servizi.
>  
>
>  **API di riposo:**
>  
>  Assicurarsi di aver impostato il parametro View in base alle esigenze. Il parametro View consente di specificare il set di contenuto geopoliticamente contestato restituito tramite i servizi di Azure Maps.View parameter specifies which set of geopolitically disputed content is returned via Azure Maps services. 
>
>  Servizi REST di Azure Maps interessati:Affected Azure Maps REST Services:
>    
>    * Ottieni riquadro mappa
>    * Ottieni immagine mappa 
>    * Ottieni la ricerca fuzzy
>    * Ottieni ricerca POI
>    * Ottenere la categoria POI di ricercaGet Search POI Category
>    * Ottieni la ricerca nelle vicinanze
>    * Ottieni indirizzo di ricerca
>    * Ottenere l'indirizzo di ricerca strutturatoGet Search Address Structured
>    * Get Search Address Reverse
>    * Ottenere l'indirizzo di ricerca trasversale inverso
>    * Post Search Inside Geometry
>    * Post Search Address Batch Anteprima
>    * Post Ricerca Indirizzo Batch Inverso Anteprima
>    * Ricerca post lungo il percorso
>    * Post Ricerca Fuzzy Batch Anteprima
>
>    
>  **Sdk:**
>
>  Assicurarsi di aver impostato il parametro **View** come richiesto e di disporre della versione più recente di Web SDK e Android SDK. SDK interessati:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Per impostazione predefinita, il parametro View è impostato su **Unified**, anche se non è stato definito nella richiesta. Determinare la posizione degli utenti. Quindi, impostare correttamente il parametro **View** per tale posizione. In alternativa, è possibile impostare 'Visualizza-Auto', che restituirà i dati della mappa in base all'indirizzo IP della richiesta.  Il parametro **View** in Azure Maps deve essere usato in conformità alle leggi applicabili, incluse le leggi sul mapping del paese in cui vengono rese disponibili mappe, immagini e altri dati e altri contenuti di terze parti a cui sei autorizzato ad accedere tramite Azure Maps.


Nella tabella seguente vengono fornite le viste supportate.

| Visualizza         | Descrizione                            |  Mappe | Ricerca | Controllo mappa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirati Arabi Uniti (vista araba)    |   ✓   |        |     ✓          |
| AR           | Argentina (vista argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (vista araba)                 |   ✓   |        |     ✓          |
| IN           | India (vista indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraq (vista araba)                    |   ✓   |        |     ✓          |
| JO           | Giordania (vista araba)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (vista araba)                  |   ✓   |        |     ✓          |
| LB           | Libano (vista araba)                 |   ✓   |        |     ✓          |
| MA           | Marocco (vista marocchina)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (vista araba)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (vista pakistana)              |   ✓   |    ✓    |     ✓          |
| PS           | Autorità Palestinese (vista araba)    |   ✓   |        |     ✓          |
| QA           | Qatar (vista araba)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudita (vista araba)            |   ✓   |        |     ✓          |
| SY           | Siria (vista arabo)                   |   ✓   |        |     ✓          |
| YE           | Yemen (vista araba)                   |   ✓   |        |     ✓          |
| Auto         | Restituire i dati della mappa in base all'indirizzo IP della richiesta.|   ✓   |    ✓   |     ✓          |
| Unified      | Visualizzazione unificata (altri)                  |   ✓   |   ✓     |     ✓          |
