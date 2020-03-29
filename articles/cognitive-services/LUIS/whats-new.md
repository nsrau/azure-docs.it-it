---
title: Novità - Language Understanding (LUIS)
description: Questo articolo viene aggiornato regolarmente con le notizie sull'API di comprensione del linguaggio di Servizi cognitivi di Azure.This article is regularly updated with news about the Azure Cognitive Services Language Understanding API.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 562f7f8b4974363daab91991e6a8219b352432fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156005"
---
# <a name="whats-new-in-language-understanding"></a>Novità di Language Understanding

Ecco cosa c'è di nuovo nel servizio. Questi elementi includono note sulla versione, video, post di blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.

## <a name="release-notes"></a>Note sulla versione

### <a name="march-2020"></a>Marzo 2020

* TLS 1.2 è ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [Sicurezza dei Servizi cognitivi](../cognitive-services-security.md)di Azure .For more information, see Azure Cognitive Services security .

### <a name="november-4-2019---ignite"></a>4 novembre 2019 - Ignite

* Video - [Modelli NLU (Natural Language Understanding) avanzati con LUIS e Servizi cognitivi di Azure . BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Produttività degli sviluppatori migliorata
    * Disponibilità generale [dell'endpoint di stima V3.](luis-migration-api-v3.md)
    * Possibilità di importare ed esportare app con formato .lu ([LUDown).](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) Questo apre la strada a un efficace processo CI/CD.
* Espansione della lingua
    * [Arabo e Hindi](luis-language-support.md) in anteprima pubblica.
* Modelli predefiniti
    * [I domini predefiniti](luis-reference-prebuilt-domains.md) sono ora disponibili in genere (GA)
    * Le [entità predefinite](luis-reference-prebuilt-entities.md#japanese-entity-support) giapponesi: età, valuta, numero e percentuale non sono supportate in V3.
    * [Entità predefinite](luis-reference-prebuilt-entities.md#italian-entity-support) italiane: età, valuta, dimensione, numero e risoluzione percentuale modificate rispetto al V2.
* Esperienza utente migliorata nel [portale preview.luis.ai:](https://preview.luis.ai) esperienza di etichettatura rinnovata per consentire la creazione e il debug di modelli complessi. Prova le esercitazioni del portale di anteprima:
    * [Solo finalità](tutorial-intents-only.md)
    * [Entità scomposta appresa dal computer](tutorial-machine-learned-entity.md)
* Capacità di comprensione del linguaggio avanzate - creazione di [modelli linguistici sofisticati](luis-concept-entity-types.md) con meno sforzo.
* Definire le funzionalità di apprendimento automatico a livello di modello e consentire l'utilizzo di modelli come segnali ad altri modelli, ad esempio l'utilizzo di entità come entità per finalità e altre entità.
* Nuovi [limiti](luis-boundaries.md) espansi - massimo più alto per elenchi di frasi e frasi totali, nuovo modello come limiti di funzionalità
* Estrarre informazioni dal testo nel formato della struttura gerarchica profonda, rendendo le applicazioni di conversazione più potenti.

    ![immagine di entità appresa automaticamente](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>giovedì 3 settembre 2019

* Risorsa di creazione di Azure - [eseguire ora la migrazione](luis-migration-authoring.md).
    * 500 app per risorsa di Azure50 apps per Azure resource
    * 100 versioni per app
* Supporto turco per le entità predefinite
* Supporto italiano per datetimeV2

### <a name="july-23-2019"></a>giovedì 23 luglio 2019

* Aggiornare il [testo dei riconoscitori](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) alla 1.2.3
    * Sistemi di riconoscimento di età, temperatura, dimensione e valuta in italiano.
    * Miglioramento del riconoscimento vacanze in inglese per calcolare correttamente le date basate sul Ringraziamento.
    * Miglioramenti in DateTime francese per ridurre i falsi positivi delle entità non-Date e non Time.
    * Supporto per calendario/scuola/anno fiscale e acronimi in inglese DateRange.
    * Miglioramento del riconoscimento PhoneNumber in cinese e giapponese.
    * Supporto migliorato per NumberRange in inglese.
    * Miglioramenti delle prestazioni.

### <a name="june-24-2019"></a>giovedì 24 giugno 2019

* [Entità predefinita OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) per supportare l'ordine, ad esempio successivo, precedente e ultimo. Solo cultura inglese.

### <a name="may-6-2019---build-conference"></a>6 maggio 2019 - //Build Conference

Le seguenti funzionalità sono state rilasciate alla conferenza Build 2019:

* [Anteprima della guida alla migrazione delle API V3](luis-migration-api-v3.md)
* [Dashboard di analisi migliorato](luis-how-to-use-dashboard.md)
* [Domini predefiniti migliorati](luis-reference-prebuilt-domains.md)
* [Entità elenco dinamico](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entità esterne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blog

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video

### <a name="2019-ignite-videos"></a>Video di Ignite 2019

[Modelli nLU (Natural Language Understanding) avanzati che usano LUIS e Servizi cognitivi di Azure BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>Video sulla creazione 2019

[Come usare l'iA conversazionale di Azure per ridimensionare l'azienda per la prossima generazioneHow to use Azure Conversational AI to scale your business for the next generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aggiornamenti del servizio

[Annunci di aggiornamento di Azure per Servizi cognitivi](https://azure.microsoft.com/updates/?product=cognitive-services)
