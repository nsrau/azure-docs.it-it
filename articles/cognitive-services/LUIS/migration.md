---
title: Panoramica della migrazione-LUIS
description: Informazioni sul percorso di migrazione
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: d6ecacf9aa1a7e650de74a412ed4f161ed0e0790
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253803"
---
# <a name="migration-in-luis"></a>Migrazione in LUIS

Molti elementi si trovano in un percorso di migrazione. Usare la tabella seguente per comprendere le conseguenze e quando è necessario completare la migrazione.

|Area|Descrizione|Data di completamento della migrazione|
|--|--|--|
|[API di stima](luis-migration-api-v3.md)|Eseguire la migrazione all'API V3.|TBD|
|[API di creazione](luis-migration-authoring-entities.md)|Eseguire la migrazione all'API V3.|TBD|
|[Creazione di una risorsa](luis-migration-authoring.md)|Eseguire la migrazione da nessuna risorsa di creazione all'uso di una risorsa LUIS authoring nel portale LUIS.|2 novembre 2020 |
|[Funzionalità obbligatoria](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|Questa modifica è stata apportata nel maggio 2020 della conferenza Build e si applica solo alle API di creazione di V3 in cui un'app usa una funzionalità vincolata.|19 giugno 2020|
|[Entità composita](migrate-from-composite-entity.md)|Aggiornare l'entità composita all'entità Machine Learning per compilare un'entità che riceve stime più complete con una migliore decomposizione per il debug dell'entità.|TBD|
