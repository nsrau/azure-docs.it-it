---
title: Origini dati delle cartelle di lavoro di Monitoraggio di Azure Documenti Microsoft
description: Semplifica la creazione di report complessi con cartelle di lavoro di Monitoraggio di Azure predefinite e personalizzate create da più origini dati
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248580"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Origini dati delle cartelle di lavoro di Azure MonitorAzure Monitor workbooks data sources

Le cartelle di lavoro sono compatibili con un numero elevato di origini dati. Questo articolo illustra le origini dati attualmente disponibili per le cartelle di lavoro di Monitoraggio di Azure.This article will guide you through data sources which are currently available for Azure Monitor workbooks.

## <a name="logs"></a>Log

Le cartelle di lavoro consentono l'esecuzione di query nei log dalle origini seguenti:Workbooks allow querying logs from the following sources:

* Log di Monitoraggio di Azure (risorse di Application Insights e aree di lavoro di Log Analytics).
* Dati incentrati sulle risorse (log attività)Resource-centric data (Activity logs)

Gli autori di cartelle di lavoro possono utilizzare query KQL che trasformano i dati delle risorse sottostanti per selezionare un set di risultati che può essere visualizzato come testo, grafici o griglie.

![Screenshot dell'interfaccia del report dei log delle cartelle di lavoro](./media/workbooks-overview/logs.png)

Gli autori di cartelle di lavoro possono facilmente eseguire query su più risorse creando un'esperienza di creazione di report veramente unificata.

## <a name="metrics"></a>Metriche

Le risorse di Azure generano [metriche](data-platform-metrics.md) a cui è possibile accedere tramite cartelle di lavoro. È possibile accedere alle metriche nelle cartelle di lavoro tramite un controllo specializzato che consente di specificare le risorse di destinazione, le metriche desiderate e la relativa aggregazione. Questi dati possono quindi essere tracciati in grafici o griglie.

![Screenshot dei grafici delle metriche della cartella di lavoro sull'utilizzo di cpu](./media/workbooks-overview/metrics-graph.png)

![Screenshot dell'interfaccia delle metriche della cartella di lavoro](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Diagramma delle risorse di Azure 

Le cartelle di lavoro supportano l'esecuzione di query per le risorse e i relativi metadati tramite Azure Resource Graph (ARG). Questa funzionalità viene utilizzata principalmente per creare ambiti di query personalizzati per i report. L'ambito delle risorse è espresso tramite un sottoinsieme KQL supportato da ARG, che spesso è sufficiente per i casi d'uso comuni.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa Tipo di query per scegliere Azure Resource Graph e selezionare le sottoscrizioni di destinazione. Utilizzare il controllo Query per aggiungere il sottoinsieme ARG KQL che seleziona un sottoinsieme di risorse interessante.


![Screenshot della query KQL di Azure Resource Graph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Avvisi (anteprima)Alerts (preview)

Le cartelle di lavoro consentono agli utenti di visualizzare gli avvisi attivi relativi alle risorse. Questa funzionalità consente la creazione di report che riuniscono i dati delle notifiche (avviso) e le informazioni di diagnostica (metriche, log) in un unico report. Queste informazioni possono anche essere unite per creare report avanzati che combinano informazioni dettagliate tra queste origini dati.

Per fare in modo che un controllo query utilizzi questa origine dati, usare l'elenco a discesa Tipo di query per scegliere Avvisi e selezionare le sottoscrizioni, i gruppi di risorse o le risorse di destinazione. Utilizzare i menu a discesa del filtro degli avvisi per selezionare un sottoinsieme interessante di avvisi per le esigenze di analisi.

![Screenshot della query degli avvisi](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Integrità del carico di lavoro (anteprima)Workload health (preview)

Monitoraggio di Azure dispone di funzionalità che monitorano in modo proattivo la disponibilità e le prestazioni dei sistemi operativi guest Windows o Linux.Azure Monitor has functionality that proactively monitor the availability and performance of Windows or Linux guest operating systems. Monitoraggio di Azure modella i componenti chiave e le relative relazioni, i criteri per misurare l'integrità di tali componenti e i componenti che inviano un avviso quando viene rilevata una condizione non integra. Le cartelle di lavoro consentono agli utenti di utilizzare queste informazioni per creare report interattivi avanzati.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa **Tipo** di query per scegliere Integrità carico di lavoro e selezionare le risorse di sottoscrizione, gruppo di risorse o VM di destinazione. Utilizzare i menu a discesa del filtro di integrità per selezionare un sottoinsieme interessante di incidenti di salute per le esigenze di analisi.

![Screenshot della query degli avvisi](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Integrità delle risorse di AzureAzure resource health 

Le cartelle di lavoro supportano l'ottenimento dell'integrità delle risorse di Azure e la combinazione con altre origini dati per creare report di integrità interattivi e avanzatiWorkbooks support getting Azure resource health and combining it with other data sources to create rich, interactive health reports

Per fare in modo che un controllo di query usi questa origine dati, usare l'elenco a discesa **Tipo** di query per scegliere Integrità di Azure e selezionare le risorse di destinazione. Usare gli elenchi a discesa del filtro integrità per selezionare un sottoinsieme interessante di problemi relativi alle risorse per le esigenze analitiche.

![Screenshot della query degli avvisi](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (anteprima)Azure Data Explorer (preview)

Le cartelle di lavoro dispongono ora del supporto per l'esecuzione di query dai cluster di [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) con il potente linguaggio di query Kusto.Workbooks now have support for querying from Azure Data Explorer clusters with the powerful [Kusto](https://docs.microsoft.com/azure/kusto/query/index) query language.   

![Schermata della finestra di query kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
