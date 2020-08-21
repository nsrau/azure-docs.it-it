---
title: Esegui la migrazione a monitoraggio connessione (anteprima) da Monitoraggio prestazioni rete
titleSuffix: Azure Network Watcher
description: Informazioni su come eseguire la migrazione a monitoraggio connessione (anteprima) da Monitoraggio prestazioni rete.
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701832"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Esegui la migrazione a monitoraggio connessione (anteprima) da Monitoraggio prestazioni rete

È possibile eseguire la migrazione dei test da Monitoraggio prestazioni rete al monitoraggio della connessione nuovo e migliorato (anteprima) con un solo clic e senza tempi di inattività. Per ulteriori informazioni sui vantaggi, è possibile leggere [monitoraggio connessione (anteprima)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> È possibile eseguire la migrazione solo dei test da monitoraggio connettività servizio a monitoraggio connessione (anteprima).
>

## <a name="key-points-to-note"></a>Punti chiave da notare

* Gli agenti locali e le impostazioni del firewall funzioneranno così come sono. Non sono necessarie modifiche. Gli agenti di Log Analytics installati nelle macchine virtuali di Azure devono essere sostituiti con Network Watcher estensione
* I test esistenti verranno mappati a Connection Monitor (anteprima)-> gruppo di test-> formato di test. Gli utenti possono fare clic su *modifica* per visualizzare e modificare le proprietà del nuovo monitoraggio della connessione e scaricare il modello per apportare modifiche al monitoraggio della connessione e inviarlo tramite Azure Resource Manager.
* Gli agenti inviano i dati all'area di lavoro di Log Analytics e alle metriche.
* Dati di monitoraggio
    * Dati in Log Analytics: tutti i dati pre-migrazione continuano a trovarsi nell'area di lavoro in cui NPM è configurato nella tabella NetworkMonitoring. Dopo la migrazione, i dati passano alla tabella NetworkMonitoring e ConnectionMonitor_CL tabella nella stessa area di lavoro. Una volta che i test sono stati disabilitati da NPM, i dati verranno archiviati solo in ConnectionMonitor_CL tabella
    * Avvisi basati su log, dashboard e integrazioni: sarà necessario modificare manualmente le query basate sulla nuova tabella ConnectionMonitor_CL. È anche possibile ricreare gli avvisi nelle metriche usando questo collegamento. La possibilità di eseguire la migrazione degli avvisi basati su log nella tabella NetworkMonitoring a avvisi basati su metriche automaticamente come parte della migrazione sarà presto disponibile
    
## <a name="prerequisites"></a>Prerequisiti

*   Verificare che Network Watcher sia abilitato nella sottoscrizione e nell'area dell'area di lavoro Log Analytics
*   Le macchine virtuali di Azure con gli agenti di log Analytics installate dovranno essere abilitate con Network Watcher estensione

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Passaggi per la migrazione dei test da Monitoraggio prestazioni rete a monitoraggio connessione (anteprima)

1.  Fare clic su "monitoraggio connessione", passare a "migrare test da NPM" per eseguire la migrazione di test a Connection Monitor (anteprima)

    ![Screenshot che mostra la migrazione dei test da NPM a Connection Monitor Preview](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Selezionare sottoscrizione, area di lavoro e la funzionalità NPM di cui si vuole eseguire la migrazione. Attualmente è possibile eseguire la migrazione solo dei test dal monitoraggio della connettività del servizio.  
1.  Fare clic su "Importa" per eseguire la migrazione dei test
1.  Una volta avviata la migrazione, si verificano le modifiche seguenti: 
    1. Viene creata una nuova risorsa di monitoraggio della connessione
        1. Viene creato un monitoraggio della connessione per area e sottoscrizione. Per i test con agenti locali, il nome del nuovo monitoraggio della connessione è nel formato <workspaceName> _"locale". Per i test con agenti di Azure, il nome del nuovo monitoraggio della connessione <workspaceName> è nel formato_<Azure_region_name>
        1. I dati di monitoraggio vengono ora archiviati nella stessa area di lavoro Log Analytics in cui è abilitato NPM, in una nuova tabella denominata Connectionmonitor_CL tabella. 
        1. Il nome del test viene portato avanti al nome del gruppo di test. Non verrà eseguita la migrazione della descrizione del test.
        1. Gli endpoint di origine e di destinazione vengono creati e usati nel gruppo di test creato. Per gli agenti locali, gli endpoint vengono denominati nel formato <workspaceName> _"endpoint"_ <FQDN of on-premises machine> . Per Azure, se i test di migrazione contengono agenti non sono in esecuzione, sarà necessario abilitare gli agenti ed eseguire di nuovo la migrazione.
        1. La porta di destinazione e l'intervallo di sondaggio vengono spostati nella configurazione di test denominata "TC"_ <testname> "e" TC "_ <testname> _" AppThresholds ". In base ai valori di porta, viene impostato il protocollo. Le soglie di esito positivo e altre proprietà facoltative vengono lasciate vuote.
    1. NPM non è disabilitato. Di conseguenza, i test migrati continuano a inviare dati alla tabella NetworkMonitoring, oltre che ConnectionMonitor_CL tabella. Questo passaggio assicura che gli avvisi e le integrazioni esistenti basati su log non siano interessati. La migrazione automatica degli avvisi basati su log nella tabella NetworkMonitoring agli avvisi basati su metriche come parte della migrazione sarà presto disponibile.
    1. Il monitoraggio connessione appena creato sarà visibile nel monitoraggio connessione (anteprima)
1.  Dopo la migrazione, è necessario disabilitare manualmente i test in NPM. Fino a quando non si esegue questa operazione, si continuerà a pagare per lo stesso. Quando si disabilita NPM, assicurarsi di ricreare gli avvisi in ConnectionMonitor_CL tabella o usare le metriche. Assicurarsi inoltre che sia necessario eseguire la migrazione di tutte le integrazioni esterne come i dashboard in Power BI, Grafana, integrazioni con i sistemi SIEM alla tabella ConnectionMonitor_CL


## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come eseguire la migrazione da monitoraggio connessione a monitoraggio connessione (anteprima)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Informazioni [su come creare un monitoraggio connessione (anteprima) utilizzando portale di Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
