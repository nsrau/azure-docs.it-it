---
title: Esportazione dei dati di Sicurezza di Azure a SIEM - Configurazione pipeline (anteprima) | Microsoft Docs
description: In questo articolo viene descritto come esportare i log del centro sicurezza di Azure a un SIEM
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: barclayn
ms.openlocfilehash: aef623f047bd7e14cb5bd17fb2a2c18e3c5d42b9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Esportazione dei dati di Sicurezza di Azure a SIEM - Configurazione pipeline (anteprima)

Questo documento descrive la procedura per esportare i dati di sicurezza del Centro sicurezza di Azure a un SIEM.

Gli eventi elaborati prodotti dal Centro sicurezza di Azure vengono pubblicati nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) di Azure, uno dei tipi di log disponibili tramite Monitoraggio di Azure. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Questa operazione viene eseguita trasmettendo i dati in un hub eventi in cui è possibile effettuarne il pull in uno strumento di partner.

Questa pipe usa la [singola pipeline di Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) per l'accesso ai dati di monitoraggio dall'ambiente Azure. Questo consente di configurare facilmente i SIEM e di monitorare gli strumenti per utilizzare i dati.

Le sezioni seguenti spiegano come configurare i dati per poterli trasmettere a un hub eventi. In questi passaggi si presuppone che l'utente abbia già configurato Centro sicurezza di Azure nella sottoscrizione Azure.

Panoramica generale

![Panoramica generale](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quali dati della sicurezza di Azure vengono esposti a SIEM?

In questa versione di anteprima vengono esposti gli [avvisi di sicurezza.](../security-center/security-center-managing-and-responding-alerts.md) Nelle prossime versioni il set di dati verrà ampliato con le raccomandazioni sulla sicurezza.

## <a name="how-to-setup-the-pipeline"></a>Informazioni su come configurare la pipeline 

### <a name="create-an-event-hub"></a>Creare un hub eventi 

Prima di iniziare, è necessario [creare uno spazio dei nomi di Hub eventi](../event-hubs/event-hubs-create.md). Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi

Fare riferimento all'articolo [Trasmettere il log attività a Hub eventi](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Installare un connettore SIEM partner 

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner.

Fare riferimento al collegamento seguente per vedere l'elenco di [SIEM supportati](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Esempio di query sui dati 

Di seguito sono riportati due query Splunk che è possibile usare per eseguire il pull dei dati di avviso:

| **Descrizione della query**                                | **Query**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Tutti gli avvisi                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Riepilogare il numero di operazioni in base al nome             | **Avvisi** index=main sourcetype="amal:security" \| tabella operationName \| conteggio statistiche per operationName                                |
| Ottenere le informazioni sugli avvisi: ora, nome, stato, ID e sottoscrizione | index=main Microsoft.Security/locations/alerts \| tabella \_time, properties.eventName, stato, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passaggi successivi

- [SIEM supportati](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Trasmettere il log attività a Hub eventi](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Avvisi di sicurezza.](../security-center/security-center-managing-and-responding-alerts.md)