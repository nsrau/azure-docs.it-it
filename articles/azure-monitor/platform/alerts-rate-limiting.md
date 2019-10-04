---
title: Limitazione della frequenza per SMS, messaggi di posta elettronica, notifiche push dell'app Azure e webhook
description: Informazioni su come Azure limita il numero di possibili notifiche tramite SMS, posta elettronica, notifiche push dell'app Azure o webhook da un gruppo di azione.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 11fd6a2c58671cc5d0bcf0593239eb9e62aca834
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60346649"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitazione della frequenza per chiamata vocale, SMS, messaggi di posta elettronica, notifiche push dell'app Azure e post Webhook
La limitazione della frequenza è una sospensione delle notifiche che si verifica quando un numero eccessivo di notifiche viene inviato a un particolare numero telefonico, indirizzo di posta elettronica o dispositivo. La limitazione assicura che gli avvisi siano gestibili ed eseguibili.

I limiti della soglia di frequenza sono:

- **SMS**: non più di 1 ogni 5 minuti.
- **Chiamata vocale**: non più di 1 ogni 5 minuti.
- **Messaggio di posta elettronica**: non più di 100 messaggi di posta elettronica in un'ora.
 
  Per le altre azioni non ci sono limiti di frequenza.

## <a name="rate-limit-rules"></a>Regole dei limiti di frequenza
- Vengono messi i limiti di frequenza per uno specifico numero di telefono o un indirizzo di posta elettronica quando si ricevono più avvisi rispetto alla soglia di frequenza.
- Un numero di telefono o un indirizzo di posta elettronica può fare parte di gruppi di azione tra più sottoscrizioni. La limitazione della frequenza viene applicata a tutte le sottoscrizioni. Al raggiungimento della soglia, si applica anche se i messaggi vengono inviati da più sottoscrizioni.
- Quando un indirizzo di posta elettronica ha una limitazione della frequenza, viene inviata una notifica aggiuntiva per comunicare tale limitazione. Il messaggio di posta elettronica indica la scadenza della limitazione della frequenza.

## <a name="next-steps"></a>Passaggi successivi ##
* Altre informazioni sul [Comportamento degli avvisi SMS](alerts-sms-behavior.md).
* Leggere una [panoramica degli avvisi del log attività](alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

