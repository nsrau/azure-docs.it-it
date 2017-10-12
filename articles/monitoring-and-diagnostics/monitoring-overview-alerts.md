---
title: Panoramica degli avvisi in Microsoft Azure e Monitoraggio di Azure | Microsoft Docs
description: Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afa863e2a900d4f823b77453d92f034db7d5a93f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi in Microsoft Azure?
Questo articolo descrive le varie origini di avvisi in Microsoft Azure, lo scopo di tali avvisi, i vantaggi che offrono e un'introduzione sul loro utilizzo. L'articolo è incentrato in particolare su Monitoraggio di Azure, ma include indicazioni anche relative ad altri servizi. Gli avvisi rappresentano un metodo per eseguire il monitoraggio in Azure: è possibile configurare condizioni relative ai dati e ricevere una notifica qualora tali condizioni corrispondano ai dati di monitoraggio più recenti.

## <a name="taxonomy-of-azure-alerts"></a>Tassonomia degli avvisi di Azure
Azure usa i termini seguenti per descrivere gli avvisi e le relative funzioni:
* **Avviso**: una definizione di criteri (una o più regole o condizioni) che vengono attivati quando vengono soddisfatti.
* **Attivo**: lo stato di quando vengono soddisfatti i criteri definiti da un avviso.
* **Risolto**: lo stato di quando i criteri definiti da un avviso non sono più soddisfatti dopo esserlo stati precedentemente.
* **Notifica**: l'azione eseguita quando un avviso diventa attivo.
* **Azione**: una chiamata specifica inviata a un ricevitore di una notifica (ad esempio, l'invio di un messaggio di posta elettronica a un indirizzo o la pubblicazione in un URL del webhook). Le notifiche possono in genere attivare più azioni.

## <a name="alerts-in-different-azure-services"></a>Avvisi in diversi servizi di Azure
Gli avvisi sono disponibili in vari servizi di monitoraggio di Azure. Per informazioni su come e quando usare questi servizi, [vedere questo articolo](./monitoring-overview.md). Ecco un riepilogo dei tipi di avviso disponibili in Azure:

| Service | Tipo di avviso | Servizi supportati | Descrizione |
|---|---|---|---|
| Monitoraggio di Azure | [Avvisi delle metriche](./insights-alerts-portal.md) | [Metriche supportate con Monitoraggio di Azure](./monitoring-supported-metrics.md) | Ricevere una notifica quando una metrica a livello di piattaforma soddisfa una condizione specifica (ad esempio, la % della CPU in una macchina virtuale è superiore a 90 negli ultimi 5 minuti). |
|Monitoraggio di Azure | [Avvisi metrica quasi in tempo reale (anteprima)](./monitoring-near-real-time-metric-alerts.md)| [Risorse supportate da Monitoraggio di Azure](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Ricevere una notifica più velocemente rispetto agli avvisi metrica quando una o più metriche a livello di piattaforma soddisfano le condizioni specificate, ad esempio se la percentuale di CPU su una macchina virtuale è maggiore del 90% e la rete in ingresso è maggiore di 500 MB per gli ultimi 5 minuti. |
| Monitoraggio di Azure | [Avvisi del log attività](./monitoring-activity-log-alerts.md) | Tutti i tipi di risorse disponibili in Azure Resource Manager | Ricevere una notifica quando un nuovo evento nel [Log attività di Azure](./monitoring-overview-activity-logs.md) corrisponde a condizioni specifiche (ad esempio, quando un'operazione di "eliminazione VM" si verifica in myProductionResourceGroup o quando viene generato un nuovo evento di integrità del servizio con stato "Attivo"). |
| Application Insights | [Avvisi metrica](../application-insights/app-insights-alerts.md) | Un'applicazione qualsiasi dotata delle funzionalità per l'invio di dati ad Application Insights | Ricevere una notifica quando una metrica a livello di piattaforma soddisfa una condizione specifica (ad esempio, il tempo di risposta di un server è superiore a 2 secondi). |
| Application Insights | [Avvisi di test Web](../application-insights/app-insights-monitor-web-app-availability.md) | Un sito Web qualsiasi dotato delle funzionalità per l'invio di dati ad Application Insights | Ricevere una notifica quando la disponibilità o la velocità di risposta di un sito Web è inferiore alle aspettative. |
| Log Analytics | [Avvisi di Log Analytics](../log-analytics/log-analytics-alerts.md) | Qualsiasi servizio configurato per l'invio di dati in Log Analytics | Ricevere una notifica quando una query di ricerca di Log Analytics sui dati di metrica e/o evento soddisfa criteri specifici. |

## <a name="alerts-on-azure-monitor-data"></a>Avvisi sui dati di Monitoraggio di Azure
Esistono tre tipi di avvisi al di fuori dei dati disponibili in Monitoraggio di Azure: avvisi metrica, avvisi metrica quasi in tempo reale (anteprima) e avvisi del log attività.

* **Avvisi metrica**: questo avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata. L'avviso genera una notifica quando viene "attivato" (quando la soglia viene superata e viene soddisfatta la condizione di avviso) e quando viene "risolto" (quando il valore rientra nella soglia e la condizione non viene più soddisfatta). Per un elenco in continua crescita delle metriche disponibili supportate dal monitoraggio di Azure, vedere [l'elenco delle metriche supportate in Monitoraggio di Azure](monitoring-supported-metrics.md).
* **Avvisi metrica quasi in tempo reale (anteprima)**: questi avvisi sono simili agli avvisi metrica ma sono diversi in alcuni aspetti. In primo luogo, come suggerisce il nome, questi avvisi possono essere attivati quasi in tempo reale (più velocemente di 1 minuto). Supportano inoltre il monitoraggio di più metriche (attualmente due).  L'avviso genera una notifica quando viene "attivato" (quando la soglia per ogni metrica viene superata allo stesso tempo e la condizione di avviso viene soddisfatta) e quando viene "risolto" (quando almeno una metrica rientra nella soglia e la condizione non viene più soddisfatta).

> [!NOTE]
> Gli avvisi metrica quasi in tempo reale sono attualmente disponibili in anteprima pubblica. L'esperienza utente e la funzionalità sono soggette a modifiche.
>
>

* **Avvisi del log attività**: un avviso di log in streaming si attiva quando viene generato un evento di log attività che corrisponde ai criteri di filtro assegnati. Questi avvisi hanno un solo stato, vale a dire "Attivato", perché il motore degli avvisi applica semplicemente i criteri di filtro a qualsiasi nuovo evento. Questi avvisi possono essere usati per notificare quando si verifica un nuovo evento imprevisto relativo all'integrità del servizio o quando un utente o un'applicazione esegue un'operazione nella sottoscrizione, ad esempio "Elimina macchina virtuale".

Per i dati del registro di diagnostica disponibili tramite Monitoraggio di Azure, è consigliabile indirizzare i dati in Log Analytics e usare un avviso di Log Analytics. Il diagramma seguente riepiloga le origini dei dati in Monitoraggio di Azure e, a livello concettuale, i modi per avvisare di tali dati.

![Avvisi illustrati](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Come si riceve una notifica su un avviso di Monitoraggio di Azure?
Gli avvisi di Azure nelle versioni precedenti da servizi diversi usano metodi di notifica predefiniti propri. Monitoraggio di Azure offre ora un raggruppamento di notifiche riutilizzabili chiamato gruppi di azioni. I gruppi di azioni specificano un set di destinatari di una notifica (un numero qualsiasi di indirizzi di posta elettronica, numeri di telefono (per SMS) o URL di webhook) e ogni volta che viene attivato un avviso che fa riferimento al gruppo di azioni, tutti i destinatari ricevono la notifica. Ciò consente di riutilizzare un raggruppamento di destinatari, ad esempio un elenco di ingegneri su richiesta, in molti oggetti di avviso. Solo gli avvisi del log attività prevedono l'utilizzo di gruppi di azioni al momento, ma anche molti altri tipi di avviso di Azure useranno presto i gruppi di azioni.

I gruppi di azioni supportano la notifica tramite la registrazione a un URL di webhook oltre che agli indirizzi di posta elettronica e ai numeri SMS. Ciò consente l'automazione e la correzione, ad esempio, usando:
    - Runbook di Automazione di Azure
    - Funzione di Azure
    - App per la logica di Azure
    - un servizio di terze parti

Gli avvisi metrica quasi in tempo reale (anteprima) e gli avvisi del log attività usano i gruppi di azioni.

Gli avvisi di metrica non usano ancora i gruppi di azioni. In un singolo avviso di metrica è possibile configurare le notifiche per:
* Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
* Richiamare un webhook, che consente di avviare altre azioni di automazione.

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole degli avvisi e sulla relativa configurazione usando:

* Altre informazioni sulle [metriche](monitoring-overview-metrics.md)
* Configurare gli [avvisi sulle metriche tramite il portale di Azure](insights-alerts-portal.md)
* Configurare gli [avvisi sulle metriche con PowerShell](insights-alerts-powershell.md)
* Configurare gli [avvisi sulle metriche tramite l'interfaccia della riga di comando](insights-alerts-command-line-interface.md)
* Configurare gli [avvisi sulle metriche tramite l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Altre informazioni sul [log attività](monitoring-overview-activity-logs.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](monitoring-activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
* Altre informazioni sugli [avvisi metrica quasi in tempo reale](monitoring-near-real-time-metric-alerts.md)
* Altre informazioni sulle [notifiche del servizio](monitoring-service-notifications.md)
* Altre informazioni sui [gruppi di azione](monitoring-action-groups.md)
