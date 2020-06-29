---
title: Novità - Language Understanding (LUIS)
description: Questo articolo viene aggiornato regolarmente e include le novità sull'API Language Understanding di Servizi cognitivi di Azure.
ms.topic: overview
ms.date: 06/15/2020
ms.openlocfilehash: d178ee2f5db74949f4a8ad68df93bf3c4407c58a
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789209"
---
# <a name="whats-new-in-language-understanding"></a>Novità di Language Understanding

Ecco cosa c'è di nuovo nel servizio. Le novità riguardano note sulla versione, video, post di blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.

## <a name="release-notes"></a>Note sulla versione

### <a name="june-2020"></a>Giugno 2020

* [Anteprima della creazione di 3,0](luis-migration-authoring-entities.md) SDK
    * Versione 3.2.0-Preview. 3- [.NET-NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Versione 4.0.0-Preview. 3- [JS-NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Applicazione delle procedure di DevOps con LUIS
    * Concetti
        * [Procedure DevOps per LUIS](luis-concept-devops-sourcecontrol.md)
        * [Flussi di lavoro di integrazione continua e recapito continuo per LUIS DevOps](luis-concept-devops-automation.md)
        * [Test per LUIS DevOps](luis-concept-devops-testing.md)
    * Procedure
        * [Applicare DevOps per lo sviluppo di app LUIS usando le azioni di GitHub](luis-how-to-devops-with-github.md)
    * [Completare il repository GitHub del codice](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Maggio 2020 - //Build

* Rilasciato come **disponibile a livello generale** (GA):
    * [Contenitore Language Understanding](luis-container-howto.md)
    * Portale di anteprima promosso a [portale corrente](https://www.luis.ai), ma portale [precedente](https://previous.luis.ai) ancora disponibile
    * Nuova esperienza di creazione ed etichettatura per le entità di Machine Learning
    * [Processo di aggiornamento](migrate-from-composite-entity.md) da entità composite e semplici a entità basate su Machine Learning
    * Supporto delle [impostazioni](how-to-application-settings-portal.md) per la normalizzazione delle varianti di parole
* Anteprima delle modifiche apportate all'API di creazione
    * Schema delle app 7.x per entità annidate basate su Machine Learning
    * [Migrazione alla funzionalità obbligatoria](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Nuove risorse per sviluppatori
    * [Strumenti di integrazione continua](developer-reference-resource.md#continuous-integration-tools)
    * Workshop: informazioni sulle procedure consigliate per [_Natural Language Understanding_ (NLU) con LUIS](developer-reference-resource.md#workshops)
* [Chiavi gestite dal cliente](luis-encryption-of-data-at-rest.md): crittografare tutti i dati usati in LUIS usando le proprie chiavi
* [Presentazione di IA](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (video): vedere le nuove funzionalità di LUIS



### <a name="march-2020"></a>Marzo 2020

* TLS 1.2 viene ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [Sicurezza di Servizi cognitivi di Azure](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 novembre 2019 - Ignite

* Video: [Modelli avanzati di riconoscimento del linguaggio naturale con LUIS e Servizi cognitivi di Azure | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Miglioramento della produttività degli sviluppatori
    * Disponibilità generale dell'[endpoint di stima V3](luis-migration-api-v3.md).
    * Possibilità di importare ed esportare app in formato `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Questa novità pone le basi per un processo CI/CD efficace.
* Altre lingue disponibili
    * [Arabo e hindi](luis-language-support.md) in anteprima pubblica.
* Modelli predefiniti
    * La funzionalità [Domini predefiniti](luis-reference-prebuilt-domains.md) è ora disponibile a livello generale
    * Le [entità predefinite](luis-reference-prebuilt-entities.md#japanese-entity-support) per il giapponese, ovvero age, currency, number e percentage, non sono supportate nella versione V3.
    * Le [entità predefinite](luis-reference-prebuilt-entities.md#italian-entity-support) per l'italiano, ovvero age, currency, dimension, number e percentage resolution sono cambiate rispetto alla versione V2.
* Miglioramento dell'esperienza utente nel [portale preview.luis.ai](https://preview.luis.ai): l'esperienza di assegnazione di etichette è stata riprogettata per consentire la compilazione e il debug di modelli complessi. Provare le esercitazioni del portale di anteprima:
    * [Solo finalità](tutorial-intents-only.md)
    * [Entità scomponibile basata su Machine Learning](tutorial-machine-learned-entity.md)
* Funzionalità avanzate di riconoscimento del linguaggio: [creazione di modelli linguistici sofisticati](luis-concept-entity-types.md) con minore sforzo.
* Definizione delle funzionalità di Machine Learning a livello di modello e possibilità di usare modelli come segnali per altri modelli, ad esempio usare entità come funzionalità per le finalità e per altre entità.
* Novità - Espansione dei [limiti](luis-limits.md): nuovo limite massimo per elenchi di frasi e numero totale di frasi, nuovo modello come limiti di funzionalità
* Estrazione di informazioni dal testo nel formato della struttura della gerarchia profonda, che rende ancor più potenti le applicazioni di conversazione.

    ![Immagine di entità di Machine Learning](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 settembre 2019

* Risorsa di creazione di Azure: [Eseguire subito la migrazione](luis-migration-authoring.md).
    * 500 app per ogni risorsa di Azure
    * 100 versioni per ogni app
* Supporto della lingua turco per le entità predefinite
* Supporto della lingua italiana per datetimeV2

### <a name="july-23-2019"></a>23 luglio 2019

* Aggiornamento di [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) alla versione 1.2.3
    * Sistemi di riconoscimento per età, temperatura, dimensione e valuta in lingua italiana.
    * Miglioramento del riconoscimento delle festività in lingua inglese per il calcolo corretto delle date basate sul Giorno del Ringraziamento.
    * Miglioramenti di DateTime in lingua francese per ridurre i falsi positivi relativi a entità diversi da Date e Time.
    * Supporto per anno di calendario/scolastico/fiscale e acronimi in DateRange per la lingua inglese.
    * Miglioramento del riconoscimento di PhoneNumber per le lingue cinese e giapponese.
    * Miglioramento del supporto per NumberRange in lingua inglese.
    * Miglioramenti delle prestazioni.

### <a name="june-24-2019"></a>24 giugno 2019

* [Entità predefinita OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) per supportare l'ordinamento, come next, previous, and last. Solo impostazioni cultura inglese.

### <a name="may-6-2019---build-conference"></a>6 maggio 2019 - Conferenza //Build

In occasione della conferenza Build 2019 sono state rilasciate le funzionalità seguenti:

* [Anteprima della Guida alla migrazione dell'API V3](luis-migration-api-v3.md)
* [Miglioramento del dashboard di analisi](luis-how-to-use-dashboard.md)
* [Miglioramento dei domini predefiniti](luis-reference-prebuilt-domains.md)
* [Entità elenco dinamiche](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entità esterne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blog

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video

### <a name="2019-ignite-videos"></a>Video di Ignite 2019

[Modelli avanzati di riconoscimento del linguaggio naturale con LUIS e Servizi cognitivi di Azure | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>Video di Build 2019

[Come usare l'intelligenza artificiale per conversazioni di Azure per preparare l'azienda per la prossima generazione](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aggiornamenti del servizio

[Annunci di aggiornamento di Azure per Servizi cognitivi](https://azure.microsoft.com/updates/?product=cognitive-services)
