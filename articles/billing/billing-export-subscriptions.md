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
ms.openlocfilehash: 5c32b90c8a291ff744b4894af12f8d623cb95137
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391405"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Esportare e visualizzare le informazioni della sottoscrizione di livello superiore
Se è necessario visualizzare il set di ID sottoscrizione associato alle credenziali utente, [scaricare il file con estensione json con le informazioni della sottoscrizione dal Centro account di Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Il file con estensione json scaricato offre le informazioni seguenti:
- Indirizzo di posta elettronica: indirizzo di posta elettronica associato all'account.
- PUID: identificatore univoco associato all'account di fatturazione.
- ID sottoscrizione: elenco di sottoscrizioni appartenenti all'account, enumerate in base all'ID sottoscrizione.

### <a name="subscriptionsjson-sample"></a>esempio di subscriptions.json
~~~~
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
~~~~
