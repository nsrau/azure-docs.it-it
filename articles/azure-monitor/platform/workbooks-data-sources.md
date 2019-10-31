---
title: Creare report interattivi con le cartelle di lavoro di monitoraggio di Azure da più origini dati di Azure. Microsoft docs
description: Semplifica la creazione di report complessi con cartelle di lavoro di monitoraggio di Azure con parametri predefinite e personalizzate compilate da più origini dati
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dd5068da31c3aa863fc56022834a28b60ee15004
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166200"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Origini dati delle cartelle di lavoro di monitoraggio di Azure

Le cartelle di lavoro sono compatibili con un numero elevato di origini dati. In questo articolo vengono illustrate le origini dati attualmente disponibili per le cartelle di lavoro di monitoraggio di Azure.

## <a name="logs"></a>Log

Le cartelle di lavoro consentono di eseguire query sui log dalle origini seguenti:

* Log di monitoraggio di Azure (Application Insights risorse e aree di lavoro Log Analytics).
* Dati incentrati sulle risorse (log attività)

Gli autori delle cartelle di lavoro possono utilizzare query KQL che trasformano i dati delle risorse sottostanti per selezionare un set di risultati che può essere visualizzato come testo, grafici o griglie.

![Screenshot dell'interfaccia di report dei log delle cartelle di lavoro](./media/workbooks-overview/logs.png)

Gli autori delle cartelle di lavoro possono eseguire facilmente query tra più risorse creando un'esperienza di creazione di report avanzata.

## <a name="metrics"></a>Metriche

Le risorse di Azure emettono [metriche](data-platform-metrics.md) a cui è possibile accedere tramite le cartelle di lavoro. È possibile accedere alle metriche nelle cartelle di lavoro tramite un controllo specializzato che consente di specificare le risorse di destinazione, le metriche desiderate e la relativa aggregazione. Questi dati possono quindi essere tracciati in grafici o griglie.

![Screenshot dei grafici di metriche delle cartelle di lavoro di utilizzo della CPU](./media/workbooks-overview/metrics-graph.png)

![Screenshot dell'interfaccia della metrica cartella di lavoro](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Diagramma risorse di Azure 

Le cartelle di lavoro supportano l'esecuzione di query per le risorse e i relativi metadati tramite Azure Resource Graph (ARG). Questa funzionalità viene utilizzata principalmente per creare ambiti di query personalizzati per i report. L'ambito della risorsa viene espresso tramite un subset di KQL supportato da ARG, che è spesso sufficiente per i casi d'uso comuni.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa tipo di query per scegliere Azure Resource Graph e selezionare le sottoscrizioni di destinazione. Usare il controllo query per aggiungere il subset di KQL ARG che seleziona un subset di risorse interessante.


![Screenshot della query KQL Graph di risorse di Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Avvisi (anteprima)

Le cartelle di lavoro consentono agli utenti di visualizzare gli avvisi attivi correlati alle relative risorse. Questa funzionalità consente la creazione di report che riuniscono i dati di notifica (avviso) e le informazioni di diagnostica (metriche, log) in un unico report. Queste informazioni possono anche essere unite per creare report avanzati che combinano informazioni dettagliate su tali origini dati.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa tipo di query per scegliere gli avvisi e selezionare le sottoscrizioni, i gruppi di risorse o le risorse di destinazione. Usare gli elenchi a discesa del filtro avvisi per selezionare un subset interessante di avvisi per le proprie esigenze analitiche.

![Screenshot della query degli avvisi](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Stato del carico di lavoro (anteprima)

Monitoraggio di Azure offre funzionalità che monitora in modo proattivo la disponibilità e le prestazioni dei sistemi operativi guest Windows o Linux. Monitoraggio di Azure modella i componenti chiave e le relative relazioni, i criteri per misurare l'integrità di tali componenti e i componenti che inviano un avviso quando viene rilevata una condizione di tipo non integro. Le cartelle di lavoro consentono agli utenti di usare queste informazioni per creare report interattivi avanzati.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa **tipo di query** per scegliere integrità del carico di lavoro e selezionare la sottoscrizione, il gruppo di risorse o le risorse della macchina virtuale di destinazione. Utilizzare gli elenchi a discesa Filtro integrità per selezionare un subset interessante di eventi imprevisti di integrità per le proprie esigenze analitiche.

![Screenshot della query degli avvisi](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Integrità risorse di Azure 

Le cartelle di lavoro supportano l'ottenimento di integrità delle risorse di Azure e la combinazione con altre origini dati per creare report di integrità avanzati e interattivi

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa **tipo di query** per scegliere integrità di Azure e selezionare le risorse di destinazione. Usare gli elenchi a discesa del filtro di integrità per selezionare un subset interessante di problemi di risorse in base alle esigenze analitiche.

![Screenshot della query degli avvisi](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Esplora dati di Azure (anteprima)

Le cartelle di lavoro ora includono il supporto per l'esecuzione di query da cluster [Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/) con il potente linguaggio di query [kusto](https://docs.microsoft.com/azure/kusto/query/index) .   

![Screenshot della finestra di query kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
