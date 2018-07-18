---
title: Panoramica degli avvisi classici in Microsoft Azure e Monitoraggio di Azure
description: Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 89c3975a1212aa991e42e0cce4fe5521b53bd373
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263671"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi classici in Microsoft Azure?

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [nuovi avvisi delle metriche near real time e una nuova esperienza di avvisi](monitoring-overview-unified-alerts.md). 
>

Gli avvisi consentono di configurare le condizioni sui dati e la notifica di quando le condizioni corrispondono ai dati di monitoraggio più recenti.


## <a name="alerts-on-azure-monitor-data"></a>Avvisi sui dati di Monitoraggio di Azure
Sono disponibili due tipi di avvisi classici, vale a dire gli avvisi delle metriche e gli avvisi del log attività.

* **Avvisi delle metriche classici**: questo avviso si attiva quando il valore di una data metrica supera una soglia assegnata. L'avviso genera una notifica quando viene "attivato" (quando la soglia viene superata e viene soddisfatta la condizione di avviso) e quando viene "risolto" (quando il valore rientra nella soglia e la condizione non viene più soddisfatta). Per gli avvisi di metrica più recenti, vedere di seguito.

* **Avvisi classici del log attività**: un avviso di log in streaming si attiva quando viene generato un evento di log attività che corrisponde ai criteri di filtro assegnati. Questi avvisi hanno un solo stato, vale a dire "Attivato", perché il motore degli avvisi applica semplicemente i criteri di filtro a qualsiasi nuovo evento. Questi avvisi possono essere usati per notificare quando si verifica un nuovo evento imprevisto relativo all'integrità del servizio o quando un utente o un'applicazione esegue un'operazione nella sottoscrizione, ad esempio "Elimina macchina virtuale".

Per i dati del log di diagnostica disponibili tramite Monitoraggio di Azure, è consigliabile indirizzare i dati in Log Analytics (in precedenza OMS) e usare un avviso di query di Log Analytics. Log Analytics ora usa il [nuovo metodo di avviso](monitoring-overview-unified-alerts.md) 

Il diagramma seguente riepiloga le origini dei dati in Monitoraggio di Azure e, a livello concettuale, i modi per avvisare di tali dati.

![Avvisi illustrati](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Tassonomia degli avvisi di Monitoraggio di Azure (classici)
Azure usa i termini seguenti per descrivere gli avvisi classici e le relative funzioni:
* **Avviso**: una definizione di criteri (una o più regole o condizioni) che vengono attivati quando vengono soddisfatti.
* **Attivo**: lo stato di quando vengono soddisfatti i criteri definiti da un avviso classico.
* **Risolto**: lo stato di quando i criteri definiti da un avviso classico non sono più soddisfatti dopo esserlo stati precedentemente.
* **Notifica**: l'azione eseguita quando un avviso classico diventa attivo.
* **Azione**: una chiamata specifica inviata a un ricevitore di una notifica (ad esempio, l'invio di un messaggio di posta elettronica a un indirizzo o la pubblicazione in un URL del webhook). Le notifiche possono in genere attivare più azioni.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Come si riceve una notifica da un avviso classico di Monitoraggio di Azure?
Gli avvisi di Azure nelle versioni precedenti da servizi diversi usano metodi di notifica predefiniti propri. 

Monitoraggio di Azure offre ora un raggruppamento di notifiche riutilizzabili denominato *gruppi di azioni*. I gruppi di azioni specificano un set di destinatari di una notifica. Ogni volta che viene attivato un avviso che fa riferimento al gruppo di azioni, tutti i destinatari ricevono tale notifica. Ciò consente di riutilizzare un raggruppamento di destinatari, ad esempio un elenco di ingegneri su richiesta, in molti oggetti di avviso. I gruppi di azioni supportano la notifica, oltre ad altre azioni, tramite la registrazione a un URL di webhook, agli indirizzi di posta elettronica e ai numeri SMS.  Per altre informazioni, vedere [Gruppi di azioni](monitoring-action-groups.md). 

Gli avvisi classici precedenti del log attività usano i gruppi di azioni.

Non usano invece i gruppi di azioni gli avvisi delle metriche precedenti. È comunque possibile configurare le azioni seguenti: 
* Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
* Richiamare un webhook, che consente di avviare altre azioni di automazione.

I webhook consentono l'automazione e la correzione, ad esempio, usando:
    - Runbook di Automazione di Azure
    - Funzione di Azure
    - App per la logica di Azure
    - un servizio di terze parti

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
* Altre informazioni sui [gruppi di azione](monitoring-action-groups.md)
* Configurare [nuovi avvisi](monitor-alerts-unified-usage.md)
