---
title: Limitazione della frequenza per SMS, messaggi di posta elettronica e Webhook | Microsoft Docs
description: "Ricevere una notifica SMS, webhook e posta elettronica quando si verificano determinati eventi nel log attività."
author: anirudhcavale
manager: carmonm
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
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 93f8d0f32a851d530df84186d419bd8e2d47f1bc
ms.lasthandoff: 03/31/2017


---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>Limitazione della frequenza per SMS, messaggi di posta elettronica e Webhook
La limitazione della frequenza è una sospensione delle notifiche che si verifica quando un numero eccessivo di notifiche viene inviato a un particolare numero telefono o posta elettronica. La limitazione assicura che le comunicazioni relative agli avvisi del log attività e agli avvisi di integrità del servizio siano gestibili ed eseguibili

Le regole per SMS e messaggi di posta elettronica sono uguali. Il limite della soglia di frequenza per
 - SMS - 10 messaggi in un'ora
 - Messaggi di posta elettronica - 100 messaggi in un'ora

## <a name="rate-limit-rules"></a>Regole dei limiti di frequenza
- Vengono messi i limiti di frequenza per uno specifico numero di telefono o un indirizzo di posta elettronica quando riceve più avvisi rispetto alla soglia di frequenza
- Un numero di telefono o un indirizzo di posta elettronica può fare parte di gruppi di azione tra più sottoscrizioni. La limitazione della frequenza viene applicata a tutte le sottoscrizioni, vale a dire, viene applicata non appena viene raggiunta la soglia anche se si riceve da più sottoscrizioni.  
- Quando un numero di telefono o un indirizzo di posta elettronica ha una limitazione della frequenza, per comunicare tale limitazione viene inviato un altro messaggio dello stesso tipo. Il messaggio di posta elettronica o l'SMS indica la scadenza della limitazione della frequenza.

## <a name="rate-limit-of-webhooks"></a>Limitazione della frequenza di webhook ##
Non esiste al momento alcuna limitazione della frequenza per webhook.

## <a name="next-steps"></a>Passaggi successivi ##
Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md)  
Ottenere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni sulla ricezione degli avvisi  
Come [configurare gli avvisi ogni volta che viene inviata una notifica di integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md)

