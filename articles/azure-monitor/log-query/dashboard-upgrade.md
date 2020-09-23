---
title: Aggiornamento delle visualizzazioni del dashboard Log Analytics
description: Informazioni su come aggiornare le visualizzazioni del dashboard Log Analytics con query che possono fornire informazioni avanzate.
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: a029dcbebf6dfe7a2b6cb517641c824a5937ca95
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988246"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Aggiornamento delle visualizzazioni del dashboard Log Analytics

Nel febbraio 2020 è stata introdotta una tecnologia migliorata per la visualizzazione. Migliorare e migliorare la capacità di visualizzare i risultati delle query e raggiungere rapidamente informazioni avanzate. 

Per altre informazioni su questo aggiornamento, vedere questo [aggiornamento di Azure](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Questa nuova tecnologia di visualizzazione sta aprendo la strada per esperienze nuove e migliorate nel set di risultati della query. 

## <a name="dashboards-in-azure"></a>Dashboard in Azure

I dashboard di Azure sono un modo per visualizzare lo stato dell'intera superficie di attacco di Azure. Sono progettati per fornire un unico riquadro di vetro allo stato di Azure estate e consentire un'ampia gamma di scelte rapide per le azioni comuni. 

Per altre informazioni, vedere [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>Aggiornamento di parti del dashboard Log Analytics

La nuova tecnologia di visualizzazione risolve alcuni problemi comuni relativi all'implementazione precedente e introduce alcune nuove funzionalità per l'aggiunta di Log Analytics parti: 

- **Tipi disponibili** : tutti i tipi di visualizzazione disponibili in log Analytics sono disponibili come parti aggiunte nei dashboard.

- **Aspetto coerente** : l'aspetto della visualizzazione per le parti bloccate ora è quasi identico a quello in log Analytics. Le differenze sono dovute a ottimizzazioni che richiedono differenze minime nel contenuto dei dati dell'oggetto visivo. Per informazioni più dettagliate su queste differenze, vedere la sezione Considerazioni di questo documento.

- **Descrizioni comandi ed etichette: le** nuove visualizzazioni log Analytics aggiunte supportano le descrizioni comandi. I grafici a torta e ad anello ora supportano le etichette.

- Legende **interattive** : facendo clic sulla legenda della visualizzazione è possibile aggiungere o rimuovere dimensioni dall'oggetto visivo aggiunto come in log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Fase 1: messaggio di aggiornamento esplicito

Quando è possibile aggiornare una parte Log Analytics bloccata, viene visualizzata una nuova notifica di *consenso esplicito* nelle parti log Analytics bloccate nei dashboard che consentono agli utenti di aggiornare la visualizzazione. Se si vogliono sperimentare le nuove visualizzazioni per aggiornare le visualizzazioni selezionate nel dashboard.

 
![Barra laterale](media/dashboard-upgrade/update-message-1.png)
 
![Barra laterale](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Una volta pubblicato il dashboard, l'aggiornamento è irreversibile. Tuttavia, le modifiche vengono ignorate se ci si allontana dal dashboard senza ripubblicarlo.  

Una volta fatto clic, la visualizzazione verrà aggiornata alla nuova tecnologia. È possibile che si verifichino differenze minime nella visualizzazione per allinearsi all'aspetto in Log Analytics.

Dopo l'aggiornamento delle visualizzazioni, è necessario ripubblicare il dashboard per rendere effettive le modifiche.

![Barra laterale](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Fase 2-migrazione di tutti i dashboard

Al termine del periodo di consenso iniziale, il team Log Analytics aggiornerà tutti i dashboard del sistema. L'allineamento di tutti i dashboard di Azure consente al team di introdurre più visualizzazioni e migliorare l'esperienza nella bacheca.

## <a name="considerations"></a>Considerazioni

Log Analytics visualizzazioni aggiunte a un dashboard hanno un comportamento specifico progettato per un'esperienza ottimale. Esaminare le considerazioni di progettazione seguenti quando si aggiunge una visualizzazione a un dashboard.

### <a name="query-time-scope---30-day-limit"></a>Ambito tempo query-limite di 30 giorni

Poiché i dashboard possono contenere più visualizzazioni di più query, l'intervallo temporale per una singola query bloccata è limitato a 30 giorni. Una singola query può essere eseguita solo in un intervallo di tempo minore o uguale a 30 giorni. Questa limitazione consiste nel garantire un tempo di caricamento del dashboard ragionevole.

### <a name="query-data-values---25-values-and-other-grouping"></a>Valori dei dati di query-25 valori e altro raggruppamento

I dashboard possono essere visivamente dense e complesse. Per ridurre il carico cognitivo durante la visualizzazione di un dashboard, è possibile ottimizzare le visualizzazioni limitando la visualizzazione a 25 tipi di dati diversi. Quando sono presenti più di 25 Log Analytics ottimizza i dati. Singolarmente Mostra i 25 tipi con la maggior parte dei dati come separati e quindi raggruppa i valori rimanenti in un valore "altro". Nel grafico seguente viene illustrato un caso di questo tipo.  

![Barra laterale](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Aggiornamento del dashboard al caricamento

I dashboard vengono aggiornati al caricamento. Vengono eseguite tutte le query correlate alle visualizzazioni Log Analytics aggiunte dal dashboard e il dashboard viene aggiornato dopo il caricamento. Se la pagina dashboard rimane aperta, i dati nel dashboard vengono aggiornati ogni 60 minuti.

## <a name="next-steps"></a>Passaggi successivi

[Creare e condividere dashboard in Log Analytics](../learn/tutorial-logs-dashboards.md)
