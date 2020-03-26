---
title: Esportare le informazioni generali della sottoscrizione di Azure
description: Descrive come visualizzare tutti gli ID sottoscrizione di Azure associati all'account.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 87135b309f0406528bcada1cd906dd2f8535d1ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202897"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Esportare e visualizzare le informazioni della sottoscrizione di livello superiore
Se è necessario visualizzare il set di ID sottoscrizione associato alle credenziali utente, [scaricare il file con estensione json con le informazioni della sottoscrizione dal Centro account di Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

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
