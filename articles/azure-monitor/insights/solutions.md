---
title: Soluzioni di monitoraggio in Monitoraggio di Azure | Microsoft Docs
description: Le soluzioni di monitoraggio in Monitoraggio di Azure sono una raccolta di regole logiche, di visualizzazione e di acquisizione dei dati che forniscono metriche relative a un'area problematica specifica.  Questo articolo offre informazioni sull'installazione e l'uso delle soluzioni di monitoraggio.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 6932f9f302bf1816842d78a662f01ebaafed8686
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989183"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluzioni di monitoraggio in Monitoraggio di Azure
Le soluzioni di monitoraggio sfruttano i servizi in Azure per offrire informazioni dettagliate aggiuntive sul funzionamento di un determinato servizio o di un'applicazione specifica. Questo articolo offre una breve panoramica delle soluzioni di monitoraggio in Azure e informazioni dettagliate sull'uso e l'installazione di tali soluzioni.

> [!NOTE]
> In precedenza le soluzioni di monitoraggio erano denominate soluzioni di gestione.

Le soluzioni di monitoraggio raccolgono in genere log di dati e offrono query e visualizzazioni per l'analisi dei dati raccolti. Possono anche sfruttare altri servizi, come Automazione di Azure, per eseguire azioni relative all'applicazione o al servizio.

È possibile aggiungere soluzioni di monitoraggio a Monitoraggio di Azure per qualsiasi applicazione e servizio usati. Sono in genere disponibili gratuitamente, ma la raccolta dati potrebbe implicare addebiti per l'utilizzo. Oltre alle soluzioni fornite da Microsoft, i partner e i clienti possono [creare soluzioni di gestione](solutions-creating.md) da usare nel proprio ambiente o da rendere disponibili per i propri clienti attraverso la community.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Usare le soluzioni di monitoraggio
Aprire la pagina **Panoramica** in Monitoraggio di Azure per visualizzare un riquadro per ogni soluzione installata nell'area di lavoro. 

1. Accedere al portale di Azure.
1. Aprire **Tutti i servizi** e individuare **Monitoraggio**.
1. Nel menu **Informazioni dettagliate** selezionare **Altri**.
1. Usare le caselle a discesa nella parte superiore della schermata per modificare l'area di lavoro o l'intervallo di tempo usato per i riquadri.
1. Fare clic sul riquadro di una soluzione per aprire la visualizzazione corrispondente che include un'analisi più dettagliata dei dati raccolti.

![Panoramica](media/solutions/overview.png)

Le soluzioni di monitoraggio possono contenere più tipi di risorse di Azure ed è possibile visualizzare tutte le risorse incluse in una soluzione proprio come qualsiasi altra risorsa. Ad esempio, le query di log incluse nella soluzione sono elencate in **Solution Queries** (Query soluzione) in [Esplora query](../log-query/get-started-portal.md#load-queries). È possibile usare queste query durante l'esecuzione di analisi ad hoc con [query di log](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Elencare le soluzioni di monitoraggio installate 
Usare la procedura seguente per elencare le soluzioni di monitoraggio installate nella sottoscrizione.

1. Accedere al portale di Azure.
1. Aprire **Tutti i servizi** e individuare **Soluzioni**.
4. Vengono elencate le soluzioni installate in tutte le aree di lavoro. Il nome della soluzione è seguito dal nome dell'area di lavoro in cui è installata.
1. Usare le caselle a discesa nella parte superiore della schermata per filtrare in base alla sottoscrizione o al gruppo di risorse.


![Elencare tutte le soluzioni](media/solutions/list-solutions-all.png)

Fare clic sul nome di una soluzione per aprire la relativa pagina di riepilogo. Questa pagina mostra le visualizzazioni incluse nella soluzione e fornisce diverse opzioni per la soluzione stessa e la relativa area di lavoro. Visualizzare la pagina di riepilogo per una soluzione usando una delle procedure sopra indicate per elencare le soluzioni e quindi fare clic sul nome della soluzione.

![Proprietà della soluzione](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Installare una soluzione di monitoraggio
Le soluzioni di monitoraggio di Microsoft e dei partner sono disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com). È possibile cercare e installare le soluzioni disponibili usando la procedura seguente. Quando si installa una soluzione, è necessario selezionare un'[area di lavoro Log Analytics](../platform/manage-access.md) in cui la soluzione verrà installata e in cui verranno raccolti i dati.

1. Nell'[elenco delle soluzioni per la sottoscrizione ](#list-installed-monitoring-solutions) fare clic su **Aggiungi**.
1. Sfogliare o cercare una soluzione. È anche possibile esplorare le soluzioni da [questo collegamento di ricerca](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Individuare la soluzione di monitoraggio desiderata e leggere la relativa descrizione.
1. Fare clic su **Crea** per avviare il processo di installazione.
1. Quando viene avviato il processo di installazione, viene richiesto di specificare l'area di lavoro Log Analytics e fornire la configurazione necessaria per la soluzione.

![Installare una soluzione](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installare una soluzione dalla community
I membri della community possono inviare le soluzioni di gestione come modelli di avvio rapido di Azure. È possibile installare direttamente queste soluzioni o scaricarle per installarle in un secondo momento.

1. Seguire la procedura descritta in [area di lavoro Log Analytics e account di Automazione](#log-analytics-workspace-and-automation-account) per collegare un'area di lavoro e un account.
2. Passare a [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). 
3. Cercare una soluzione adatta alle proprie esigenze.
4. Selezionare la soluzione nei risultati per visualizzarne i dettagli.
5. Fare clic sul pulsante **Distribuisci in Azure**.
6. Viene richiesto di fornire informazioni come il gruppo di risorse e la posizione, oltre ai valori per i parametri nella soluzione.
7. Fare clic su **Acquista** per installare la soluzione.


## <a name="log-analytics-workspace-and-automation-account"></a>area di lavoro Log Analytics e account di Automazione
Tutte le soluzioni di monitoraggio richiedono un'[area di lavoro Log Analytics](../platform/manage-access.md) per archiviare i dati raccolti dalla soluzione e per ospitare le ricerche nei log e le visualizzazioni. Alcune soluzioni richiedono anche un [account di Automazione](../../automation/automation-security-overview.md#automation-account-overview) per contenere i runbook e le relative risorse. L'area di lavoro e l'account devono soddisfare i requisiti indicati di seguito.

* Ogni installazione di una soluzione può usare solo un'area di lavoro Log Analytics e un account di Automazione. È possibile installare la soluzione separatamente in più aree di lavoro.
* Se una soluzione richiede un account di Automazione, l'area di lavoro Log Analytics e l'account di Automazione devono essere collegati tra loro. Un'area di lavoro Log Analytics può essere collegata a un solo account di Automazione e un account di Automazione può essere collegato a una sola area di lavoro Log Analytics.
* Per essere collegati, l'area di lavoro Log Analytics e l'account di Automazione devono trovarsi nello stesso gruppo di risorse e nella stessa area. L'eccezione è rappresentata da un'area di lavoro nell'area Stati Uniti orientali e un account di Automazione nell'area Stati Uniti orientali 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Creare un collegamento tra un'area di lavoro Log Analytics e un account di Automazione
La modalità con cui si specificano l'area di lavoro Log Analytics e l'account di Automazione dipende dal metodo di installazione per la soluzione.

* Quando si installa una soluzione tramite Azure Marketplace, vengono richiesti un'area di lavoro e un account di Automazione. Se non sono già collegati, viene creato il collegamento tra di essi.
* Per le soluzioni esterne ad Azure Marketplace, è necessario collegare l'area di lavoro Log Analytics e l'account di Automazione prima di installare la soluzione. A tale scopo, è possibile selezionare qualsiasi soluzione in Azure Marketplace e quindi selezionare l'area di lavoro Log Analytics e l'account di Automazione. Non è necessario installare effettivamente la soluzione, perché il collegamento viene creato non appena si selezionano l'area di lavoro Log Analytics e l'account di Automazione. Una volta creato il collegamento, è possibile usare l'area di lavoro Log Analytics e l'account di Automazione per qualsiasi soluzione.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verificare il collegamento tra un'area di lavoro Log Analytics e un account di Automazione
È possibile verificare il collegamento tra un'area di lavoro Log Analytics e un account Automazione mediante la procedura seguente.

1. Selezionare l'account di Automazione nel portale di Azure.
1. Scorrere fino alla sezione **Risorse correlate** del menu.
1. Se l'impostazione **Area di lavoro** è abilitata, questo account viene collegato a un'area di lavoro Log Analytics. È possibile fare clic su **Area di lavoro** per visualizzare i dettagli dell'area di lavoro.

## <a name="remove-a-monitoring-solution"></a>Rimuovere una soluzione di monitoraggio
Per rimuovere una soluzione installata, individuarla nell'[elenco delle soluzioni installate](#list-installed-monitoring-solutions). Fare clic sul nome della soluzione per aprire la pagina di riepilogo e quindi fare clic su **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
* Ottenere un [elenco di soluzioni di monitoraggio da Microsoft](solutions-inventory.md).
* Informazioni su come [creare query](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle soluzioni di monitoraggio.

