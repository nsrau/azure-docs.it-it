---
title: Gestire Protezione DDoS di Azure Standard nel portale di Azure | Microsoft Docs
description: Informazioni su come usare la telemetria di Protezione DDoS di Azure Standard in Monitoraggio di Azure per mitigare un attacco.
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2b55341a9d5107376557147913f4fb6cb3966bae
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gestire Protezione DDoS di Azure Standard nel portale di Azure

>[!IMPORTANT]
>Protezione DDoS di Azure Standard (Protezione DDoS) è attualmente in versione di anteprima. Il servizio Protezione DDoS è supportato da un numero limitato di risorse di Azure e solo in aree specifiche. Per abilitare Protezione DDoS per la sottoscrizione, è necessario [eseguire la registrazione per il servizio](http://aka.ms/ddosprotection) durante la fase di anteprima limitata. Dopo la registrazione, si verrà contattati dal team di Protezione DDoS di Azure che fornirà supporto per il processo di abilitazione. Protezione DDoS è disponibile nelle aree Stati Uniti orientali, Stati Uniti occidentali e Stati Uniti centrali. Durante la fase di anteprima non verrà addebitato alcun costo per l'uso del servizio.

Questo articolo illustra come usare il portale di Azure per abilitare Protezione DDoS, disabilitare Protezione DDoS e usare la telemetria per mitigare un attacco. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="enable-ddos-protection"></a>Abilitare Protezione DDoS

Abilitare Protezione DDoS in una rete virtuale nuova o esistente.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Creare una nuova rete virtuale e abilitare Protezione DDoS

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. Immettere le informazioni relative alla rete virtuale. In *Protezione DDoS* fare clic su **Abilitato** e quindi su **Crea**.

    ![Creare una rete virtuale](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

Un messaggio di avviso indica che l'abilitazione di Protezione DDoS comporta addebiti. Durante la fase di anteprima non viene addebitato alcun costo per Protezione DDoS. Gli addebiti vengono applicati con la disponibilità generale del servizio e i clienti riceveranno un preavviso di 30 giorni prima dell'avvio di questa fase con il conseguente addebito dei costi.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Abilitare Protezione DDoS in una rete virtuale esistente 

1. Fare clic su **Reti virtuali** nel menu del portale di Azure e quindi selezionare la rete virtuale.
2. Fare clic su **Protezione DDoS**, fare clic su **Abilitato** nella schermata *Protezione DDoS* e quindi fare clic su **Salva**. 

Un messaggio di avviso indica che l'abilitazione di Protezione DDoS comporta addebiti. Durante la fase di anteprima non viene addebitato alcun costo per Protezione DDoS. Gli addebiti vengono applicati con la disponibilità generale del servizio e i clienti riceveranno un preavviso di 30 giorni prima dell'avvio di questa fase con il conseguente addebito dei costi.

## <a name="disable-ddos-protection"></a>Disabilitare Protezione DDoS

1. Fare clic su **Reti virtuali** nel menu del portale di Azure e quindi selezionare la rete virtuale.
2. Fare clic su **Protezione DDoS**, fare clic su **Disabilitato** nella schermata *Protezione DDoS* e quindi fare clic su **Salva**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurare avvisi per le metriche di Protezione DDoS

Sfruttando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di Protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco. Quando vengono soddisfatte le condizioni, si riceve un messaggio di posta elettronica di avviso all'indirizzo specificato.

1. Fare clic su **Monitoraggio** e quindi su **Metriche**.
2. Nella schermata *Metriche* selezionare il gruppo di risorse, il tipo di risorsa **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure.
3. Per configurare un avviso di posta elettronica per una metrica, fare clic su **Fare clic per aggiungere un avviso**. È possibile creare un avviso di posta elettronica per qualsiasi metrica, ma la metrica più usata è **Under DDoS attack or not** (Attacco DDoS in corso). Si tratta di un valore booleano 1 o 0. **1** indica che è in corso un attacco. **0** indica che non è in corso un attacco.
4. Per ricevere un messaggio di posta elettronica in caso di attacco, impostare la metrica per **Under DDoS attack or not** (Attacco DDoS in corso) e **Condition to Greater than zero (0) over the last 5 minutes** (Condizione maggiore di zero (0) negli ultimi 5 minuti). È possibile configurare avvisi simili per altre metriche.

    ![Configurare le metriche](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Entro pochi minuti dal rilevamento degli attacchi, si riceve una notifica in base alle metriche di Monitoraggio di Azure.

    ![Avviso di attacco](./media/ddos-protection-manage-portal/ddos-alert.png) 

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e alle [app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) per la creazione di avvisi.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurare la registrazione per le metriche di Protezione DDoS

1. Fare clic su **Monitoraggio** e quindi su **Impostazioni di diagnostica**.
2. Nella schermata *Metriche* selezionare il gruppo di risorse, il tipo di risorsa **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure.
3. Fare clic su **Turn on diagnostics to collect the following data** (Attiva diagnostica per raccogliere i dati seguenti).

Per la registrazione sono disponibili tre opzioni:

- **Archivia in un account di archiviazione**: scrive i log in un account di archiviazione.
- **Streaming in un hub eventi**: consente a un ricevitore di log di prelevare i log usando un hub eventi. Ciò consente l'integrazione con Splunk o altri sistemi SIEM.
- **Invia a Log Analytics**: scrive i log nel servizio Azure OMS Log Analytics.

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di Protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

1. Fare clic su **Monitoraggio** e quindi su **Metriche**. 
2. Nella schermata *Metriche* selezionare il gruppo di risorse, il tipo di risorsa **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure. A sinistra dello schermo viene visualizzata una serie di metriche disponibili. Queste metriche, quando selezionate, vengono rappresentate nel grafico delle metriche di Monitoraggio di Azure nella schermata di panoramica. 

I nomi delle metriche si riferiscono a diversi tipi di pacchetto e byte rispetto ai pacchetti, con una struttura di base correlata ai nomi di tag per ogni metrica, come indicato di seguito:

- **Dropped tag name (e.g. Inbound Packets Dropped DDoS)** (Nome tag eliminato - ad esempio pacchetti in ingresso eliminati da DDoS): numero di pacchetti eliminati/sottoposti a scrubbing dal sistema di Protezione DDoS.
- **Forwarded tag name (e.g: Inbound Packets Forwarded DDoS)** (Nome tag inoltrato - ad esempio pacchetti in ingresso inoltrati da DDoS): numero di pacchetti inoltrati dal sistema DDoS all'indirizzo VIP di destinazione (il traffico non è stato filtrato).
- **No tag name (e.g: Inbound Packets DDoS)** (Nessun nome di tag - ad esempio pacchetti in ingresso elaborati da DDoS): numero totale di pacchetti arrivati nel sistema di scrubbing, che rappresenta la somma dei pacchetti eliminati e di quelli inoltrati.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
