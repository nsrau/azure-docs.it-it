---
title: Limitazione della frequenza per SMS, messaggi di posta elettronica, notifiche push dell'app Azure e Webhook | Microsoft Docs
description: Informazioni su come Azure limita il numero di possibili notifiche tramite SMS, posta elettronica, notifiche push dell'app Azure o webhook da un gruppo di azione.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/12/2018
ms.author: dukek
ms.openlocfilehash: 9216a64dbd8201ff09ea5c9283b4db465682a3bd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
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
* Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
