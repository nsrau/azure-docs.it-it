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
ms.openlocfilehash: 435ae9f08f718a9310fd1687fb7859058edf8b45
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384254"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

> [!NOTE]
> A subset of security solutions has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. For solutions like antimalware and vulnerability assessment, Security Center can provision the agent on your virtual machines. For firewall appliances, Security Center can take care of much of the network configuration required.
- **Rilevamenti integrati**: gli eventi di sicurezza delle soluzioni dei partner vengono raccolti, aggregati e visualizzati automaticamente come parte degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: i clienti possono usare eventi di integrità integrati per monitorare tutte le soluzioni dei partner in un attimo. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Currently, integrated security solutions include vulnerability assessment by [Qualys](https://www.qualys.com/public-cloud/#azure) and [Rapid7](https://www.rapid7.com/products/insightvm/) and Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center does not install the Microsoft Monitoring Agent on partner virtual appliances because most security vendors prohibit external agents running on their appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. You can also connect other security data sources, including computers running on-premises or in other clouds.

![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

3. Selezionare **Soluzioni di sicurezza** nel menu del Centro sicurezza.

   ![Panoramica del Centro sicurezza](./media/security-center-partner-integration/overview.png)

In **Security solutions**, you can see the health of integrated Azure security solutions and run basic management tasks.

### <a name="connected-solutions"></a>Soluzioni connesse

The **Connected solutions** section includes security solutions that are currently connected to Security Center. It also shows the health status of each solution.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Una soluzione partner può avere uno degli stati seguenti:

* Healthy (green) - no health issues.
* Unhealthy (red) - there's a health issue that requires immediate attention.
* Problemi di integrità (arancione): la soluzione non segnala più lo stato di integrità.
* Not reported (gray) - the solution hasn't reported anything yet and no health data is available. A solution's status may be unreported if it was connected recently and is still deploying.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. If no health data is available and no alerts were received within the last 14 days, Security Center indicates that the solution is unhealthy or not reporting.
>
>

1. Select **VIEW** for additional information and options such as:

   - **Console della soluzione**. Apre l'esperienza di gestione per questa soluzione.
   - **Collega la macchina virtuale**. Opens the Link Applications page. In questo pannello è possibile connettere risorse alla soluzione del partner.
   - **Elimina soluzione**.
   - **Configura**.

   ![Dettagli della soluzione di un partner](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

Security Center automatically discovers security solutions running in Azure but not connected to Security Center and displays the solutions in the **Discovered solutions** section. These  solutions include Azure solutions, like [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), and partner solutions.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. See [Pricing](security-center-pricing.md) to learn more about the pricing tiers.
>
>

Select **CONNECT** under a solution to integrate with Security Center and be notified of security alerts.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Esportazione dei dati in un sistema di informazioni di sicurezza e gestione degli eventi

You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

All events from Azure Security Center are published to Azure Monitor's Azure [Activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor uses [a consolidated pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) to  stream the data to an Event Hub where it can then be pulled into your monitoring tool.

Le sezioni seguenti spiegano come configurare i dati per poterli trasmettere a un hub eventi. In questi passaggi si presuppone che l'utente abbia già configurato Centro sicurezza di Azure nella sottoscrizione Azure.

### <a name="high-level-overview"></a>Panoramica di alto livello

![Panoramica generale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quali dati della sicurezza di Azure vengono esposti a SIEM?

In this version, we expose the [security alerts.](../security-center/security-center-managing-and-responding-alerts.md) Nelle prossime versioni il set di dati verrà ampliato con le raccomandazioni sulla sicurezza.

### <a name="how-to-set-up-the-pipeline"></a>How to set up the pipeline

#### <a name="create-an-event-hub"></a>Creare un hub eventi

Before you begin, [create an Event Hubs namespace](../event-hubs/event-hubs-create.md) - the destination for all your monitoring data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi

See the following article [stream activity log to Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Installare un connettore SIEM partner 

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner.

See the following article for the list of [supported SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Esempio di query sui dati 

Here are some Splunk queries you can use to pull alert data:

| **Descrizione della query** | **Query** |
|----|----|
| Tutti gli avvisi| index=main Microsoft.Security/locations/alerts|
| Riepilogare il numero di operazioni in base al nome| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Ottenere le informazioni sugli avvisi: ora, nome, stato, ID e sottoscrizione | index=main Microsoft.Security/locations/alerts \| tabella \_time, properties.eventName, stato, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
