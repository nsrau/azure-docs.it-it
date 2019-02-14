---
title: Struttura di denominazione degli analizzatori - API Analisi linguistica
titlesuffix: Azure Cognitive Services
description: Informazioni sul modo in cui l'analizzatore dell'API Analisi linguistica usa la struttura di denominazione per consentire flessibilità e precisione.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: b2e956b099022751712f96ab0b15aa57c752b458
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873377"
---
# <a name="analyzer-names"></a>Nomi degli analizzatori

> [!IMPORTANT]
> L'anteprima di Analisi linguistica è stata ritirata il 9 agosto 2018. Per l'elaborazione e l'analisi del testo è consigliabile usare i [moduli di analisi del testo di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

Viene usata una struttura di denominazione piuttosto complicata per gli analizzatori per consentire sia la flessibilità per gli analizzatori che la precisione per comprendere il significato di un nome.
I nomi di analizzatori sono costituiti da quattro parti: un ID, un tipo, una specifica e un'implementazione.
Il ruolo di ogni componente è definito di seguito.

## <a name="id"></a>ID
Innanzitutto, un analizzatore ha un ID univoco, ovvero un GUID.
Questi GUID cambiano relativamente raramente, ma sono l'unico modo per descrivere in modo univoco un analizzatore particolare.

## <a name="kind"></a>Tipo
Ogni analizzatore è poi un **tipo**.
Questa caratteristica definisce a grandi linee il tipo di analisi restituita e dovrebbe definire in modo univoco la struttura dei dati usata per rappresentare tale analisi.
Attualmente, sono disponibili tre tipi distinti:
 - [Token](Sentences-and-Tokens.md)
 - [Tag delle parti del discorso (POS)](Pos-Tagging.md)
 - [Albero di analisi sintattica](constituency-parsing.md)

## <a name="specification"></a>Specifiche
All'interno di un determinato tipo, tuttavia, diversi esperti potrebbero non essere concordi sul modo in cui deve essere analizzato un particolare fenomeno.
A differenza dei linguaggi di programmazione, non esiste una definizione chiara ed esatta di come devono essere eseguite queste operazioni.

Ad esempio, si supponga di voler trovare i token nella frase inglese "He didn't go."
In particolare, considerare la stringa "didn't".
Una possibile interpretazione è che questa stringa deve essere suddivisa in due token: "did" e "not".
La frase alternativa "He did not go" includerebbe quindi lo stesso set di token.
Un'altra possibilità è affermare che la stringa dovrebbe essere suddivisa nei token "did" e "n't".
L'ultimo token non verrebbe normalmente considerato una parola, ma questo approccio conserva più informazioni sulla stringa di superficie e ciò può talvolta essere utile.
O forse la contrazione dovrebbe essere considerata una singola parola.

Indipendentemente dalla scelta effettuata, è necessario che sia coerente.
Questo è esattamente lo scopo di una **specifica**: stabilire quale deve essere una rappresentazione corretta.

Gli output degli analizzatori possono essere solo paragonati a dati conformi alla stessa specifica.

## <a name="implementation"></a>Implementazione

Spesso esistono più modelli che tentano di ottenere gli stessi risultati, ma con caratteristiche di prestazioni diverse.
Un modello potrebbe essere più veloce ma meno accurato. Un altro potrebbe offrire caratteristiche bilanciate diversamente.

La parte dell'**implementazione** del nome di un analizzatore viene usata per identificare questo tipo di informazioni, in modo che gli utenti possano selezionare l'analizzatore più appropriato per le specifiche esigenze.
