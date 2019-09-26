---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0a3bc6bcae2f06173cbc334ffe80e2dfa001e407
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309272"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

> [!NOTE]
> Un subset di soluzioni di sicurezza è stato ritirato il 31 luglio 2019. Per ulteriori informazioni e servizi alternativi, vedere [ritiro delle funzionalità del Centro sicurezza (luglio 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per soluzioni come antimalware e valutazione della vulnerabilità, il Centro sicurezza può effettuare il provisioning dell'agente nelle macchine virtuali. Per le appliance firewall, il Centro sicurezza può gestire la maggior parte delle configurazioni di rete necessarie.
- **Rilevamenti integrati**: Gli eventi di sicurezza delle soluzioni partner vengono raccolti, aggregati e visualizzati automaticamente nell'ambito degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: I clienti possono usare gli eventi di integrità integrati per monitorare in modo immediato tutte le soluzioni partner. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente, le soluzioni di sicurezza integrate includono la valutazione della vulnerabilità di [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e del Web Application Firewall del gateway applicazione Microsoft.

> [!NOTE]
> Il Centro sicurezza non installa il Microsoft Monitoring Agent su appliance virtuali partner perché la maggior parte dei fornitori di sicurezza impedisce l'esecuzione di agenti esterni nei propri dispositivi.
>
>

## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. È anche possibile connettere altre origini dati di sicurezza, inclusi i computer in esecuzione in locale o in altri cloud.

![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

3. Selezionare **Soluzioni di sicurezza** nel menu del Centro sicurezza.

   ![Panoramica del Centro sicurezza](./media/security-center-partner-integration/overview.png)

Nelle **soluzioni di sicurezza**è possibile visualizzare l'integrità delle soluzioni di sicurezza di Azure integrate ed eseguire attività di gestione di base.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **soluzioni connesse** include le soluzioni di sicurezza attualmente connesse al centro sicurezza. Mostra anche lo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Una soluzione partner può avere uno degli stati seguenti:

* Integro (verde): nessun problema di integrità.
* Non integro (rosso): si è verificato un problema di integrità che richiede attenzione immediata.
* Problemi di integrità (arancione): la soluzione non segnala più lo stato di integrità.
* Non segnalato (grigio): la soluzione non ha ancora segnalato alcun dato e i dati di integrità non sono disponibili. Lo stato di una soluzione può non essere segnalato se è stato connesso di recente ed è ancora in distribuzione.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati di integrità e non è stato ricevuto alcun avviso negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non segnala.
>
>

1. Selezionare **Visualizza** per ulteriori informazioni e opzioni, ad esempio:

   - **Console della soluzione**. Apre l'esperienza di gestione per questa soluzione.
   - **Collega la macchina virtuale**. Apre la pagina di collegamento delle applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
   - **Elimina soluzione**.
   - **Configura**.

   ![Dettagli della soluzione di un partner](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluzioni individuate

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure ma non connesse al centro sicurezza e visualizza le soluzioni nella sezione **soluzioni individuate** . Queste soluzioni includono soluzioni di Azure, come [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluzioni partner.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. Per altre informazioni sui piani tariffari, vedere [prezzi](security-center-pricing.md) .
>
>

Selezionare **Connetti** in una soluzione per l'integrazione con il Centro sicurezza e ricevere notifiche relative agli avvisi di sicurezza.

![Soluzioni individuate](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Aggiungi origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Esportazione dei dati in un sistema di informazioni di sicurezza e gestione degli eventi

È possibile configurare SIEMs o altri strumenti di monitoraggio per ricevere gli eventi del Centro sicurezza di Azure.

Tutti gli eventi del Centro sicurezza di Azure vengono pubblicati nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)di Azure di monitoraggio di Azure. Monitoraggio di Azure usa [una pipeline consolidata](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) per trasmettere i dati a un hub eventi in cui è possibile eseguire il pull nello strumento di monitoraggio.

Le sezioni seguenti spiegano come configurare i dati per poterli trasmettere a un hub eventi. In questi passaggi si presuppone che l'utente abbia già configurato Centro sicurezza di Azure nella sottoscrizione Azure.

### <a name="high-level-overview"></a>Panoramica generale

![Panoramica generale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quali dati della sicurezza di Azure vengono esposti a SIEM?

In questa versione vengono esposti gli [avvisi di sicurezza.](../security-center/security-center-managing-and-responding-alerts.md) Nelle prossime versioni il set di dati verrà ampliato con le raccomandazioni sulla sicurezza.

### <a name="how-to-set-up-the-pipeline"></a>Come configurare la pipeline

#### <a name="create-an-event-hub"></a>Creare un hub eventi

Prima di iniziare, [creare uno spazio dei nomi di hub eventi](../event-hubs/event-hubs-create.md) , ovvero la destinazione per tutti i dati di monitoraggio.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi

Vedere l'articolo seguente [trasmettere il log attività a hub eventi](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Installare un connettore SIEM partner 

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner.

Per l'elenco dei [Siem supportati](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub) , vedere l'articolo seguente.

### <a name="example-for-querying-data"></a>Esempio di query sui dati 

Ecco alcune query Splunk che è possibile usare per eseguire il pull dei dati di avviso:

| **Descrizione della query** | **Query** |
|----|----|
| Tutti gli avvisi| index=main Microsoft.Security/locations/alerts|
| Riepilogare il numero di operazioni in base al nome| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Ottenere informazioni sugli avvisi: ora, nome, stato, ID e sottoscrizione | index=main Microsoft.Security/locations/alerts \| tabella \_time, properties.eventName, stato, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
