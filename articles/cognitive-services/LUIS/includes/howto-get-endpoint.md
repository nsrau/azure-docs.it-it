---
title: includere file
description: includere file
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959033"
---
Nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) della sezione **Manage** (Gestisci) (menu in alto a destra) copiare l'URL della **query di esempio** e incollarlo in una nuova scheda del browser.

L'URL dell'endpoint sarà simile al seguente, con il sottodominio, l'ID app e la chiave dell'endpoint personalizzati che sostituiscono APP-ID e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```