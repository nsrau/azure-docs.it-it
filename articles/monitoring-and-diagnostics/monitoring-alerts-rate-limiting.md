---
title: Limitazione della frequenza per SMS, messaggi di posta elettronica e Webhook | Microsoft Docs
description: Informazioni su come Azure limita il numero di possibili notifiche tramite SMS, posta elettronica o webhook da un gruppo di azione.
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Limitazione della frequenza per i messaggi SMS e di posta elettronica e per i post webhook
La limitazione della frequenza è una sospensione delle notifiche che si verifica quando un numero eccessivo di notifiche viene inviato a un particolare numero telefono o indirizzo di posta elettronica. La limitazione assicura che gli avvisi siano gestibili ed eseguibili.

Le regole per SMS e messaggi di posta elettronica sono uguali. Il limite della soglia di frequenza è:

 - **SMS**: 10 messaggi in un'ora.
 - **Messaggi di posta elettronica**: 100 messaggi in un'ora.

## <a name="rate-limit-rules"></a>Regole dei limiti di frequenza
- Vengono messi i limiti di frequenza per uno specifico numero di telefono o un indirizzo di posta elettronica quando si ricevono più avvisi rispetto alla soglia di frequenza.
- Un numero di telefono o un indirizzo di posta elettronica può fare parte di gruppi di azione tra più sottoscrizioni. La limitazione della frequenza viene applicata a tutte le sottoscrizioni. Al raggiungimento della soglia, si applica anche se i messaggi vengono inviati da più sottoscrizioni.  
- Quando un numero di telefono o un messaggio di posta elettronica ha una limitazione della frequenza, viene inviata una notifica aggiuntiva per comunicare tale limitazione. La notifica indica la scadenza della limitazione della frequenza.

## <a name="rate-limit-of-webhooks"></a>Limitazione della frequenza di webhook ##
Non esiste alcuna limitazione della frequenza per webhook.

## <a name="next-steps"></a>Passaggi successivi ##
* Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
