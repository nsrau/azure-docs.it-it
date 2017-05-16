---
title: Comportamento degli avvisi SMS nei gruppi di azione | Microsoft Docs
description: Formato di messaggio SMS e risposta a messaggi SMS per l&quot;annullamento e la riesecuzione della sottoscrizione o la richiesta di assistenza.
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
ms.openlocfilehash: 10f33898fb86bd2449994a153d99cb59dc6078d6
ms.lasthandoff: 03/31/2017


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
Un utente può annullare la sottoscrizione agli SMS per gli avvisi di un solo gruppo di azioni rispondendo al codice breve 20873 con le parole chiave: "DISABLE &lt;nome breve del gruppo di azioni&gt;".

Esempio: Un utente che vuole annullare la sottoscrizione agli avvisi di un gruppo di azioni con nome breve "Azure" invia un SMS al codice breve 20873 con il testo "DISABLE Azure"

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Annullamento della sottoscrizione agli avvisi SMS per tutti i gruppi di azioni
Un utente può annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni rispondendo al codice breve 20873 con la parola chiave seguente:
* STOP

Esempio: Un utente che desidera annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni, potrebbe inviare un SMS non 20873 con il testo "STOP"

>[!NOTE]
>Se un utente ha annullato la sottoscrizione agli avvisi SMS e viene in seguito aggiunto a un nuovo gruppo di azioni, l'utente riceve gli avvisi SMS del nuovo gruppo di azioni mantenendo l'annullamento della sottoscrizione a tutti i gruppi di azioni precedenti.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>Riesecuzione della sottoscrizione agli avvisi SMS per un gruppo di azioni
Un utente può rieseguire la sottoscrizione agli SMS per gli avvisi di un solo gruppo di azioni rispondendo al codice breve 20873 con le parole chiave: "ENABLE &lt;nome breve del gruppo di azioni&gt;".

Esempio: Un utente che vuole rieseguire la sottoscrizione agli avvisi di un gruppo di azioni con nome breve "Azure" invia un SMS al codice breve 20873 con il testo "ENABLE Azure"

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Riesecuzione della sottoscrizione agli avvisi SMS per tutti i gruppi di azioni
Un utente può rieseguire la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni rispondendo al codice breve 20873 con la parola chiave seguente:

* START

Esempio: Un utente che vuole annullare la sottoscrizione a tutti gli avvisi SMS per tutti i gruppi di azioni invia un SMS al codice breve 20873 con il testo "START"

### <a name="requesting-help-via-sms"></a>Richiesta di assistenza tramite SMS
Un utente può richiedere altre informazioni sul messaggio SMS ricevuto rispondendo al codice breve 20873 con la parola chiave seguente:
* HELP

Verrà inviata una risposta all'utente con un collegamento a questo articolo.

## <a name="next-steps"></a>Passaggi successivi
Ottenere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni sulla ricezione degli avvisi  
Altre informazioni sulla [limitazione della frequenza degli SMS](monitoring-alerts-rate-limiting.md)  
Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md)

