---
title: Comportamento degli avvisi SMS nei gruppi di azioni
description: Formato di messaggio SMS e risposta a messaggi SMS per l'annullamento e la riesecuzione della sottoscrizione o la richiesta di assistenza.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 225c86ee1a7f764f60b2da0b8e3be02aa5dd22e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123301"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento degli avvisi SMS nei gruppi di azione
## <a name="overview"></a>Panoramica ##
I gruppi di azioni consentono di configurare un elenco di azioni. Questi gruppi si usano quando si definiscono avvisi, per assicurarsi che un determinato gruppo di azioni riceva una notifica quando viene generato l'avviso. Una delle azioni supportate è costituita dagli SMS; le notifiche via SMS supportano la comunicazione bidirezionale. Un utente può rispondere a un SMS per:

- **Annullare la sottoscrizione agli avvisi:** un utente può annullare la sottoscrizione a tutti gli avvisi via SMS per tutti i gruppi di azioni o per un singolo gruppo di azioni.
- **Ripetere la sottoscrizione agli avvisi:** un utente può ripetere la sottoscrizione a tutti gli avvisi via SMS per tutti i gruppi di azioni o per un singolo gruppo di azioni.  
- **Richiedere assistenza:** un utente può richiedere altre informazioni sull'SMS. Viene reindirizzato a questo articolo.

Questo articolo descrive il comportamento degli avvisi SMS e le azioni di risposta che l'utente può eseguire in base alle impostazioni locali:

## <a name="receiving-an-sms-alert"></a>Ricezione di un avviso SMS
Un ricevitore di SMS configurato come parte di un gruppo di azioni riceverà un SMS quando viene generato un avviso. L'SMS Contiene le informazioni seguenti:
* Nome breve del gruppo di azione a cui è stato inviato l'avviso
* Titolo dell'avviso

| REPLY | DESCRIZIONE |
| ----- | ----------- |
| DISABLE <Action Group Short name> | Disabilita gli SMS dal gruppo di azioni |
| ENABLE <Action Group Short name> | Abilita di nuovo gli SMS dal gruppo di azioni |
| STOP | Disabilita gli SMS da tutti i gruppi di azioni |
| START | Abilita di nuovo gli SMS da tutti i gruppi di azioni |
| HELP | Viene inviata una risposta all'utente con un collegamento a questo articolo. |

>[!NOTE]
>Se un utente ha annullato la sottoscrizione agli avvisi SMS e viene in seguito aggiunto a un nuovo gruppo di azioni, l'utente riceve gli avvisi SMS del nuovo gruppo di azioni mantenendo l'annullamento della sottoscrizione a tutti i gruppi di azioni precedenti.

## <a name="next-steps"></a>Fasi successive
Ottenere una [panoramica degli avvisi del log attività](alerts-overview.md) e informazioni sulla ricezione degli avvisi  
Altre informazioni sulla [limitazione della frequenza degli SMS](alerts-rate-limiting.md)  
Altre informazioni sui [gruppi di azioni](../../azure-monitor/platform/action-groups.md)

