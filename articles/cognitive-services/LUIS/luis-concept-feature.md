---
title: Caratteristiche - LUIS
titleSuffix: Azure Cognitive Services
description: L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220996"
---
# <a name="machine-learned-features"></a>Funzioni apprese automaticamente 

Nell'apprendimento automatico, una _funzionalità_ è un tratto distintivo o un attributo dei dati che il sistema osserva & apprende. In Informazioni sulla lingua (LUIS), una funzionalità descrive e spiega ciò che è significativo sulle finalità e le entità.

Nel [portale LUIS di anteprima,](https://preview.luis.ai)le funzionalità sono _descrittori_ perché vengono utilizzate per _descrivere_ la finalità o l'entità.  

## <a name="features-_descriptors_-in-language-understanding"></a>Caratteristiche (_descrittori_) in Comprensione della lingua

Le funzionalità, note anche come descrittori, descrivono gli indizi che consentono a Informazioni sul linguaggio di identificare le espressioni di esempio. Funzionalità incluse: 

* Elenco di frasi come entità o finalità
* Entità come entità a finalità o entità

Le entità geografiche devono essere considerate come una parte necessaria dello schema per la scomposizione del modello. 

## <a name="what-is-a-phrase-list"></a>Che cos'è un elenco di frasiWhat is a phrase list

Un elenco di frasi è un elenco di parole, frasi, numeri o altri caratteri che consentono di identificare il concetto che si sta tentando di identificare. L'elenco non fa distinzione tra maiuscole e minuscole. 

## <a name="when-to-use-a-phrase-list"></a>Quando usare un elenco di frasiWhen to use a phrase list

Con un elenco di frasi, LUIS considera il contesto e generalizza per identificare gli elementi simili, ma non una corrispondenza esatta del testo. Se è necessario che l'app LUIS sia in grado di generalizzare e identificare nuovi elementi, usare un elenco di frasi. 

Quando si desidera essere in grado di riconoscere nuove istanze, ad esempio un'utilità di pianificazione riunioni che deve riconoscere i nomi dei nuovi contatti o un'app di inventario che deve riconoscere i nuovi prodotti, iniziare con un'entità appresa dal computer. Creare quindi un elenco di frasi che aiuti LUIS a trovare parole con un significato simile. Questo elenco di frasi guida LUIS a riconoscere gli esempi aggiungendo un significato aggiuntivo al valore di tali parole. 

Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità. 

## <a name="considerations-when-using-a-phrase-list"></a>Considerazioni sull'utilizzo di un elenco di frasi

Per impostazione predefinita, viene applicato un elenco di frasi a tutti i modelli dell'app. Questo funzionerà per gli elenchi di frasi che possono attraversare tutte le finalità e le entità. Per la decomposizione, è necessario applicare un elenco di frasi solo ai modelli a cui è rilevante. 

Se si crea un elenco di frasi (creato globalmente per impostazione predefinita), quindi applicarlo successivamente come descrittore (feature) a un modello specifico, viene rimosso dagli altri modelli. Questa rimozione aggiunge pertinenza all'elenco di frasi per il modello a cui viene applicato, contribuendo a migliorare la precisione fornita nel modello. 

Il `enabledForAllModels` flag controlla questo ambito del modello nell'API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Come utilizzare un elenco di frasi

[Crea un elenco](luis-how-to-add-features.md) di frasi quando la tua intenzione o entità ha parole o frasi importanti, come:

* termini di settore
* gergo
* abbreviazioni
* linguaggio specifico di un'azienda
* linguaggio derivato da un'altra lingua, ma usato spesso nell'app
* parole chiave o frasi nelle espressioni di esempio

**Non** aggiungere tutte le parole o le frasi possibili. Aggiungi invece alcune parole o frasi alla volta, quindi ripubblicala e pubblica. Man mano che l'elenco cresce nel tempo, è possibile che alcuni termini abbiano molte forme (sinonimi). Dividi questi in un'altra lista. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Quando usare un'entità come funzionalitàWhen to use an entity as a feature 

Un'entità può essere aggiunta come funzionalità a livello di finalità o di entità. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entità come entità a un intento

Aggiungere un'entità come descrittore (funzionalità) a una finalità quando il rilevamento di tale entità è significativo per la finalità.

Ad esempio, se l'intento è quello di prenotare un volo e l'entità è costituita da informazioni sul biglietto (ad esempio il numero di postazioni, l'origine e la destinazione), trovare l'entità delle informazioni sul biglietto deve aggiungere peso alla previsione dell'intento del volo del libro. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entità come funzionalità per un'altra entità

Un'entità (A) deve essere aggiunta come funzionalità a un'altra entità (B) quando il rilevamento di tale entità (A) è significativo per la stima dell'entità (B).

Ad esempio, se viene rilevata l'entità indirizzo di indirizzi (A), trovare l'indirizzo (A) aggiunge peso alla stima per l'entità dell'indirizzo di spedizione (B). 

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.