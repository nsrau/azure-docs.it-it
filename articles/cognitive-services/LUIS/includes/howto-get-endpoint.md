---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544993"
---
Nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) della sezione **Manage** (Gestisci) (menu in alto a destra) copiare l'URL della **query di esempio** e incollarlo in una nuova scheda del browser.

L'URL dell'endpoint sarà simile al seguente, con il sottodominio, l'ID app e la chiave dell'endpoint personalizzati che sostituiscono APP-ID e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```