---
title: Supporto della localizzazione nelle mappe di Azure | Microsoft Docs
description: Informazioni sulle lingue supportate per i servizi disponibili in Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299834"
---
# <a name="localization-support-in-azure-maps"></a>Supporto della localizzazione nelle mappe di Azure

Azure Maps supporta diversi linguaggi e visualizzazioni in base al paese/area geografica. Questo articolo fornisce le lingue e le visualizzazioni supportate per la guida dell'implementazione di Azure maps.


## <a name="azure-maps-supported-languages"></a>Lingue supportate in Mappe di Azure

I servizi disponibili in Mappe di Azure sono stati localizzati in varie lingue. Nella tabella seguente sono elencati i codici della lingua supportata per ogni servizio.  
  

| id         | NOME                   |  Mappe | Cerca | Routing. | Eventi imprevisti del traffico | Controllo mappa JS | Fuso orario |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arabo                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Basco                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Bulgaro              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | Catalano                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Cinese (semplificato)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Cinese (tradizionale)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | Croato               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Ceco                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Danese                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | Olandese                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Olandese (Belgio)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | Inglese (Australia)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | Inglese (Nuova Zelanda)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Inglese (Gran Bretagna) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Inglese (Stati Uniti)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estone               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finlandese                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Francese                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Francese (Canada)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galiziano               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Tedesco                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | Greco                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | Ebraico                 |       |    ✓   |         |         ✓         |                |     ✓     |
| hi-IN      | Hindi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Ungherese              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonesiano             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Giapponese               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazako                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Spagnolo (America Latina) |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Lettone                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | Malese (alfabeto latino)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norvegese bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Mappa con esonimi: lingue ufficiali per tutte le aree con lo specifico sistema di caratteri, se disponibile |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Mappa con esonimi: esonimi latini. Verrà usato il sistema di caratteri dell'alfabeto latino, se disponibile |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polacco                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Portoghese (Brasile)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Portoghese (Portogallo)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ro-RO      | Rumeno               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Serbo (alfabeto cirillico)     |       |    Serbo (alfabeto cirillico) (SR-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | Serbo (alfabeto latino)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Slovacco              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Sloveno              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Spagnolo                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Spagnolo (Messico)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Svedese                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ucraino               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnamita             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Viste supportate di Azure Maps

> [!Note]
> Azure Maps è stato rilasciato nei paesi/aree geografiche seguenti il 1 ° agosto 2019:
>  * Argentina
>  * India
>  * Marocco
>  * Pakistan
>
> Dopo il 1 ° agosto 2019, l'impostazione del parametro **View** definisce il contenuto della mappa restituito per le nuove aree/Paesi elencati sopra. Si consiglia di assicurarsi di aver configurato il parametro di visualizzazione come richiesto per le API REST e gli SDK utilizzati dai servizi.
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
>    * Ottenere l'indirizzo di ricerca inverso
>    * Ottenere l'indirizzo di ricerca inverso tra le strade
>    * Post-ricerca all'interno della geometria
>    * Anteprima del batch di indirizzi post-ricerca
>    * Post-ricerca-anteprima batch inversa dell'indirizzo
>    * Post-ricerca lungo la route
>    * Post Search fuzzy batch Preview
>
>    
>  **SDK**
>
>  Assicurarsi di aver configurato il parametro di visualizzazione come richiesto e di avere la versione più recente di Web SDK e Android SDK. SDK interessati:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Il parametro di **visualizzazione** mappe di Azure, detto anche "parametro dell'area utente", è un codice paese di due lettere ISO-3166 che mostra le mappe corrette per quel paese/area geografica specificando il set di contenuto con conflitti geopolitico che viene restituito tramite le mappe di Azure Servizi, inclusi bordi ed etichette visualizzati sulla mappa. 

Per impostazione predefinita, il parametro di visualizzazione è impostato su **unificato**, anche se non è stato definito nella richiesta. È responsabilità dell'utente determinare la posizione degli utenti e quindi impostare il parametro di visualizzazione correttamente per tale percorso. In alternativa, è possibile impostare ' View = auto ', che restituirà i dati della mappa in base all'indirizzo IP della richiesta.  Il parametro View in Maps di Azure deve essere usato in conformità alle leggi applicabili, incluse quelle relative al mapping del paese in cui sono disponibili mappe, immagini e altri dati e contenuti di terze parti a cui si è autorizzati ad accedere tramite le mappe di Azure.


Nella tabella seguente vengono fornite le visualizzazioni supportate.

| visualizzazione         | Descrizione                            |  Mappe | Cerca | controllo mappa JS |
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
| Automatico         | Restituisce i dati della mappa in base all'indirizzo IP della richiesta.|   ✓   |    ✓   |     ✓          |
| Unificato      | Visualizzazione unificata (altri)                  |   ✓   |   ✓     |     ✓          |
