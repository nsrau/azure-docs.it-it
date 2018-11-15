---
title: Panoramica degli avvisi classici in Microsoft Azure e Monitoraggio di Azure
description: Gli avvisi classici sono in fase di deprecazione. Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: e7622ffcfbaca4f83f0b5233159fc94720148365
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614141"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi classici in Microsoft Azure?

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [nuovi avvisi delle metriche near real time e una nuova esperienza di avvisi](monitoring-overview-alerts.md). 
>

Gli avvisi consentono di configurare le condizioni sui dati e la notifica di quando le condizioni corrispondono ai dati di monitoraggio più recenti.

## <a name="old-and-new-alerting-capabilities"></a>Vecchie e nuove funzionalità di avviso

In precedenza Monitoraggio di Azure, Application Insights, Log Analytics e integrità dei servizi di Azure avevano funzionalità di avviso separate. Con il tempo Azure ha migliorato e combinato sia l'interfaccia utente che i diversi metodi di invio degli avvisi. Il consolidamento è ancora in corso. Avvisi

È possibile visualizzare gli avvisi classici solo nella schermata dell'utente degli avvisi classici nel portale di Azure. Questa schermata viene visualizzata usando il pulsante **Visualizza avvisi classici** sulla schermata degli avvisi. 

 ![Opzioni di avviso nel portale di Azure](./media/monitoring-overview-alerts-classic/monitor-alert-screen2.png)

La nuova esperienza utente degli avvisi offre i vantaggi seguenti rispetto all'esperienza degli avvisi in versione classica:
-   **Migliore sistema di notifica**: tutti gli avvisi più recenti usano gruppi di azioni, ovvero gruppi di azioni e notifiche con nome che possono essere riusati in più avvisi. Al contrario, gli avvisi delle metriche classici e gli avvisi di Log Analytics di tipo precedente non usano gruppi di azioni.
-   **Esperienza di creazione unificata**: tutte le funzionalità di creazione per metriche, log e log attività in Monitoraggio di Azure, Log Analytics e Application Insights si trovano in un'unica posizione.
-   **Visualizzazione degli avvisi di Log Analytics attivati nel portale di Azure**: è ora possibile visualizzare anche gli avvisi di Log Analytics attivati nella sottoscrizione. In precedenza, era necessario un portale separato.
-   **Separazione tra avvisi attivati e regole di avviso**: viene fatta distinzione tra regole di avviso (definizioni delle condizioni che attivano gli avvisi) e avvisi attivati (istanze di attivazione delle regole di avviso) e sono pertanto disponibili visualizzazioni separate per le attività operative e la configurazione.
-   **Flusso di lavoro migliore**: la nuova esperienza di creazione di avvisi guida l'utente nel processo di configurazione di una regola di avviso, rendendo più semplice l'identificazione delle condizioni per cui è opportuno ricevere un avviso.
-   **Consolidamento degli avvisi intelligenti** e **impostazione dello stato dell'avviso**: gli avvisi più recenti includono funzionalità di raggruppamento automatico che mostrano avvisi simili tra loro per ridurre l'overload dell'interfaccia utente. 

Gli avvisi delle metriche più recenti presentano i seguenti vantaggi rispetto agli avvisi delle metriche classici:
-   **Latenza migliorata**: gli avvisi delle metriche più recenti possono essere eseguiti con una frequenza di un minuto. Gli avvisi metrica meno recenti sono sempre eseguibili con una frequenza di 5 minuti. Gli avvisi più recenti presentano un ritardo più breve tra il verificarsi del problema e la notifica o l'azione (da 3 a 5 minuti). Gli avvisi precedenti vanno da 5 a 15 minuti a seconda del tipo.  Gli avvisi relativi ai log presentano in genere un ritardo di 10-15 minuti a causa del tempo necessario per inserire i log, ma i metodi di elaborazione più recenti stanno riducendo questo tempo. 
-   **Supporto delle metriche multidimensionali**: è possibile inviare un avviso per le metriche multidimensionali, in modo da monitorare un segmento interessante della metrica.
-   **Maggiore controllo delle condizioni delle metriche**: è possibile definire regole di avviso più dettagliate. Gli avvisi più recenti supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
-   **Monitoraggio combinato di più metriche**: è possibile monitorare più metriche (attualmente un massimo di due) con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
-   **Migliore sistema di notifica**: tutti gli avvisi più recenti usano [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md), ovvero gruppi di azioni e notifiche con nome che possono essere riusati in più avvisi.  Al contrario, gli avvisi delle metriche classici e gli avvisi di Log Analytics di tipo precedente non usano gruppi di azioni. 
-   **Metriche dai log** (anteprima pubblica): i dati di log diretti a Log Analytics possono essere ora estratti in metriche di Monitoraggio di Azure e quindi essere oggetto di avvisi come le altre metriche. Vedere [Avvisi (versione classica)](monitoring-overview-alerts-classic.md) per la terminologia degli avvisi classici. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Avvisi classici sui dati di Monitoraggio di Azure
Sono disponibili due tipi di avvisi classici, vale a dire gli avvisi delle metriche e gli avvisi del log attività.

* **Avvisi delle metriche classici**: questo avviso si attiva quando il valore di una data metrica supera una soglia assegnata. L'avviso genera una notifica quando è "Attivato" (quando la soglia viene superata e la condizione di avviso è soddisfatta). Inoltre, genera un'altra notifica quando viene "Risolto" (quando il valore rientra nella soglia e la condizione non è più soddisfatta).

* **Avvisi classici del log attività**: un avviso di log in streaming si attiva quando viene generato un evento di log attività che corrisponde ai criteri di filtro assegnati. Questi avvisi hanno un solo stato, vale a dire "Attivato", perché il motore degli avvisi applica semplicemente i criteri di filtro a qualsiasi nuovo evento. Questi avvisi possono essere usati per notificare quando si verifica un nuovo evento imprevisto relativo all'integrità del servizio o quando un utente o un'applicazione esegue un'operazione nella sottoscrizione, ad esempio "Elimina macchina virtuale".

Per i dati del log di diagnostica disponibili tramite Monitoraggio di Azure, indirizzare i dati in Log Analytics (in precedenza OMS) e usare un avviso di query di Log Analytics. Log Analytics ora usa il [nuovo metodo di avviso](monitoring-overview-alerts.md) 

Il diagramma seguente riepiloga le origini dei dati in Monitoraggio di Azure e, a livello concettuale, i modi per avvisare di tali dati.

![Avvisi illustrati](./media/monitoring-overview-alerts-classic/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Tassonomia degli avvisi (versione classica)
Azure usa i termini seguenti per descrivere gli avvisi classici e le relative funzioni:
* **Avviso**: una definizione di criteri (una o più regole o condizioni) che vengono attivati quando vengono soddisfatti.
* **Attivo**: lo stato di quando vengono soddisfatti i criteri definiti da un avviso classico.
* **Risolto**: lo stato di quando i criteri definiti da un avviso classico non sono più soddisfatti dopo esserlo stati precedentemente.
* **Notifica**: l'azione eseguita quando un avviso classico diventa attivo.
* **Azione**: una chiamata specifica inviata a un ricevitore di una notifica (ad esempio, l'invio di un messaggio di posta elettronica a un indirizzo o la pubblicazione in un URL del webhook). Le notifiche possono in genere attivare più azioni.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Come si riceve una notifica da un avviso classico di Monitoraggio di Azure?
Gli avvisi di Azure nelle versioni precedenti da servizi diversi usano metodi di notifica predefiniti propri. 

Monitoraggio di Azure ha creato un raggruppamento di notifiche riutilizzabili denominato *gruppi di azioni*. I gruppi di azioni specificano un set di destinatari di una notifica. Ogni volta che viene attivato un avviso che fa riferimento al gruppo di azioni, tutti i destinatari ricevono tale notifica. I gruppi di azioni consentono di riutilizzare un raggruppamento di destinatari, ad esempio un elenco di tecnici su richiesta, in molti oggetti di avviso. I gruppi di azioni supportano la notifica, oltre ad altre azioni, tramite la registrazione a un URL di webhook, agli indirizzi di posta elettronica e ai numeri SMS.  Per altre informazioni, vedere [Gruppi di azioni](monitoring-action-groups.md). 

Gli avvisi classici precedenti del log attività usano i gruppi di azioni.

Non usano invece i gruppi di azioni gli avvisi delle metriche precedenti. È comunque possibile configurare le azioni seguenti: 
- Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
- Richiamare un webhook, che consente di avviare altre azioni di automazione.

I webhook consentono l'automazione e la correzione, ad esempio, usando:
    - Runbook di Automazione di Azure
    - Funzione di Azure
    - App per la logica di Azure
    - un servizio di terze parti

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole degli avvisi e sulla relativa configurazione usando:

* Altre informazioni sulle [metriche](../monitoring/monitoring-data-collection.md)
* Configurare gli [avvisi delle metriche classici tramite il portale di Azure](alert-metric-classic.md)
* Configurare gli [avvisi delle metriche classici per PowerShell](alert-metric-classic.md)
* Configurare gli [avvisi delle metriche classici per l'interfaccia della riga di comando](alert-metric-classic.md)
* Configurare gli [avvisi delle metriche classici per l'API REST del Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Altre informazioni sul [log attività](monitoring-overview-activity-logs.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](monitoring-activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Resource Manager](alert-activity-log.md)
* Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
* Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md)
* Configurare [nuovi avvisi](alert-metric.md)
