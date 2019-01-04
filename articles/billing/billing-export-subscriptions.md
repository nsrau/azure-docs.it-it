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
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.openlocfilehash: 09231ab69276f3b4763f07c51230921d15333f63
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339434"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Esportare e visualizzare le informazioni della sottoscrizione di livello superiore
Se è necessario visualizzare il set di ID sottoscrizione associato alle credenziali utente, [scaricare il file con estensione json con le informazioni della sottoscrizione dal Centro account di Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Il file con estensione json scaricato offre le informazioni seguenti:
- Posta elettronica: indirizzo di posta elettronica associato all'account.
- Puid: identificatore univoco associato all'account di fatturazione.
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
