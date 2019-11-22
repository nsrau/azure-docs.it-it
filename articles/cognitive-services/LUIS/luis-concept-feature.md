---
title: Funzionalità-LUIS
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280907"
---
# <a name="machine-learned-features"></a>Funzionalità acquisite dal computer 

In Machine Learning, una _funzionalità_ è un tratto di distinzione o un attributo di dati che il sistema osserva & apprende. In Language Understanding (LUIS), una funzionalità descrive e spiega cosa è significativo sugli Intent ed entità.

Nel [portale di anteprima Luis](https://preview.luis.ai)le funzionalità sono _descrittori_ perché vengono usate per _descrivere_ lo scopo o l'entità.  

## <a name="features-_descriptors_-in-language-understanding"></a>Funzionalità (_descrittori_) in Language Understanding

Le funzionalità, note anche come descrittori, descrivono gli indizi per aiutare Language Understanding identificare le espressioni di esempio. Funzionalità incluse: 

* Elenco di frasi come funzionalità per Intent o entità
* Entità come funzionalità per Intent o entità

Le funzionalità devono essere considerate come una parte necessaria dello schema per la scomposizione dei modelli. 

## <a name="what-is-a-phrase-list"></a>Che cos'è un elenco di frasi

Un elenco di frasi è un elenco di parole, frasi, numeri o altri caratteri che consentono di identificare il concetto che si sta tentando di identificare. L'elenco non fa distinzione tra maiuscole e minuscole. 

## <a name="when-to-use-a-phrase-list"></a>Quando usare un elenco di frasi

Con un elenco di frasi, LUIS considera il contesto e generalizza per identificare gli elementi simili a, ma non una corrispondenza esatta del testo. Se è necessario che l'app LUIS sia in grado di generalizzare e identificare nuovi elementi, usare un elenco di frasi. 

Quando si vuole essere in grado di riconoscere nuove istanze, ad esempio un'utilità di pianificazione della riunione che deve riconoscere i nomi dei nuovi contatti o un'app di inventario che dovrebbe riconoscere nuovi prodotti, iniziare con un'entità appresa dal computer. Creare quindi un elenco di frasi che aiuta LUIS a trovare parole con un significato simile. Questa frase elenca le guide di LUIS per riconoscere gli esempi aggiungendo altro significato al valore di tali parole. 

Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità. 

## <a name="considerations-when-using-a-phrase-list"></a>Considerazioni sull'utilizzo di un elenco di frasi

Per impostazione predefinita, viene applicato un elenco di frasi a tutti i modelli nell'app. Questa operazione funzionerà per gli elenchi di frasi che possono incrociare tutti gli Intent ed entità. Per la decomposizione, è necessario applicare un elenco di frasi solo ai modelli a cui è pertinente. 

Se si crea un elenco di frasi (creato globalmente per impostazione predefinita), successivamente lo si applica come descrittore (funzionalità) a un modello specifico, questo viene rimosso dagli altri modelli. Questa rimozione aggiunge la pertinenza all'elenco di frasi per il modello a cui viene applicato, migliorando l'accuratezza fornita nel modello. 

Il flag di `enabledForAllModels` controlla questo ambito del modello nell'API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Come usare un elenco di frasi

[Creare un elenco di frasi](luis-how-to-add-features.md) quando lo scopo o l'entità contiene parole o frasi importanti, ad esempio:

* termini di settore
* gergo
* abbreviazioni
* linguaggio specifico di un'azienda
* linguaggio derivato da un'altra lingua, ma usato spesso nell'app
* parole chiave o frasi nelle espressioni di esempio

**Non** aggiungere tutte le parole o frasi possibili. Aggiungere invece alcune parole o frasi alla volta, quindi ripetere il training e pubblicare. Man mano che l'elenco cresce nel tempo, è possibile che alcuni termini abbiano molti formati (sinonimi). Suddividerli in un altro elenco. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Quando usare un'entità come funzionalità 

Un'entità può essere aggiunta come funzionalità a livello di finalità o di entità. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entità come funzionalità per finalità

Aggiungere un'entità come descrittore (funzionalità) a uno scopo quando il rilevamento di tale entità è significativo per lo scopo.

Ad esempio, se lo scopo è quello di prenotare un volo e l'entità è costituita da informazioni sui ticket, ad esempio il numero di postazioni, di origine e di destinazione, la ricerca dell'entità di informazioni sui ticket dovrebbe aggiungere peso alla stima della finalità del volo del libro. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entità come funzionalità di un'altra entità

Un'entità (A) deve essere aggiunta come caratteristica a un'altra entità (B) quando il rilevamento di tale entità (A) è significativo per la stima dell'entità (B).

Se, ad esempio, viene rilevata l'entità via Address (A), la ricerca dell'indirizzo (a) aggiunge il peso alla stima per l'entità Address Shipping (B). 

## <a name="best-practices"></a>Procedure consigliate
Informazioni sulle [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.