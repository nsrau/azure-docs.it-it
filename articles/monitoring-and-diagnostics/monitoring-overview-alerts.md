---
title: Panoramica degli avvisi classici in Microsoft Azure e Monitoraggio di Azure | Microsoft Docs
description: Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi classici in Microsoft Azure?

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [nuovi avvisi delle metriche near real time](monitoring-overview-unified-alerts.md)
>

Questo articolo descrive le varie origini di avvisi in Microsoft Azure, lo scopo di tali avvisi, i vantaggi che offrono e un'introduzione sul loro utilizzo. È incentrato in modo particolare sugli avvisi classici di Monitoraggio di Azure. Gli avvisi rappresentano un metodo per eseguire il monitoraggio in Azure: è possibile configurare condizioni relative ai dati e ricevere una notifica qualora tali condizioni corrispondano ai dati di monitoraggio più recenti.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Tassonomia degli avvisi classici di Monitoraggio di Azure
Azure usa i termini seguenti per descrivere gli avvisi classici e le relative funzioni:
* **Avviso**: una definizione di criteri (una o più regole o condizioni) che vengono attivati quando vengono soddisfatti.
* **Attivo**: lo stato di quando vengono soddisfatti i criteri definiti da un avviso classico.
* **Risolto**: lo stato di quando i criteri definiti da un avviso classico non sono più soddisfatti dopo esserlo stati precedentemente.
* **Notifica**: l'azione eseguita quando un avviso classico diventa attivo.
* **Azione**: una chiamata specifica inviata a un ricevitore di una notifica (ad esempio, l'invio di un messaggio di posta elettronica a un indirizzo o la pubblicazione in un URL del webhook). Le notifiche possono in genere attivare più azioni.


## <a name="alerts-on-azure-monitor-data"></a>Avvisi sui dati di Monitoraggio di Azure
Esistono tre tipi di avvisi al di fuori dei dati disponibili in Monitoraggio di Azure: avvisi metrica, avvisi metrica quasi in tempo reale e avvisi del log attività.

* **Avvisi delle metriche classici**: questo avviso si attiva quando il valore di una data metrica supera una soglia assegnata. L'avviso genera una notifica quando viene "attivato" (quando la soglia viene superata e viene soddisfatta la condizione di avviso) e quando viene "risolto" (quando il valore rientra nella soglia e la condizione non viene più soddisfatta). Questi sono gli avvisi di metrica meno recenti. Per gli avvisi di metrica più recenti, vedere di seguito.

* **Avvisi delle metriche near real time** (nuovo tipo di avvisi): si tratta di una nuova generazione di avvisi delle metriche con funzionalità migliorate rispetto agli avvisi delle metriche di tipo precedente. Questi avvisi possono essere eseguiti con una frequenza di 1 minuto. Supportano inoltre il monitoraggio di diverse metriche (attualmente due).  L'avviso genera una notifica quando viene "attivato" (quando la soglia per ogni metrica viene superata allo stesso tempo e la condizione di avviso viene soddisfatta) e quando viene "risolto" (quando almeno una metrica rientra nella soglia e la condizione non viene più soddisfatta).

* **Avvisi classici del log attività**: un avviso di log in streaming si attiva quando viene generato un evento di log attività che corrisponde ai criteri di filtro assegnati. Questi avvisi hanno un solo stato, vale a dire "Attivato", perché il motore degli avvisi applica semplicemente i criteri di filtro a qualsiasi nuovo evento. Questi avvisi possono essere usati per notificare quando si verifica un nuovo evento imprevisto relativo all'integrità del servizio o quando un utente o un'applicazione esegue un'operazione nella sottoscrizione, ad esempio "Elimina macchina virtuale".

Per i dati del registro di diagnostica disponibili tramite Monitoraggio di Azure, è consigliabile indirizzare i dati in Log Analytics e usare un avviso di Log Analytics. Il diagramma seguente riepiloga le origini dei dati in Monitoraggio di Azure e, a livello concettuale, i modi per avvisare di tali dati.

![Avvisi illustrati](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Come si riceve una notifica su un avviso classico di Monitoraggio di Azure?
Gli avvisi di Azure nelle versioni precedenti da servizi diversi usano metodi di notifica predefiniti propri. Monitoraggio di Azure offre ora un raggruppamento di notifiche riutilizzabili chiamato gruppi di azioni. I gruppi di azioni specificano un set di destinatari di una notifica (un numero qualsiasi di indirizzi di posta elettronica, numeri di telefono (per SMS) o URL di webhook) e ogni volta che viene attivato un avviso che fa riferimento al gruppo di azioni, tutti i destinatari ricevono la notifica. Ciò consente di riutilizzare un raggruppamento di destinatari, ad esempio un elenco di ingegneri su richiesta, in molti oggetti di avviso. Solo gli avvisi del log attività prevedono l'utilizzo di gruppi di azioni al momento, ma anche molti altri tipi di avviso di Azure useranno presto i gruppi di azioni.

I gruppi di azioni supportano la notifica tramite la registrazione a un URL di webhook oltre che agli indirizzi di posta elettronica e ai numeri SMS. Ciò consente l'automazione e la correzione, ad esempio, usando:
    - Runbook di Automazione di Azure
    - Funzione di Azure
    - App per la logica di Azure
    - un servizio di terze parti

Gli avvisi di metrica quasi in tempo reale e gli avvisi del log attività usano gruppi di azioni.

Gli avvisi di metrica meno recenti disponibili nella sezione degli avvisi (versione classica) non utilizzano gruppi di azioni. In un singolo avviso di metrica è possibile configurare le notifiche per:
* Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
* Richiamare un webhook, che consente di avviare altre azioni di automazione.

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole degli avvisi e sulla relativa configurazione usando:

* Altre informazioni sulle [metriche](monitoring-overview-metrics.md)
* Configurare gli [avvisi delle metriche classici tramite il portale di Azure](insights-alerts-portal.md)
* Configurare gli [avvisi delle metriche classici per PowerShell](insights-alerts-powershell.md)
* Configurare gli [avvisi delle metriche classici per l'interfaccia della riga di comando](insights-alerts-command-line-interface.md)
* Configurare gli [avvisi delle metriche classici per l'API REST del Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Altre informazioni sul [log attività](monitoring-overview-activity-logs.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](monitoring-activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
* Altre informazioni sui [nuovi avvisi delle metriche](monitoring-near-real-time-metric-alerts.md)
* Altre informazioni sulle [notifiche del servizio](monitoring-service-notifications.md)
* Altre informazioni sui [gruppi di azione](monitoring-action-groups.md)
* Configurare gli [avvisi](monitor-alerts-unified-usage.md)
