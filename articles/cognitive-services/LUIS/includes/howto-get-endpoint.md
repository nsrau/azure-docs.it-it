---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279477"
---
Nella sezione **Manage** (Gestisci) del menu in alto a destra, nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) copiare l'URL **Example Query** (Query di esempio), quindi incollarlo in una nuova scheda del browser.

L'URL dell'endpoint avrà un aspetto simile al seguente, con l'ID app e la chiave dell'endpoint che sostituiscono APP-ID e KEY-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```