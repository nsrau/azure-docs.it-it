---
title: includere file
description: includere file
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "87374926"
---
Nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) della sezione **Manage** (Gestisci) (menu in alto a destra) copiare l'URL della **query di esempio** e incollarlo in una nuova scheda del browser.

L'URL dell'endpoint sarà simile al seguente, con il sottodominio, l'ID app e la chiave dell'endpoint personalizzati che sostituiscono APP-ID e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```