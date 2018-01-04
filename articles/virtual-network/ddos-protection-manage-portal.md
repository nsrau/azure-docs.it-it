---
title: Gestire Protezione DDoS di Azure Standard nel portale di Azure | Microsoft Docs
description: Informazioni su come usare la telemetria di Protezione DDoS di Azure Standard in Monitoraggio di Azure per mitigare un attacco.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 958533079c0e0864ff0e561ad865114ef2a415a8
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gestire Protezione DDoS di Azure Standard nel portale di Azure

Informazioni su come abilitare e disabilitare la protezione dall'attacco Distributed Denial of Service (DDoS) e usare la telemetria per mitigare un attacco DDoS con Protezione DDoS Standard di Azure. Protezione DDoS Standard protegge le risorse di Azure, come le macchine virtuali, i bilanciamenti del carico e i gateway applicazione a cui è stato assegnato un [indirizzo IP pubblico](virtual-network-public-ip-address.md) di Azure. Per altre informazioni su Protezione DDoS Standard e sulle relative funzionalità, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md). 

>[!IMPORTANT]
>Protezione DDoS di Azure Standard (Protezione DDoS) è attualmente in versione di anteprima. Il servizio Protezione DDoS è supportato da un numero limitato di risorse di Azure ed è disponibile solo in aree specifiche. Per un elenco delle aree disponibili, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md). Per abilitare Protezione DDoS per la sottoscrizione, è necessario [eseguire la registrazione per il servizio](http://aka.ms/ddosprotection) durante la fase di anteprima limitata. Dopo la registrazione, si verrà contattati dal team di Protezione DDoS di Azure che guiderà l'utente nel processo di abilitazione. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Abilitare Protezione DDoS Standard - Nuova rete virtuale

1. Accedere al portale di Azure all'indirizzo http://portal.azure.com. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
2. Fare clic su **creare una risorsa** nell'angolo superiore sinistro del portale di Azure.
3. Selezionare **Rete** e quindi **Rete virtuale**.
4. Creare una rete virtuale con le impostazioni scelte. Per altre informazioni sulle reti virtuali, vedere [Creare una rete virtuale](virtual-networks-create-vnet-arm-pportal.md). In **Protezione DDoS** fare clic su **Abilitato** e quindi su **Crea**. Se non viene visualizzato **protezione DDoS**, una causa probabile è che la sottoscrizione non è registrata per la funzionalità. È necessario completare [registrazione](http://aka.ms/ddosprotection)e ricevere una notifica che la sottoscrizione è stata abilitata per la funzionalità, prima di **protezione DDoS** viene visualizzato.

    ![Creare una rete virtuale](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > Quando si seleziona un'area, scegliere un'area supportata dall'elenco in [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md). Se non si seleziona un'area non supportata, creazione della rete virtuale ha esito negativo.

    Un messaggio di avviso indica che l'abilitazione di Protezione DDoS comporta addebiti. Durante la fase di anteprima non viene addebitato alcun costo per Protezione DDoS. Gli addebiti vengono applicati con la disponibilità generale del servizio. I clienti riceveranno un preavviso di 30 giorni prima dell'avvio di questa fase con il conseguente addebito dei costi.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Abilitare Protezione DDoS Standard - Rete virtuale esistente 

1. Fare clic su **Reti virtuali** nel menu del portale di Azure e quindi selezionare la rete virtuale.
2. Fare clic su **Protezione DDoS**, fare clic su **Abilitato** nella schermata *Protezione DDoS* e quindi fare clic su **Salva**. Se non viene visualizzato **protezione DDoS**, una causa probabile è che la sottoscrizione non è registrata per la funzionalità. È necessario completare [registrazione](http://aka.ms/ddosprotection)e ricevere una notifica che la sottoscrizione è stata abilitata per la funzionalità, prima di **protezione DDoS** viene visualizzato. 

    > [!WARNING]
    > La rete virtuale deve essere presente in un'area supportata. Per un elenco delle aree supportate, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md).

    Un messaggio di avviso indica che l'abilitazione di Protezione DDoS comporta addebiti. Durante la fase di anteprima non viene addebitato alcun costo per Protezione DDoS. Gli addebiti vengono applicati con la disponibilità generale del servizio. I clienti riceveranno un preavviso di 30 giorni prima dell'avvio di questa fase con il conseguente addebito dei costi.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Disabilitare Protezione DDoS in una rete virtuale

1. Fare clic su **Reti virtuali** nel menu del portale di Azure e quindi selezionare la rete virtuale.
2. Fare clic su **Protezione DDoS**, fare clic su **Disabilitato** nella schermata *Protezione DDoS* e quindi fare clic su **Salva**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurare avvisi per le metriche di Protezione DDoS

Usando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di Protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco. Quando vengono soddisfatte le condizioni, si riceve un messaggio di posta elettronica di avviso all'indirizzo specificato.

1. Fare clic su **Monitoraggio** e quindi su **Metriche**.
2. Nella schermata *Metriche* selezionare il gruppo di risorse, il tipo di risorsa di **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure.
3. Per configurare un avviso di posta elettronica per una metrica, fare clic su **Aggiungi avviso per la metrica**. È possibile creare un avviso di posta elettronica per qualsiasi metrica, ma la metrica più usata è **Under DDoS attack or not** (Attacco DDoS in corso). Si tratta di un valore booleano 1 o 0. **1** indica che è in corso un attacco. **0** indica che non è in corso un attacco.
4. Per ricevere un messaggio di posta elettronica in caso di attacco, impostare la metrica per **Under DDoS attack or not** (Attacco DDoS in corso) e **Condition to Greater than zero (0) over the last 5 minutes** (Condizione maggiore di zero (0) negli ultimi 5 minuti). È possibile configurare avvisi simili per altre metriche.

    ![Configurare le metriche](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Entro pochi minuti dal rilevamento degli attacchi, si riceve una notifica in base alle metriche di Monitoraggio di Azure.

    ![Avviso di attacco](./media/ddos-protection-manage-portal/ddos-alert.png) 

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e alle [app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) per la creazione di avvisi.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Configurare la registrazione per le metriche di Protezione DDoS Standard

1. Fare clic su **Monitoraggio** e quindi su **Impostazioni di diagnostica**.
2. Nella schermata *Metriche* selezionare il gruppo di risorse, il tipo di risorsa di **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure.
3. Fare clic su **Turn on diagnostics to collect the following data** (Attiva diagnostica per raccogliere i dati seguenti).

Per la registrazione sono disponibili tre opzioni:

- **Archivia in un account di archiviazione**: scrive i log in un account di archiviazione.
- **Streaming in un hub eventi**: consente a un ricevitore di log di prelevare i log usando un hub eventi. Ciò consente l'integrazione con Splunk o altri sistemi SIEM.
- **Invia a Log Analytics**: scrive i log nel servizio Azure OMS Log Analytics.

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di Protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

1. Fare clic su **Monitoraggio** e quindi su **Metriche**. 
2. Nella schermata *Metriche* selezionare il gruppo di risorse, il tipo di risorsa di **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure. A sinistra dello schermo viene visualizzata una serie di **metriche disponibili**. Queste metriche, quando selezionate, vengono rappresentate nel **grafo delle metriche di Monitoraggio di Azure** nella schermata di panoramica. 

I nomi delle metriche si riferiscono a diversi tipi di pacchetto e byte rispetto ai pacchetti, con una struttura di base correlata ai nomi di tag per ogni metrica, come indicato di seguito:

- **Dropped tag name (e.g. Inbound Packets Dropped DDoS)** (Nome tag eliminato - ad esempio pacchetti in ingresso eliminati da DDoS): numero di pacchetti eliminati/sottoposti a scrubbing dal sistema di Protezione DDoS.
- **Forwarded tag name (e.g: Inbound Packets Forwarded DDoS)** (Nome tag inoltrato - ad esempio pacchetti in ingresso inoltrati da DDoS): numero di pacchetti inoltrati dal sistema DDoS all'indirizzo VIP di destinazione (il traffico non è stato filtrato).
- **No tag name (e.g: Inbound Packets DDoS)** (Nessun nome di tag - ad esempio pacchetti in ingresso elaborati da DDoS): numero totale di pacchetti arrivati nel sistema di scrubbing, che rappresenta la somma dei pacchetti eliminati e di quelli inoltrati.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)