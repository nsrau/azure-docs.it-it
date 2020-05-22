---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8aef4a5eeeec27bfb2ca3edf9ff6823b88431319
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591837"
---
L'entità pattern.any consente di trovare i dati in formato libero nel caso in cui la formulazione dell'entità renda difficile determinare la fine dell'entità dal resto dell'espressione.

Un'app di gestione delle risorse umane consente ai dipendenti di trovare i moduli aziendali.

|Espressione|
|--|
|Dove si trova **HRF-123456**?|
|Chi ha creato **HRF-123234**?|
|**HRF-456098** viene pubblicato in francese?|

Tuttavia, ogni modulo contiene sia un nome formattato, usato nella tabella precedente, nonché un nome descrittivo, ad esempio `Request relocation from employee new to the company 2018 version 5`.

Le espressioni con il nome descrittivo hanno l'aspetto seguente:

|Espressione|
|--|
|Dove si trova la **Richiesta di trasferimento del nuovo dipendente dell'azienda 2018 versione 5**?|
|Chi ha creato la **"Richiesta di trasferimento del nuovo dipendente dell'azienda 2018 versione 5"** ?|
|La **Richiesta di trasferimento del nuovo dipendente dell'azienda 2018 versione 5** viene pubblicata in francese?|

La lunghezza variabile include parole che potrebbero confondere LUIS circa la fine dell'entità. Usando un'entità Pattern.any in un criterio è possibile specificare l'inizio e la fine del nome del modulo in modo che LUIS lo estragga correttamente.

|Espressione modello di esempio|
|--|
|Dov'è {NomeModulo}[?]|
|Chi ha creato {NomeModulo}[?]|
|{NomeModulo} viene pubblicato in francese[?]|

Vedere le informazioni di [riferimento](../reference-entity-pattern-any.md) su Pattern.any