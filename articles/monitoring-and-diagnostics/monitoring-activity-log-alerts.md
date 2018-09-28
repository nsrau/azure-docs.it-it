---
title: Avvisi del log attività nel Monitoraggio di Azure
description: Ricevere una notifica tramite SMS, webhook, posta elettronica e altro quando si verificano determinati eventi nel log attività.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5ddf510d50f38ed9aaf742bd06c330e53ffe1391
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992797"
---
# <a name="alerts-on-activity-log"></a>Avvisi sul log attività 

## <a name="overview"></a>Panoramica
Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso. Si tratta di risorse di Azure e possono essere create usando un modello di Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In questo articolo vengono presentati i concetti alla base degli avvisi del log attività. Questo articolo illustra come usare il portale di Azure per configurare un avviso sugli eventi del log attività. Per altre informazioni sull'utilizzo, vedere [Creare e gestire gli avvisi del log attività](alert-activity-log.md).

> [!NOTE]
> Gli avvisi **non possono** essere creati per gli eventi nella categoria Avviso del log attività

In genere, si creano avvisi del log attività per ricevere notifiche quando:

* Vengono effettuate operazioni specifiche nelle risorse nella sottoscrizione di Azure. Questi avvisi sono spesso limitati a risorse o gruppi di risorse specifici. Potrebbe ad esempio essere utile ricevere una notifica quando viene eliminata una macchina virtuale in myProductionResourceGroup o se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.
* Si verifica un evento di integrità del servizio. Gli eventi di integrità del servizio includono la notifica di eventi imprevisti e di manutenzione che si applicano alle risorse nella sottoscrizione.

Una semplice analogia per comprendere le condizioni in cui è possibile creare le regole di avviso nel log attività è quella di esplorare o filtrare gli eventi tramite il [Log attività nel portale di Azure](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). In Monitoraggio di Azure - Log attività, un utente può filtrare o trovare l'evento necessario e quindi creare un avviso usando il pulsante **Aggiungi avviso del log attività**.

In ogni caso, un avviso del log attività monitora solo degli eventi nella sottoscrizione in cui è stato creato l'evento.

È possibile configurare un avviso del log attività in base a qualsiasi proprietà di primo livello nell'oggetto JSON per un evento del log attività. Per altre informazioni, vedere [Panoramica del log attività di Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Per altre informazioni sugli eventi di integrità del servizio, vedere [Ricevere gli avvisi del log attività sulle notifiche del servizio](./monitoring-activity-log-alerts-on-service-notifications.md). 

Gli avvisi del log attività hanno alcune opzioni comuni:

- **Categoria**: amministrazione, integrità dei servizi, scalabilità automatica, protezione, criteri e indicazione. 
- **Ambito**: la singola risorsa o il set di risorse per cui è definito l'avviso nel log attività. È possibile definire l'ambito per un avviso del log attività a vari livelli:
    - Livello di risorse: ad esempio, per una macchina virtuale specifica
    - Livello di gruppo di risorse: ad esempio, tutte le macchine virtuali in un gruppo di risorse specifico
    - Livello di sottoscrizione: ad esempio, tutte le macchine virtuali in una sottoscrizione oppure tutte le risorse in una sottoscrizione
- **Gruppo di risorse**: per impostazione predefinita, la regola di avviso viene salvata nello stesso gruppo di risorse di quello di destinazione definito nell'ambito. L'utente può anche definire il gruppo di risorse in cui deve essere archiviata la regola di avviso.
- **Tipo di risorsa**: spazio dei nomi definito da Resource Manager per la destinazione dell'avviso.

- **Nome operazione**: il nome dell'operazione del controllo degli accessi in base al ruolo di Resource Manager.
- **Livello**: il livello di gravità dell'evento (dettagliato, informativo, avvertenza, errore o critico).
- **Stato**: lo stato dell'evento, in genere Avviato, Non riuscito o Riuscito.
- **Evento avviato da**: noto anche come "chiamante". L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

> [!NOTE]
> In una sottoscrizione fino a 100 regole di avviso per l'attività dell'ambito per: una sola risorsa, tutte le risorse nel gruppo di risorse oppure a livello dell'intera sottoscrizione.

Quando un avviso del log di attività viene attivato, usa un gruppo di azione per generare azioni o notifiche. Un gruppo di azione è un set riutilizzabile di ricevitori di notifica, ad esempio gli indirizzi di posta elettronica, gli URL webhook o i numeri di telefono di SMS. Più avvisi possono fare riferimento ai ricevitori per centralizzare e raggruppare i canali di notifica. Quando si definisce l'avviso del log di attività, sono disponibili due opzioni. È possibile:

* Usare un gruppo di azione esistente nell'avviso del log attività.
* Creare un nuovo gruppo di azione.

Per altre informazioni sui gruppi di azione, vedere [Creare e gestire gruppi di azione nel portale di Azure](monitoring-action-groups.md).


## <a name="next-steps"></a>Passaggi successivi
- Ottenere una [panoramica degli avvisi](monitoring-overview-alerts.md).
- Altre informazioni sulla [creazione e modifica degli avvisi del log attività](alert-activity-log.md).
- Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).
- Informazioni sulle [notifiche per l'integrità del servizio](monitoring-service-notifications.md).

