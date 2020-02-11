---
title: Gestione della versione dei modelli
titleSuffix: Azure Cognitive Services
description: Specificare le versioni del modello negli endpoint V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089071"
---
La versione 3 dell'API Analisi del testo consente di scegliere la versione del modello più aggiornata per i dati. Usare il parametro `model-version` facoltativo per selezionare la versione del modello appropriata per le richieste. Se questo parametro non viene specificato, per impostazione predefinita l'API verrà impostata su `latest`, ovvero la versione stabile più recente. Anche se è possibile usare la versione più recente del modello in qualsiasi richiesta, in ogni versione vengono aggiornate solo alcune funzionalità. La tabella seguente descrive le funzionalità che sono state aggiornate in ogni versione del modello:

| Versione del modello           | Funzionalità aggiornate         | Ultima versione per:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Riconoscimento delle entità                      | Riconoscimento delle entità                      |
| `2019-10-01`            | Riconoscimento delle entità, analisi del sentiment  | Rilevamento della lingua, estrazione di frasi chiave, analisi del sentiment|


Ogni risposta dagli endpoint v3 include un campo `model-version` che specifica la versione del modello utilizzata.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Per informazioni sugli aggiornamenti di queste versioni del modello, vedere [Novità](../whats-new.md).
