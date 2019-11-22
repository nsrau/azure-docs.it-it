---
title: Novità-Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Questo articolo viene aggiornato regolarmente con le notizie sui servizi cognitivi di Azure API Language Understanding.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: e03c7489268f3ad7076481de4498c5e202f654b6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280287"
---
# <a name="whats-new-in-language-understanding"></a>Novità di Language Understanding

Ecco cosa c'è di nuovo nel servizio. Questi elementi includono note sulla versione, video, post di Blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.  

## <a name="release-notes"></a>Note sulla versione 

### <a name="november-4-2019---ignite"></a>4 novembre 2019-Ignite

* Miglioramento della produttività degli sviluppatori
    * Disponibilità generale dell' [endpoint di stima V3](luis-migration-api-v3.md). 
    * Possibilità di importare ed esportare app con formato Lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). In questo modo viene spianato il modo di un processo di integrazione continua/recapito continuo effettivo. 
* Espansione del linguaggio
    * [Arabo e Hindi](luis-language-support.md) in versione di anteprima pubblica.
* Modelli predefiniti
    * [Domini predefiniti](luis-reference-prebuilt-domains.md) è ora disponibile a livello generale (GA)
    * [Entità predefinite](luis-reference-prebuilt-entities.md#japanese-entity-support) giapponesi: età, valuta, numero e percentuale non supportati in V3.
    * [Entità predefinite](luis-reference-prebuilt-entities.md#italian-entity-support) italiane: la risoluzione di età, valuta, dimensione, numero e percentuale è cambiata da V2.
* Esperienza utente migliorata nel [portale di Preview.Luis.ai](https://preview.luis.ai) : esperienza di assegnazione di etichette rinnovata per consentire la compilazione e il debug di modelli complessi. Provare le esercitazioni del portale di anteprima:
    * [Solo Intent](tutorial-intents-only.md)
    * [Entità Machine-learnable ricomponibile](tutorial-machine-learned-entity.md) 
* Avanzate funzionalità di comprensione della lingua: [creazione di modelli di linguaggio sofisticati](luis-concept-entity-types.md) con minor sforzo. 
* Definire le funzionalità di machine learning a livello di modello e consentire l'uso dei modelli come segnali ad altri modelli, ad esempio l'uso di entità come funzionalità per gli Intent e altre entità.
* Nuovi [limiti](luis-boundaries.md) espansi-valore massimo superiore per elenchi di frasi e frasi totali, nuovo modello come limite di funzionalità
* Estrai le informazioni dal testo nel formato della struttura della gerarchia profonda, rendendo più potenti le applicazioni di conversazione.

    ![immagine dell'entità Machine-Learned](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 settembre 2019

* Risorsa di creazione di Azure: [eseguire la migrazione adesso](luis-migration-authoring.md).
    * 500 app per ogni risorsa di Azure
    * 100 versioni per app
* Supporto turco per le entità predefinite
* Supporto italiano per datetimeV2

### <a name="july-23-2019"></a>23 luglio 2019

* Aggiornare i [Riconoscitori-testo](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) a 1.2.3
    * Riconoscimenti di età, temperatura, dimensione e valuta in italiano.
    * Miglioramento del riconoscimento delle festività in inglese per calcolare correttamente le date basate sul ringraziamento.
    * Miglioramenti del valore DateTime francese per ridurre i falsi positivi delle entità non di data e non temporali.
    * Supporto per calendario/scuola/anno fiscale e acronimi in DateRange inglese.
    * Riconoscimento PhoneNumber migliorato in cinese e giapponese.
    * Supporto migliorato per NumberRange in inglese.
    * Miglioramenti delle prestazioni.

### <a name="june-24-2019"></a>24 giugno 2019

* [OrdinalV2: entità predefinita](luis-reference-prebuilt-ordinal-v2.md) per supportare l'ordinamento, ad esempio Next, Previous e Last. Solo lingua inglese.

### <a name="may-6-2019---build-conference"></a>6 maggio, 2019-Build Conference

Le funzionalità seguenti sono state rilasciate alla conferenza Build 2019:

* [Anteprima della Guida alla migrazione dell'API V3](luis-migration-api-v3.md)
* [Dashboard di analisi migliorato](luis-how-to-use-dashboard.md)
* [Domini predefiniti migliorati](luis-reference-prebuilt-domains.md) 
* [Entità elenco dinamico](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entità esterne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blog

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video

### <a name="2019-build-videos"></a>video sulla compilazione di 2019

[Come usare l'intelligenza artificiale per la conversazione di Azure per la scalabilità dell'azienda per la prossima generazione](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aggiornamenti del servizio

[Annunci di aggiornamento di Azure per Servizi cognitivi](https://azure.microsoft.com/updates/?product=cognitive-services)
