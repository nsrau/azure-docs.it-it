---
title: Frequenza di limitazione per SMS, messaggi di posta elettronica, le notifiche push dell'App di Azure e ai webhook | Documenti Microsoft
description: Comprendere in che modo Azure limita il numero di possibili SMS, posta elettronica, le notifiche push o webhook App di Azure da un gruppo di azioni.
author: dukek
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
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Frequenza di limitazione per i messaggi SMS, messaggi di posta elettronica, le notifiche push dell'App di Azure e webhook post
La limitazione della velocità è una sospensione di notifiche che si verifica quando un numero eccessivo le notifiche vengono inviate a un dispositivo o indirizzo di posta elettronica, numero di telefono specifico. La limitazione assicura che gli avvisi siano gestibili ed eseguibili.

Le soglie di limite di velocità sono:

 - **SMS**: SMS non più di 1 ogni 5 minuti.
 - **Messaggi di posta elettronica**: 100 messaggi in un'ora.
 - **Le notifiche Push dell'App Azure**: non è presente alcuna limitazione per le notifiche push della frequenza.
 - **Webhook**: non è presente alcuna limitazione per webhook della frequenza.

## <a name="rate-limit-rules"></a>Regole dei limiti di frequenza
- Vengono messi i limiti di frequenza per uno specifico numero di telefono o un indirizzo di posta elettronica quando si ricevono più avvisi rispetto alla soglia di frequenza.
- Un numero di telefono o un indirizzo di posta elettronica può fare parte di gruppi di azione tra più sottoscrizioni. La limitazione della frequenza viene applicata a tutte le sottoscrizioni. Al raggiungimento della soglia, si applica anche se i messaggi vengono inviati da più sottoscrizioni.  
- Quando un indirizzo di posta elettronica è limitato a frequenza, verrà inviata una notifica aggiuntiva per comunicare la limitazione della velocità. La notifica indica la scadenza della limitazione della frequenza.

## <a name="next-steps"></a>Passaggi successivi ##
* Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
