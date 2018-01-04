---
title: Comportamento degli avvisi SMS nei gruppi di azione | Microsoft Docs
description: Formato di messaggio SMS e risposta a messaggi SMS per l'annullamento e la riesecuzione della sottoscrizione o la richiesta di assistenza.
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
ms.openlocfilehash: 012f001356463a8a7d9b95f186111959627f2c28
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento degli avvisi SMS nei gruppi di azione
## <a name="overview"></a>Panoramica ##
I gruppi di azione consentono di configurare un elenco di ricevitori. Questi gruppi possono essere usati in seguito durante la definizione degli avvisi del log attività per garantire che un determinato gruppo di azione riceva una notifica quando viene generato l'avviso del log attività. Uno dei metodi di avviso supportati è costituito dagli SMS. Gli avvisi supportano la comunicazione bidirezionale. Un utente può rispondere a un avviso per:

- **Annullare la sottoscrizione agli avvisi:** un utente può annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azione o per un singolo gruppo di azioni.  
- **Rieseguire la sottoscrizione agli avvisi:** un utente può rieseguire la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azione o per un singolo gruppo di azioni.  
- **Richiedere assistenza:** un utente può richiedere altre informazioni nell'SMS. Viene reindirizzato a questo articolo

Questo articolo descrive il comportamento degli avvisi SMS e le azioni di risposta che l'utente può eseguire in base alle impostazioni locali:

## <a name="usacanada-sms-behavior"></a>Comportamento SMS per Stati Uniti e Canada
### <a name="receiving-an-sms-alert"></a>Ricezione di un avviso SMS
Un ricevitore di SMS, configurato come parte di un gruppo di azioni, riceverà un SMS quando viene generato un avviso. Il messaggio SMS include le informazioni seguenti:
* Nome breve del gruppo di azione a cui è stato inviato l'avviso
- Titolo dell'avviso

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>Annullamento della sottoscrizione agli avvisi SMS per un gruppo di azioni
Un utente può annullare la sottoscrizione di SMS per gli avvisi per gruppo di un'azione rispondendo al codice breve 29873 con le parole chiave: "DISABILITARE &lt;Shortname del gruppo di azioni&gt;".

Esempio: Un utente che desidera annullare la sottoscrizione di avvisi per un gruppo di azioni con shortname "Azure", viene inviato un SMS al codice breve 29873 che afferma "DISABILITARE Azure"

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Annullamento della sottoscrizione agli avvisi SMS per tutti i gruppi di azioni
Un utente può annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni rispondendo al codice breve 29873 con una delle parole chiave seguenti:
* STOP

Esempio: Un utente che desidera annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni, viene inviato un SMS al codice breve 29873 che afferma "STOP"

>[!NOTE]
>Se un utente ha annullato la sottoscrizione agli avvisi SMS e viene in seguito aggiunto a un nuovo gruppo di azioni, l'utente riceve gli avvisi SMS del nuovo gruppo di azioni mantenendo l'annullamento della sottoscrizione a tutti i gruppi di azioni precedenti.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>Riesecuzione della sottoscrizione agli avvisi SMS per un gruppo di azioni
Un utente può rinnovare la partecipazione al SMS per gli avvisi per gruppo di un'azione in risposta al codice breve 29873 con le parole chiave: "abilitare &lt;Shortname del gruppo di azioni&gt;".

Esempio: Un utente che desidera ripetuta agli avvisi per un gruppo di azioni con shortname "Azure", viene inviato un SMS al codice breve 29873 che afferma "Abilitare Azure"

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Riesecuzione della sottoscrizione agli avvisi SMS per tutti i gruppi di azioni
Un utente può ripetuta per tutti i SMS per gli avvisi per tutti i gruppi di azioni rispondendo al codice breve 29873 con una delle parole chiave seguenti:

* START

Esempio: Un utente che desidera annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni, viene inviato un SMS al codice breve 29873 che afferma "START"

### <a name="requesting-help-via-sms"></a>Richiesta di assistenza tramite SMS
Un utente può chiedere per ulteriori informazioni su SMS, avere ricevuto in risposta al codice breve 29873 con una delle parole chiave seguenti:
* HELP

Verrà inviata una risposta all'utente con un collegamento a questo articolo.

## <a name="next-steps"></a>Fasi successive
Ottenere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni sulla ricezione degli avvisi  
Altre informazioni sulla [limitazione della frequenza degli SMS](monitoring-alerts-rate-limiting.md)  
Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md)
