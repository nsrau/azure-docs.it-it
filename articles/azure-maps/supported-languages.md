---
title: Supporto per la localizzazione nelle mappe di Azure | Microsoft Docs
description: Informazioni sulle lingue supportate per i servizi disponibili in Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446177"
---
# <a name="localization-support-in-azure-maps"></a>Supporto per la localizzazione nelle mappe di Azure

Mappe di Azure supporta diversi linguaggi e le viste basate su paese/area geografica. Questo articolo fornisce le lingue supportate e le viste per fornire indicazioni di implementazione di mappe di Azure.


## <a name="azure-maps-supported-languages"></a>Lingue supportate in Mappe di Azure

I servizi disponibili in Mappe di Azure sono stati localizzati in varie lingue. Nella tabella seguente sono elencati i codici della lingua supportata per ogni servizio.  
  

| id         | NOME                   |  Mappe | Ricerca | Routing. | Eventi imprevisti del traffico | Controllo mappa JS | Fuso orario |
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
| sr-Cyrl-RS | Serbo (alfabeto cirillico)     |       |    Serbo (alfabeto cirillico) (sr-RS)   |         |                   |                |     ✓     |
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


## <a name="azure-maps-supported-views"></a>Mappe di Azure supportati viste

> [!Note]
> 1 agosto 2019 verrà rilasciato le mappe di Azure in paesi/aree geografiche seguenti:
>  * Argentina
>  * India
>  * Marocco
>  * Pakistan
>
> Dopo il 1 agosto 2019 **vista** impostazione parametro consente di definire il contenuto della mappa restituita per le nuove aree/paesi elencati in precedenza. È consigliabile assicurarsi di aver impostato il parametro View come richiesto per le API REST e SDK che usano i servizi.
>  
>
>  **API REST:**
>  
>  Assicurarsi di che avere configurato il parametro View in base alle esigenze. Parametro View consente di specificare quale set di contenuti geopoliticamente contestato vengono restituite tramite servizi di mappe di Azure. 
>
>  Servizi REST di mappe di Azure interessato:
>    
>    * Ottenere tessera mappa
>    * Ottieni immagine mappa 
>    * Ecco la ricerca Fuzzy
>    * Introduzione a ricerca di punto di interesse
>    * Ottenere la categoria di punto di interesse ricerca
>    * Ecco la ricerca nelle vicinanze
>    * Ottenere l'indirizzo di ricerca
>    * Ottenere l'indirizzo di ricerca strutturata
>    * Ottenere l'indirizzo di ricerca inversa
>    * Ottieni ricerca indirizzi inversa Cross Street
>    * Ricerca di post all'interno della geometria
>    * Anteprima di Batch indirizzo ricerca post
>    * Post ricerca indirizzi inversa Batch Preview
>    * Ricerca di post lungo la Route
>    * Anteprima di Batch Fuzzy Search post
>
>    
>  **SDK:**
>
>  Assicurarsi che è stato impostato il parametro View in base alle esigenze e avere la versione più recente di Web SDK e Android SDK. SDK interessati:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Mappe di Azure **vista** parametro (detta anche "parametro di area utente") è una lettera due codice paese ISO 3166 che mostra le mappe corrette per tale paese/area geografica che specifica il set di geopoliticamente conteso contenuto è restituito tramite servizi di mappe di Azure, inclusi i bordi e le etichette visualizzate sulla mappa. 

Per impostazione predefinita vista parametro è impostato su **unificato**, anche se è non stata definita nella richiesta. È responsabilità dell'utente per determinare la posizione degli utenti e quindi impostare il parametro View correttamente per quel percorso. In alternativa, è possibile impostare ' View = Auto', che verranno restituiti i dati della mappa in base all'indirizzo IP della richiesta.  Il parametro View in Azure viene eseguito il mapping deve essere utilizzato in conformità alle leggi applicabili, comprese quelle relative alle mapping del paese in cui mappe, immagini e altri contenuti i dati e di terze parti a cui sei autorizzato ad per accedere tramite Azure viene eseguito il mapping viene reso disponibile.


Nella tabella seguente fornisce visualizzazioni supportate.

| visualizzazione         | Descrizione                            |  Mappe | Ricerca | Controllo mappa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirati Arabi Uniti (visualizzazione arabo)    |   ✓   |        |     ✓          |
| AR           | Argentina (visualizzazione Argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (visualizzazione arabo)                 |   ✓   |        |     ✓          |
| IN           | India (visualizzazione indiano)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraq (visualizzazione arabo)                    |   ✓   |        |     ✓          |
| JO           | Giordania (visualizzazione arabo)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (visualizzazione arabo)                  |   ✓   |        |     ✓          |
| LB           | Libano (visualizzazione arabo)                 |   ✓   |        |     ✓          |
| MA           | Marocco (visualizzazione (Marocco))                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (visualizzazione arabo)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (Pakistani visualizzazione)              |   ✓   |    ✓    |     ✓          |
| PS           | Autorità Palestinese (visualizzazione arabo)    |   ✓   |        |     ✓          |
| QA           | (Visualizzazione arabo) Qatar                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudita (visualizzazione arabo)            |   ✓   |        |     ✓          |
| SY           | (Visualizzazione arabo) Siria                   |   ✓   |        |     ✓          |
| YE           | Yemen (visualizzazione arabo)                   |   ✓   |        |     ✓          |
| Auto         | Restituire i dati della mappa in base all'indirizzo IP della richiesta.|   ✓   |    ✓   |     ✓          |
| Unificato      | Visualizzazione unificata (altri)                  |   ✓   |   ✓     |     ✓          |
