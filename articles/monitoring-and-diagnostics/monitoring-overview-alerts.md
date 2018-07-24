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
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114012"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi classici in Microsoft Azure?

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [nuovi avvisi delle metriche near real time e una nuova esperienza di avvisi](monitoring-overview-unified-alerts.md). 
>

Usando gli avvisi, è possibile configurare le condizioni sui dati e ricevere una notifica quando le condizioni corrispondono ai dati di monitoraggio più recenti.


## <a name="alerts-on-azure-monitor-data"></a>Avvisi sui dati di Monitoraggio di Azure
Sono disponibili due tipi di avvisi classici: gli avvisi delle metriche e gli avvisi del log attività.

* **Avvisi delle metriche classici**: questo avviso si attiva quando il valore di una data metrica supera una soglia assegnata. L'avviso genera una notifica quando "attivato" (quando la soglia viene superata e la condizione di avviso è soddisfatta). Inoltre, genera un avviso quando viene "risolto" (quando il valore rientra nella soglia e la condizione non è più soddisfatta). 

* **Avvisi classici del log attività**: questo avviso di log in streaming si attiva quando viene generato un evento di log attività che corrisponde ai criteri di filtro assegnati. Questi avvisi hanno un solo stato, vale a dire "attivato", perché il motore degli avvisi applica semplicemente i criteri di filtro a qualsiasi nuovo evento. Questi avvisi possono notificare quando si verifica un nuovo evento imprevisto relativo all'integrità dei servizi di Azure o quando un utente o un'applicazione esegue un'operazione nella sottoscrizione, ad esempio "Elimina macchina virtuale."

Per ricevere i log dati di diagnostica che sono disponibili tramite Monitoraggio di Azure, instradare i dati in Log Analytics (noto in precedenza come Operations Management Suite) e usare un avviso di query di Log Analytics. Log Analytics ora usa il [nuovo metodo di avviso](monitoring-overview-unified-alerts.md). 

Il diagramma seguente riepiloga le origini dei dati in Monitoraggio di Azure e suggerisce i modi per avvisare di tali dati.

![Avvisi illustrati](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Tassonomia degli avvisi di Monitoraggio di Azure (classici)
Azure usa i termini seguenti per descrivere gli avvisi classici e le relative funzioni:
* **Avviso**: la definizione di criteri (una o più regole o condizioni) che vengono attivati quando vengono soddisfatti.
* **Attivo**: lo stato di quando vengono soddisfatti i criteri definiti da un avviso classico.
* **Risolto**: lo stato di quando i criteri definiti da un avviso classico non sono più soddisfatti dopo esserlo stati precedentemente.
* **Notifica**: l'azione eseguita quando un avviso classico diventa attivo.
* **Azione**: chiamata specifica che viene inviata al ricevitore di una notifica (ad esempio, un messaggio di posta elettronica o un post in un URL webhook). Le notifiche possono in genere attivare più azioni.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Come si ricevono notifiche da un avviso classico di Monitoraggio di Azure?
Gli avvisi di Azure nelle versioni precedenti da servizi diversi usano metodi di notifica predefiniti propri. 

Monitoraggio di Azure offre ora un raggruppamento di notifiche riutilizzabili denominato *gruppi di azioni*. I gruppi di azioni specificano un set di destinatari di una notifica. Quando viene attivato un avviso che fa riferimento al gruppo di azione, tutti i ricevitori ricevono la notifica. Questa funzionalità permette di riutilizzare un raggruppamento di destinatari, ad esempio un elenco di ingegneri su richiesta, in molti oggetti di avviso. I gruppi di azioni supportano la notifica tramite metodi diversi. Questi metodi possono includere la pubblicazione in un URL webhook, l'invio di messaggi di posta elettronica, messaggi SMS e un numero di altre azioni. Per altre informazioni, consultare [Come creare e gestire gruppi di azione nel portale di Azure](monitoring-action-groups.md). 

Gli avvisi classici precedenti del log attività usano i gruppi di azioni.

In ogni caso, non usano i gruppi di azioni gli avvisi delle metriche precedenti. È comunque possibile configurare le azioni seguenti: 
* Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
* Richiamare un webhook, che consente di avviare altre azioni di automazione.

I webhook consentono l'automazione e la correzione, ad esempio, usando i servizi seguenti:
- Runbook di Automazione di Azure
- Funzioni di Azure
- App per la logica di Azure
- Un servizio di terze parti

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole di avviso e su come configurarle usando la documentazione seguente:

* Altre informazioni sulle [metriche](monitoring-overview-metrics.md)
* Configurare gli [avvisi delle metriche classici tramite il portale di Azure](insights-alerts-portal.md)
* Configurare gli [avvisi delle metriche classici tramite PowerShell](insights-alerts-powershell.md)
* Configurare gli [avvisi delle metriche classici tramite l'interfaccia della riga di comando di Azure](insights-alerts-command-line-interface.md)
* Configurare gli [avvisi delle metriche classici tramite l'API REST del Monitoraggio di Azure ](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Altre informazioni sui [log attività](monitoring-overview-activity-logs.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](monitoring-activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Gestione risorse](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
* Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md)
* Configurare [nuovi avvisi](monitor-alerts-unified-usage.md)
