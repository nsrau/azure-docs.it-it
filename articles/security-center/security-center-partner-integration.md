---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2c43a3c046243c4802fb433f3b755cee158002b6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950569"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

> [!NOTE]
> Un subset di soluzioni di sicurezza è stato ritirato il 31 luglio 2019. Per ulteriori informazioni e servizi alternativi, vedere [ritiro delle funzionalità del Centro sicurezza (luglio 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per le soluzioni come quelle antimalware e di valutazione delle vulnerabilità, il Centro sicurezza può effettuare il provisioning dell'agente necessario nelle macchine virtuali, mentre per gli appliance firewall il Centro sicurezza può occuparsi di gran parte delle attività di configurazione di rete necessarie.
- **Rilevamenti integrati**: Gli eventi di sicurezza delle soluzioni partner vengono raccolti, aggregati e visualizzati automaticamente nell'ambito degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: I clienti possono usare gli eventi di integrità integrati per monitorare in modo immediato tutte le soluzioni partner. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente, le soluzioni di sicurezza integrate includono la valutazione della vulnerabilità di [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) e [Rapid7](https://www.rapid7.com/products/insightvm/) e del Web Application Firewall del gateway applicazione Microsoft.

> [!NOTE]
> Centro sicurezza non installa Microsoft Monitoring Agent nelle appliance virtuali dei partner perché la maggior parte dei fornitori di soluzioni di sicurezza non consente che vengano eseguiti agenti esterni nelle proprie appliance.
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

In **Soluzioni di sicurezza** è possibile visualizzare informazioni sull'integrità delle soluzioni di sicurezza integrate di Azure ed eseguire attività di gestione di base.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** include le soluzioni di sicurezza attualmente connesse al Centro sicurezza e informazioni sullo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Una soluzione partner può avere uno degli stati seguenti:

* Integro (verde): non ci sono problemi di integrità.
* Danneggiato (rosso): è presente un problema di integrità che richiede attenzione immediata.
* Problemi di integrità (arancione): la soluzione non segnala più lo stato di integrità.
* Segnalazione non inviata (grigio): la soluzione non ha ancora inviato alcuna segnalazione. Lo stato di una soluzione può non essere segnalato se la soluzione è stata connessa recentemente ed è ancora in fase di distribuzione oppure se non sono disponibili dati relativi all'integrità.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati relativi all'integrità e non sono stati ricevuti avvisi negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non invia segnalazioni.
>
>

1. Selezionare **VISUALIZZA** per altre informazioni e opzioni, tra cui:

   - **Console della soluzione**. Apre l'esperienza di gestione per questa soluzione.
   - **Collega la macchina virtuale**. Apre il pannello Collega applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
   - **Elimina soluzione**.
   - **Configura**.

   ![Dettagli della soluzione di un partner](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluzioni individuate

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure che non sono tuttavia connesse al Centro sicurezza e visualizza le soluzioni nella sezione **Soluzioni individuate**. Sono incluse le soluzioni di Azure, ad esempio [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), e le soluzioni dei partner.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

Selezionare **CONNETTI** in una soluzione per integrarla con il Centro sicurezza e ricevere avvisi di sicurezza.

![Soluzioni individuate](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Aggiungi origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Esportazione dei dati in un sistema di informazioni di sicurezza e gestione degli eventi

Gli eventi elaborati prodotti dal Centro sicurezza di Azure vengono pubblicati nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) di Azure, uno dei tipi di log disponibili tramite Monitoraggio di Azure. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Questa operazione viene eseguita trasmettendo i dati in un hub eventi in cui è possibile effettuarne il pull in uno strumento di partner.

Questa pipe usa la [singola pipeline di Monitoraggio di Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) per l'accesso ai dati di monitoraggio dall'ambiente Azure. Questo consente di configurare facilmente i SIEM e di monitorare gli strumenti per utilizzare i dati.

Le sezioni seguenti spiegano come configurare i dati per poterli trasmettere a un hub eventi. In questi passaggi si presuppone che l'utente abbia già configurato Centro sicurezza di Azure nella sottoscrizione Azure.

Panoramica generale

![Panoramica generale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quali dati della sicurezza di Azure vengono esposti a SIEM?

In questa versione vengono esposti gli [avvisi di sicurezza](../security-center/security-center-managing-and-responding-alerts.md). Nelle prossime versioni il set di dati verrà ampliato con le raccomandazioni sulla sicurezza.

### <a name="how-to-setup-the-pipeline"></a>Come configurare la pipeline

#### <a name="create-an-event-hub"></a>Creare un hub eventi

Prima di iniziare, è necessario [creare uno spazio dei nomi di Hub eventi](../event-hubs/event-hubs-create.md). Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi

Fare riferimento all'articolo [Trasmettere il log attività a Hub eventi](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Installare un connettore SIEM partner 

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner.

Fare riferimento al collegamento seguente per vedere l'elenco di [SIEM supportati](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Esempio di query sui dati 

Di seguito sono riportati due query Splunk che è possibile usare per eseguire il pull dei dati di avviso:

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
