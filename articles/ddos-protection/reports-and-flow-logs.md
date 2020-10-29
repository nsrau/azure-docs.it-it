---
title: Report standard di protezione DDoS di Azure e log di flusso
description: Informazioni su come configurare i report e i log dei flussi.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2c4349662b06ff281ef028c833c6c43dd2707051
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905295"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Configurare i report di mitigazione degli attacchi DDoS e i log di flusso 

Lo standard Protezione DDoS di Azure fornisce informazioni dettagliate sugli attacchi e ne consente la visualizzazione con l'analisi degli attacchi DDoS. I clienti che proteggono le reti virtuali da attacchi DDoS hanno visibilità dettagliata sul traffico degli attacchi e sulle azioni intraprese per mitigare l'attacco tramite i report sulla mitigazione dell'attacco e i log del flusso di mitigazione. I dati di telemetria avanzati vengono esposti tramite monitoraggio di Azure, incluse le metriche dettagliate durante la durata di un attacco DDoS. Gli avvisi possono essere configurati per una delle metriche di monitoraggio di Azure esposte dalla protezione DDoS. La registrazione può essere integrata ulteriormente con [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection), Splunk (hub eventi di Azure), OMS log Analytics e archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare e configurare i report di mitigazione degli attacchi DDoS
> * Visualizzare e configurare i log del flusso di mitigazione degli attacchi DDoS

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Visualizzare e configurare i report di mitigazione degli attacchi DDoS

I report sulla mitigazione degli attacchi usano i dati del protocollo Netflow che vengono aggregati per fornire informazioni dettagliate sull'attacco alla risorsa. Ogni volta che una risorsa IP pubblica è sotto attacco, la generazione di report verrà avviata contemporaneamente alla mitigazione. Verranno generati un report incrementale ogni 5 minuti e un report post-mitigazione per l'intero periodo della mitigazione. Lo scopo è quello di assicurare che, qualora l'attacco DDoS prosegua per un periodo di tempo più lungo, sia possibile visualizzare lo snapshot più aggiornato del report sulla mitigazione ogni 5 minuti e un riepilogo completo al termine della mitigazione dell'attacco. 

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro** . Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Impostazioni di diagnostica** in **IMPOSTAZIONI** .
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico che si vuole registrare.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa** , quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono registrare le metriche.
6. Selezionare **Turn on diagnostics to collect the DDoSMitigationReports log** (Attiva diagnostica per raccogliere il log DDoSMitigationReports) e quindi selezionare tutte le opzioni necessarie tra le seguenti:

    - **Archivia in un account di archiviazione** : i dati vengono scritti in un account di archiviazione di Azure. Per altre informazioni su questa opzione, vedere [archiviare i log delle risorse](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streaming in un hub eventi** : consente a un ricevitore di log di prelevare i log usando un Hub eventi di Azure. Gli hub eventi consentono l'integrazione con Splunk o altri sistemi SIEM. Per altre informazioni su questa opzione, vedere [trasmettere i log delle risorse a un hub eventi](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Invia a log Analytics** : scrive i log nel servizio monitoraggio di Azure. Per altre informazioni su questa opzione, vedere [raccogliere i log da usare nei log di monitoraggio di Azure](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sia i report incrementali che quello sulla mitigazione successivo all'attacco includono i campi seguenti
- Attack vectors (Vettori di attacco)
- Traffic statistics (Statistiche sul traffico)
- Reason for dropped packets (Motivo dei pacchetti eliminati)
- Protocols involved (Protocolli interessati)
- Top 10 source countries or regions (Primi 10 paesi o aree di origine)
- Top 10 source ASNs (Primi 10 ASN di origine)

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Visualizzare e configurare i log del flusso di mitigazione degli attacchi DDoS
I log dei flussi di mitigazione degli attacchi consentono di esaminare il traffico eliminato, il traffico trasmesso e altri punti di interesse interessanti durante un attacco DDoS attivo quasi in tempo reale. È possibile inserire il flusso costante di questi dati in Sentinel di Azure o nei sistemi SIEM di terze parti tramite hub eventi per il monitoraggio quasi in tempo reale, intraprendere azioni potenziali e soddisfare le esigenze delle operazioni di difesa.

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro** . Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Impostazioni di diagnostica** in **IMPOSTAZIONI** .
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico che si vuole registrare.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa** , quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono registrare le metriche.
6. Selezionare **Turn on diagnostics to collect the DDoSMitigationFlowLogs log** (Attiva diagnostica per raccogliere il log DDoSMitigationFlowLogs) e quindi selezionare tutte le opzioni necessarie tra le seguenti:

    - **Archivia in un account di archiviazione** : i dati vengono scritti in un account di archiviazione di Azure. Per altre informazioni su questa opzione, vedere [archiviare i log delle risorse](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streaming in un hub eventi** : consente a un ricevitore di log di prelevare i log usando un Hub eventi di Azure. Gli hub eventi consentono l'integrazione con Splunk o altri sistemi SIEM. Per altre informazioni su questa opzione, vedere [trasmettere i log delle risorse a un hub eventi](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Invia a log Analytics** : scrive i log nel servizio monitoraggio di Azure. Per altre informazioni su questa opzione, vedere [raccogliere i log da usare nei log di monitoraggio di Azure](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-sentinel-data-connector"></a>Connettore dati sentinella di Azure

È possibile connettere il ad Azure Sentinel, visualizzare e analizzare i dati nelle cartelle di lavoro, creare avvisi personalizzati e incorporarli nei processi di analisi. Per connettersi ad Azure Sentinel, vedere [connettersi ad Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection). 

![Connettore DDoS di Azure Sentinel](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Cartella di lavoro protezione DDoS di Azure

Per visualizzare i dati dei log del flusso nel dashboard di analisi di Azure, è possibile importare il dashboard di esempio da https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

I log del flusso conterranno i campi seguenti: 
- IP di origine
- IP di destinazione
- Porta di origine 
- Porta di destinazione 
- Protocol type (Tipo di protocollo) 
- Action taken during mitigation (Azione eseguita durante la mitigazione)

![Cartella di lavoro protezione DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

L'analisi degli attacchi funziona solo se protezione DDoS standard è abilitato nella rete virtuale dell'indirizzo IP pubblico. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

- Visualizzare e configurare i report di mitigazione degli attacchi DDoS
- Visualizzare e configurare i log del flusso di mitigazione degli attacchi DDoS

Per informazioni su come testare e simulare un attacco DDoS, vedere la guida ai test di simulazione:

> [!div class="nextstepaction"]
> [Eseguire test tramite simulazioni](test-through-simulations.md)

