---
title: Esportare le informazioni di livello superiore della sottoscrizione di Azure | Microsoft Docs
description: Descrive come visualizzare tutti gli ID sottoscrizione di Azure associati all'account.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 553cc7fd27571ebc925e33f824060c023664a369
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991776"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Esportare e visualizzare le informazioni della sottoscrizione di livello superiore
Se è necessario visualizzare il set di ID sottoscrizione associato alle credenziali utente, [scaricare il file con estensione json con le informazioni della sottoscrizione dal Centro account di Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Il file con estensione json scaricato offre le informazioni seguenti:
- Indirizzo di posta elettronica: indirizzo di posta elettronica associato all'account.
- PUID: identificatore univoco associato all'account di fatturazione.
- ID sottoscrizione: elenco di sottoscrizioni appartenenti all'account, enumerate in base all'ID sottoscrizione.

### <a name="subscriptionsjson-sample"></a>esempio di subscriptions.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
