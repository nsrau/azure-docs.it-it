---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648066"
---
Nella sezione **Manage** (Gestisci) del menu in alto a destra, nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) copiare l'URL **Example Query** (Query di esempio), quindi incollarlo in una nuova scheda del browser. 

L'URL dell'endpoint avrà un aspetto simile al seguente, con l'ID app e la chiave dell'endpoint che sostituiscono APP-ID e KEY-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```