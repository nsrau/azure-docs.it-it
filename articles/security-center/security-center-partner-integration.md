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
ms.openlocfilehash: f7a1eccd76313c5b3bc74a5b5ebdbcd202ca6841
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435761"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

> [!NOTE]
> Un sottoinsieme di soluzioni di sicurezza è stato ritirato il 31 luglio 2019. Per ulteriori informazioni e servizi alternativi, vedere [Retirement of Security Center features (luglio 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per soluzioni come la valutazione antimalware e vulnerabilità, il Centro sicurezza può eseguire il provisioning dell'agente nelle macchine virtuali. Per le appliance firewall, il Centro sicurezza può occuparsi di gran parte della configurazione di rete necessaria.
- **Rilevamenti integrati:** gli eventi di sicurezza delle soluzioni partner vengono automaticamente raccolti, aggregati e visualizzati come parte degli avvisi e degli eventi del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: i clienti possono usare eventi di integrità integrati per monitorare tutte le soluzioni dei partner in un attimo. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente, le soluzioni di sicurezza integrate includono la valutazione delle vulnerabilità da parte di [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e Microsoft Application Gateway Web application application application firewall.

> [!NOTE]
> Il Centro sicurezza non installa l'agente Log Analytics nelle appliance virtuali partner perché la maggior parte dei fornitori di sicurezza proibisce gli agenti esterni in esecuzione sulle appliance.
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

In **Soluzioni**di sicurezza è possibile visualizzare l'integrità delle soluzioni di sicurezza di Azure integrate ed eseguire attività di gestione di base.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** include soluzioni di sicurezza attualmente connesse al Centro sicurezza. Mostra anche lo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Una soluzione partner può avere uno degli stati seguenti:

* Sano (verde) - nessun problema di salute.
* Non integro (rosso) - c'è un problema di salute che richiede attenzione immediata.
* Problemi di integrità (arancione): la soluzione non segnala più lo stato di integrità.
* Non segnalato (grigio) - la soluzione non ha ancora segnalato nulla e non sono disponibili dati sanitari. Lo stato di una soluzione potrebbe non essere segnalato se è stata connessa di recente ed è ancora in fase di distribuzione.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati sull'integrità e non sono stati ricevuti avvisi negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non è stata segnalata.
>
>

1. Selezionare **VISTA** per ulteriori informazioni e opzioni quali:

   - **Console della soluzione**. Apre l'esperienza di gestione per questa soluzione.
   - **Collega la macchina virtuale**. Apre la pagina Collega applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
   - **Elimina soluzione**.
   - **Configurare**.

   ![Dettagli della soluzione di un partner](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure ma non connesse al Centro sicurezza e visualizza le soluzioni nella sezione **Soluzioni individuate.** Queste soluzioni includono soluzioni di Azure, ad esempio [Azure AD Identity Protection,](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluzioni per i partner.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. Per altre informazioni sui piani tariffari, vedere [Prezzi.](security-center-pricing.md)
>
>

Selezionare **CONNECT** in una soluzione per l'integrazione con il Centro sicurezza e ricevere una notifica degli avvisi di sicurezza.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Esportazione dei dati in un sistema di informazioni di sicurezza e gestione degli eventi

> [!NOTE]
> Per informazioni dettagliate su un metodo più semplice (attualmente in anteprima) per l'esportazione dei dati in un SIEM, vedere [Export security alerts and recommendations (Preview)](continuous-export.md). Il nuovo metodo non utilizza il registro attività come intermediatore e consente l'esportazione diretta dal Centro sicurezza agli hub eventi (e quindi al SIEM), supporta anche l'esportazione di raccomandazioni di sicurezza.


È possibile configurare i SIEM o altri strumenti di monitoraggio per ricevere gli eventi del Centro sicurezza di Azure.You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

Tutti gli eventi dal Centro sicurezza di Azure vengono pubblicati nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)di Azure Monitor di Azure. Monitoraggio di Azure usa [una pipeline consolidata](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) per trasmettere i dati a un hub eventi in cui possono essere inseriti nello strumento di monitoraggio.

Le sezioni seguenti spiegano come configurare i dati per poterli trasmettere a un hub eventi. In questi passaggi si presuppone che l'utente abbia già configurato Centro sicurezza di Azure nella sottoscrizione Azure.

### <a name="high-level-overview"></a>Panoramica generale

![Panoramica generale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quali dati della sicurezza di Azure vengono esposti a SIEM?

In questa versione vengono esposti gli avvisi di [sicurezza.](../security-center/security-center-managing-and-responding-alerts.md) Nelle prossime versioni il set di dati verrà ampliato con le raccomandazioni sulla sicurezza.

### <a name="how-to-set-up-the-pipeline"></a>Come configurare la pipeline

#### <a name="create-an-event-hub"></a>Creare un hub eventi

Prima di iniziare, [creare uno spazio dei nomi Hub eventi,](../event-hubs/event-hubs-create.md) ovvero la destinazione per tutti i dati di monitoraggio.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi

Vedere il seguente [registro attività flusso agli hub eventi](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Installare un connettore SIEM partner 

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner.

Vedere l'articolo seguente per l'elenco dei [SIEM supportati](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Esempio di query sui dati 

Di seguito sono riportate alcune query Splunk che è possibile utilizzare per estrarre i dati degli avvisi:

| **Descrizione della query** | **Query** |
|----|----|
| Tutti gli avvisi| index=main Microsoft.Security/locations/alerts|
| Riepilogare il numero di operazioni in base al nome| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Ottenere le informazioni sugli avvisi: ora, nome, stato, ID e sottoscrizione | index=main Microsoft.Security/locations/alerts \| tabella \_time, properties.eventName, stato, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.