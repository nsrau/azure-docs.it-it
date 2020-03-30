---
title: Connettere i dati DNS in Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati DNS in Azure Sentinel.Learn how to connect DNS data in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588315"
---
# <a name="connect-your-domain-name-server"></a>Connettere il server dei nomi di dominio

> [!IMPORTANT]
> Il connettore dati DNS in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettere qualsiasi Domain Name Server (DNS) in esecuzione in Windows ad Azure Sentinel.You can connect any Domain Name Server (DNS) running on Windows to Azure Sentinel. Questa operazione viene eseguita installando un agente nel computer DNS. Utilizzando i registri DNS, è possibile ottenere informazioni dettagliate relative a sicurezza, prestazioni e operazioni nell'infrastruttura DNS dell'organizzazione raccogliendo, analizzando e correlando i log analitici e di controllo e altri dati correlati dai server DNS.

Quando si abilita la connessione al registro DNS, è possibile:
- Identificare i client che tentano di risolvere nomi di dominio dannosi
- Identificare i record di risorse non aggiornati
- Identificare i nomi di dominio sottoposti frequentemente a query e i client DNS loquaci
- Visualizzare il carico di richieste nei server DNS
- Visualizzare gli errori di registrazione di DNS dinamici

## <a name="connected-sources"></a>Origini connesse

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione:

| **Sorgente connessa** | **Supporto** | **Descrizione** |
| --- | --- | --- |
| [Agenti di Windows](../azure-monitor/platform/agent-windows.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti Windows. |
| [Agenti Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | No | La soluzione non raccoglie le informazioni DNS dagli agenti Linux diretti. |
| [Gruppo di gestione di System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti di un gruppo di gestione di Operations Manager connesso. Non è necessaria una connessione diretta dall'agente Operations Manager a Monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |
| [Account di archiviazione di AzureAzure storage account](../azure-monitor/platform/collect-azure-metrics-logs.md) | No | La soluzione non usa le risorse di archiviazione di Azure. |

### <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati

La soluzione raccoglie i dati relativi all'inventario e agli eventi DNS dai server DNS in cui è installato un agente Log Analytics. I dati relativi all'inventario, come il numero di server, zone e record di risorse DNS, vengono raccolti eseguendo i cmdlet di PowerShell per DNS. I dati vengono aggiornati ogni due giorni. I dati relativi agli eventi vengono raccolti quasi in tempo reale dai [log di analisi e di controllo](https://technet.microsoft.com/library/dn800669.aspx#enhanc) offerti dalle funzionalità avanzate di registrazione e diagnostica DNS di Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Connettere l'appliance DNS

1. Nel portale di Azure Sentinel selezionare **Connettori dati** e scegliere il riquadro **DNS (anteprima).**
1. Se le macchine DNS sono in Azure:If your DNS machines are in Azure:
    1. Fare clic su **Installa agente nella macchina virtuale Windows di Azure.**
    1. Nell'elenco **Macchine virtuali** selezionare la macchina DNS che si vuole trasmettere in Azure Sentinel. Assicurarsi che si tratti di una macchina virtuale Windows.Make sure This is a Windows VM.
    1. Nella finestra visualizzata per la macchina virtuale fare clic su **Connetti**.  
    1. Fare clic su **Abilita** nella finestra del **connettore DNS.** 

2. Se il computer DNS non è una macchina virtuale di Azure:If your DNS machine is not an Azure VM:
    1. Fare clic su **Installa agente in computer non Azure**.
    1. Nella finestra **Agente diretto** selezionare Scarica agente **Windows (64 bit)** o **Scarica agente Windows (32 bit)**.
    1. Installare l'agente nel computer DNS. Copiare **l'ID area di lavoro,** la chiave **primaria**e la **chiave secondaria** e utilizzarli quando richiesto durante l'installazione.

3. Per utilizzare lo schema pertinente in Log Analytics per i registri DNS, cercare **DnsEvents**.

## <a name="validate"></a>Convalida 

In Log Analytics cercare lo schema **DnsEvents** e assicurarsi che siano presenti eventi.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se le query di ricerca non vengono visualizzate in Azure Sentinel, seguire questi passaggi in modo che le query vengano visualizzate correttamente:If Lookup Queries do not show up in Azure Sentinel, follow these steps so the queries are displayed properly:
1. Attivare i [registri di Analisi DNS sui server.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))
2. Assicurarsi che DNSEvents venga visualizzato nell'elenco di raccolta di Log Analytics.
3. Attivare [Analisi DNS](../azure-monitor/insights/dns-analytics.md)di Azure .
4. In Analisi DNS di Azure, in **Configurazione**, modificare le impostazioni, salvarla, quindi modificarla se necessario e quindi salvarla di nuovo.
5. Controllare Analisi DNS di Azure per assicurarsi che le query vengano ora visualizzate.

## <a name="next-steps"></a>Passaggi successivi

In this document, you learned how to connect DNS on-premises appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
