---
title: Supporto per la localizzazione nelle mappe di Azure | Microsoft Docs
description: Informazioni sulle lingue supportate per i servizi disponibili in Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1928185521419006a487a933e2ecba79894a09d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686774"
---
# <a name="localization-support-in-azure-maps"></a>Supporto per la localizzazione nelle mappe di Azure

Mappe di Azure supporta diversi linguaggi e le viste basate su paese/area geografica. Questo articolo fornisce le lingue supportate e le viste per fornire indicazioni di implementazione di mappe di Azure.


## <a name="azure-maps-supported-languages"></a>Lingue supportate in Mappe di Azure

I servizi disponibili in Mappe di Azure sono stati localizzati in varie lingue. Nella tabella seguente sono elencati i codici della lingua supportata per ogni servizio.  
  

| ID         | NOME                   |  Mappe | Ricerca | Routing. | Eventi imprevisti del traffico | Controllo mappa JS | Fuso orario |
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

Parametro di visualizzazione esegue il mapping di Azure (detta anche "parametro di area utente") è un codice paese ISO 3166 2 lettere che verranno visualizzati i mapping corretti per tale paese/area geografica che specifica il set di geopoliticamente conteso bordi e le etichette vengono visualizzate sulla mappa.  Per impostazione predefinita vista parametro è impostato su **"Unified"** .  Paesi/aree geografiche non incluse nell'elenco della visualizzazione predefinita sarà la visualizzazione di "Unified". È responsabilità dell'utente per determinare la posizione degli utenti e quindi impostare il parametro View correttamente per quel percorso. Il parametro View in Azure viene eseguito il mapping deve essere utilizzato in conformità alle leggi applicabili, comprese quelle relative alle mapping del paese in cui mappe, immagini e altri contenuti i dati e di terze parti a cui sei autorizzato ad per accedere tramite Azure viene eseguito il mapping viene reso disponibile.

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
| Unificato      | Visualizzazione unificata (altri)                  |   ✓   |   ✓     |     ✓          |
