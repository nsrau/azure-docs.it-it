---
title: Esegui migrazione a monitoraggio connessione da monitoraggio connessione
titleSuffix: Azure Network Watcher
description: Informazioni su come eseguire la migrazione a monitoraggio connessione da monitoraggio connessione.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: eed68ff7e6b002e7a5985b336686b646a1a9acee
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522952"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Esegui migrazione a monitoraggio connessione da monitoraggio connessione (versione classica)

È possibile eseguire la migrazione dei monitoraggi connessione esistenti a un nuovo monitoraggio della connessione migliorato con pochi clic e senza tempi di inattività. Per ulteriori informazioni sui vantaggi, vedere [Connection Monitor](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Punti chiave da notare

La migrazione contribuisce a produrre i risultati seguenti:

* Gli agenti e le impostazioni del firewall funzionano così come sono. Non sono necessarie modifiche. 
* I monitoraggi connessioni esistenti vengono mappati a monitoraggio connessione > gruppo di test > formato di test. Se si seleziona **modifica**, è possibile visualizzare e modificare le proprietà del nuovo monitoraggio della connessione, scaricare un modello per apportare modifiche al monitoraggio della connessione e inviarlo tramite Azure Resource Manager. 
* Le macchine virtuali di Azure con l'estensione Network Watcher inviano i dati all'area di lavoro e alle metriche. Il monitoraggio della connessione rende disponibili i dati tramite le nuove metriche (ChecksFailedPercent e RoundTripTimeMs) anziché le metriche precedenti (ProbesFailedPercent e AverageRoundtripMs). 
* Monitoraggio dei dati:
   * **Avvisi**: la migrazione viene eseguita automaticamente nelle nuove metriche.
   * **Dashboard e integrazioni**: è necessario modificare manualmente il set di metriche. 
    
## <a name="prerequisites"></a>Prerequisiti

Se si usa un'area di lavoro personalizzata, assicurarsi che Network Watcher sia abilitata nella sottoscrizione e nell'area dell'area di lavoro Log Analytics. 

## <a name="migrate-the-connection-monitors"></a>Eseguire la migrazione dei monitoraggi connessione

1. Per eseguire la migrazione dei monitoraggi della connessione precedenti a quello più recente, selezionare **monitoraggio connessione**, quindi selezionare **Esegui migrazione dei monitoraggi connessione**.

    ![Screenshot che mostra la migrazione dei monitoraggi connessione a monitoraggio connessione.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selezionare la sottoscrizione e i monitoraggi di connessione di cui si vuole eseguire la migrazione, quindi selezionare **migrate selected**. 

Con pochi clic, è stata eseguita la migrazione dei monitoraggi connessione esistenti a monitoraggio connessione. 

È ora possibile personalizzare le proprietà di monitoraggio della connessione, modificare l'area di lavoro predefinita, scaricare i modelli e verificare lo stato della migrazione. 

Dopo l'inizio della migrazione, vengono applicate le modifiche seguenti: 
* Il Azure Resource Manager risorsa viene modificato nel monitoraggio della connessione più recente.
    * Il nome, l'area e la sottoscrizione del monitoraggio della connessione rimangono invariati. L'ID risorsa non è interessato.
    * A meno che il monitoraggio della connessione non sia personalizzato, viene creata un'area di lavoro predefinita Log Analytics nella sottoscrizione e nell'area del monitoraggio della connessione. Questa area di lavoro è la posizione in cui vengono archiviati i dati di monitoraggio. I dati dei risultati del test vengono archiviati anche nelle metriche.
    * Ogni test viene migrato a un gruppo di test denominato *defaultTestGroup*.
    * Gli endpoint di origine e di destinazione vengono creati e usati nel nuovo gruppo di test. I nomi predefiniti sono *defaultSourceEndpoint* e *defaultDestinationEndpoint*.
    * La porta di destinazione e l'intervallo di sondaggio vengono spostati in una configurazione di test denominata *configurazionetestpredefinita*. Il protocollo viene impostato in base ai valori di porta. Le soglie di esito positivo e altre proprietà facoltative vengono lasciate vuote.
* Viene eseguita la migrazione degli avvisi di metrica agli avvisi relativi alle metriche di monitoraggio della connessione. Le metriche sono diverse, quindi la modifica. Per ulteriori informazioni, vedere [monitoraggio della connettività di rete con monitoraggio connessione](./connection-monitor-overview.md#metrics-in-azure-monitor).
* I monitoraggi della connessione migrati non vengono più visualizzati come soluzione di monitoraggio della connessione precedente. Sono ora disponibili per l'uso solo in monitoraggio connessione.
* È necessario eseguire manualmente la migrazione di tutte le integrazioni esterne, ad esempio i dashboard in Power BI e Grafana, nonché le integrazioni con sistemi di gestione delle informazioni e degli eventi di sicurezza (SIEM). Questo è l'unico passaggio manuale che è necessario eseguire per eseguire la migrazione dell'installazione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su monitoraggio connessione, vedere:
* [Eseguire la migrazione da Monitoraggio prestazioni rete a monitoraggio connessione](/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor)
* [Creazione di un monitoraggio della connessione tramite il portale di Azure](./connection-monitor-create-using-portal.md)