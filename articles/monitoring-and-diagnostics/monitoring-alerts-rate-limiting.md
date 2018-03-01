---
title: Limitazione della frequenza per SMS, messaggi di posta elettronica, notifiche push dell'app Azure e Webhook | Microsoft Docs
description: Informazioni su come Azure limita il numero di possibili notifiche tramite SMS, posta elettronica, notifiche push dell'app Azure o webhook da un gruppo di azione.
author: dkamstra
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/16/2018
ms.author: dukek
ms.openlocfilehash: a4f97cc79945d266edd0af577e37fc9da2aa97bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitazione della frequenza per SMS, messaggi di posta elettronica, notifiche push dell'app Azure e post Webhook
La limitazione della frequenza è una sospensione delle notifiche che si verifica quando un numero eccessivo di notifiche viene inviato a un particolare numero telefonico, indirizzo di posta elettronica o dispositivo. La limitazione assicura che gli avvisi siano gestibili ed eseguibili.

I limiti della soglia di frequenza sono:

 - **SMS**: non più di 1 ogni 5 minuti.
 - **Messaggio di posta elettronica**: non più di 100 messaggi di posta elettronica in un'ora.
 
 Per le altre azioni non ci sono limiti di frequenza.

## <a name="rate-limit-rules"></a>Regole dei limiti di frequenza
- Vengono messi i limiti di frequenza per uno specifico numero di telefono o un indirizzo di posta elettronica quando si ricevono più avvisi rispetto alla soglia di frequenza.
- Un numero di telefono o un indirizzo di posta elettronica può fare parte di gruppi di azione tra più sottoscrizioni. La limitazione della frequenza viene applicata a tutte le sottoscrizioni. Al raggiungimento della soglia, si applica anche se i messaggi vengono inviati da più sottoscrizioni.
- Quando un indirizzo di posta elettronica ha una limitazione della frequenza, viene inviata una notifica aggiuntiva per comunicare tale limitazione. La notifica indica la scadenza della limitazione della frequenza.

## <a name="next-steps"></a>Passaggi successivi ##
* Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
