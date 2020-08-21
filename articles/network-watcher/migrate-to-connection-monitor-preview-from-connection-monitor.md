---
title: Esegui migrazione a monitoraggio connessione (anteprima) da monitoraggio connessione
titleSuffix: Azure Network Watcher
description: Informazioni su come eseguire la migrazione a monitoraggio connessione (anteprima) da monitoraggio connessione.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701843"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Esegui migrazione a monitoraggio connessione (anteprima) da monitoraggio connessione

È possibile eseguire la migrazione dei monitoraggi connessione esistenti al monitoraggio della connessione nuovo e migliorato (anteprima) con un solo clic e senza tempi di inattività. Per ulteriori informazioni sui vantaggi, è possibile leggere [monitoraggio connessione (anteprima)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Punti chiave da notare

* Gli agenti e le impostazioni del firewall funzioneranno così come sono (nessun tocco necessario) 
* Verrà eseguito il mapping di monitoraggi connessione esistenti a monitoraggio connessione (anteprima)-> gruppo di test-> formato di test. Gli utenti possono fare clic su *modifica* per visualizzare e modificare le proprietà del nuovo monitoraggio della connessione e scaricare il modello per apportare modifiche al monitoraggio della connessione e inviarlo tramite Azure Resource Manager. 
* Le macchine virtuali di Azure con estensione Network Watcher inviano dati all'area di lavoro e alle metriche. I monitoraggi connessione rendono disponibili i dati tramite le nuove metriche (ChecksFailedPercent (anteprima) e RoundTripTimeMs (anteprima)) anziché le metriche di arresto obsolete (ProbesFailedPercent e AverageRoundtripMs) 
* Dati di monitoraggio
    * Avvisi: verrà eseguita la migrazione alle nuove metriche come parte della migrazione
    * Dashboard e integrazioni: sarà necessario modificare manualmente il set di metriche. 
    
## <a name="prerequisites"></a>Prerequisiti

Se si usa un'area di lavoro personalizzata, assicurarsi che Network Watcher sia abilitata nella sottoscrizione e nell'area dell'area di lavoro Log Analytics 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Passaggi per la migrazione da monitoraggio connessione a monitoraggio connessione (anteprima)

1. Fare clic su "monitoraggio connessione", passare a "Esegui migrazione monitoraggi connessione" per eseguire la migrazione dei monitoraggi connessione dalla soluzione precedente a quella più recente.

    ![Screenshot che mostra la migrazione dei monitoraggi connessione all'anteprima di monitoraggio connessione](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selezionare monitoraggi sottoscrizione e connessione e fare clic su "migra selezionato". Con un clic, Esegui la migrazione dei monitoraggi connessione esistenti al monitoraggio connessione (anteprima) 
1. È possibile personalizzare le proprietà di monitoraggio della connessione, modificare l'area di lavoro predefinita, scaricare il modello e controllare lo stato della migrazione. 
1. Una volta avviata la migrazione, si verificano le modifiche seguenti: 
    1. Azure Resource Manager modifiche delle risorse al monitoraggio della connessione più recente
        1. Il nome, l'area e la sottoscrizione del monitoraggio della connessione rimangono invariati. Di conseguenza, l'ID risorsa non ha alcun effetto.
        1. A meno che non sia personalizzato, viene creata un'area di lavoro predefinita Log Analytics nell'area e nella sottoscrizione del monitoraggio della connessione. Questa area di lavoro è la posizione in cui verranno archiviati i dati di monitoraggio. I dati dei risultati del test verranno archiviati anche nelle metriche.
        1. Ogni test viene migrato a un gruppo di test denominato * defaultTestGroup *
        1.  Gli endpoint di origine e di destinazione vengono creati e usati nel gruppo di test creato. I nomi predefiniti sono *defaultSourceEndpoint* e *defaultDestinationEndpoint*
        1. La porta di destinazione e l'intervallo di sondaggio vengono spostati nella configurazione di test denominata *configurazionetestpredefinita*. In base ai valori di porta, viene impostato il protocollo. Le soglie di esito positivo e altre proprietà facoltative vengono lasciate vuote.
    1. Gli avvisi delle metriche vengono migrati agli avvisi delle metriche di monitoraggio connessione (anteprima). Le metriche sono diverse <link to metric section in the doc> , quindi la modifica
    1. I monitoraggi della connessione migrati non verranno visualizzati nella soluzione di monitoraggio della connessione precedente, ora saranno disponibili per l'utilizzo solo in monitoraggio connessione (anteprima)
    1. Eventuali integrazioni esterne come i dashboard in Power BI, Grafana, integrazioni con i sistemi SIEM, dovranno essere migrate direttamente dall'utente. Questo è l'unico passaggio manuale che l'utente deve eseguire per eseguire la migrazione della propria configurazione.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come eseguire la migrazione da monitoraggio prestazioni rete a monitoraggio connessione (anteprima)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Informazioni [su come creare un monitoraggio connessione (anteprima) utilizzando portale di Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
