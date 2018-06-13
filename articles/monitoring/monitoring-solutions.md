---
title: Soluzioni di gestione in Azure | Microsoft Docs
description: Le soluzioni di gestione in Azure sono una raccolta di regole logiche, di visualizzazione e di acquisizione dei dati che forniscono metriche relative a un'area problematica specifica.  Questo articolo offre informazioni sull'installazione e l'uso delle soluzioni di gestione.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 0df54d1758693bce5fb5fd74c3be9c4cfd7dccb6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885269"
---
# <a name="management-solutions-in-azure"></a>Soluzioni di gestione in Azure
Le soluzioni di gestione sfruttano i servizi in Azure per offrire informazioni dettagliate aggiuntive sul funzionamento di un determinato servizio o applicazione. Questo articolo offre una breve panoramica delle soluzioni di gestione in Azure e informazioni dettagliate sull'uso e l'installazione.

Le soluzioni di gestione raccolgono in genere informazioni in Log Analytics e offrono ricerche nei log e visualizzazioni per l'analisi dei dati raccolti. Possono anche sfruttare altri servizi, come Automazione di Azure, per eseguire azioni relative all'applicazione o al servizio.

È possibile aggiungere soluzioni di gestione alla sottoscrizione di Azure per qualsiasi applicazione e servizio usato. Sono in genere disponibili gratuitamente, ma la raccolta dati potrebbe implicare addebiti per l'utilizzo. Oltre alle soluzioni fornite da Microsoft, i partner e i clienti possono [creare soluzioni di gestione](../monitoring/monitoring-solutions-creating.md) da usare nel proprio ambiente o da rendere disponibili per i propri clienti attraverso la community.

## <a name="using-management-solutions"></a>Uso delle soluzioni di gestione
Nella pagina **Panoramica** per ogni area di lavoro di Log Analytics viene visualizzato un riquadro per ogni soluzione installata nell'area di lavoro. Fare clic sul riquadro della soluzione per aprire la visualizzazione corrispondente che include un'analisi più dettagliata dei dati raccolti.

![Panoramica](media/monitoring-solutions/overview.png)

Le soluzioni di gestione possono contenere più tipi di risorse di Azure ed è possibile visualizzare tutte le risorse incluse in una soluzione proprio come qualsiasi altra risorsa. Ad esempio, le ricerche nei log incluse nella soluzione sono incluse in **Ricerche salvate** nell'area di lavoro. È possibile usare queste ricerche durante l'esecuzione di analisi ad hoc in Log Analytics.

## <a name="list-installed-management-solutions"></a>Elencare le soluzioni di gestione installate 
Usare la procedura seguente per elencare le soluzioni di gestione installate nella sottoscrizione.

1. Accedere al portale di Azure.
2. Nel riquadro sinistro selezionare **Tutti i servizi**.
3. Scorrere verso il basso fino a **Soluzioni** oppure digitare *soluzioni* nella finestra di dialogo **Filtra**.
4. Vengono elencate le soluzioni installate in tutte le aree di lavoro. Il nome della soluzione è seguito dal nome dell'area di lavoro di Log Analytics in cui è installata.
1. Usare le caselle a discesa nella parte superiore della schermata per filtrare in base alla sottoscrizione o al gruppo di risorse.


![Elencare tutte le soluzioni](media/monitoring-solutions/list-solutions-all.png)

Fare clic sul nome di una soluzione per aprire la relativa pagina di riepilogo. Questa pagina mostra le eventuali visualizzazioni di Log Analytics incluse nella soluzione e fornisce diverse opzioni per la soluzione stessa e la relativa area di lavoro. Visualizzare la pagina di riepilogo per una soluzione usando una delle procedure sopra indicate per elencare le soluzioni e quindi fare clic sul nome della soluzione.

![Proprietà della soluzione](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>Trovare le soluzioni di gestione
È possibile cercare e installare le soluzioni di gestione disponibili da Microsoft e dai partner in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace). Eseguire una [ricerca di *soluzioni di gestione* ](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) per filtrare l'elenco e quindi fare clic su qualsiasi elemento per maggiori dettagli.

![Marketplace](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>Installare una soluzione di gestione

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>Installare una soluzione di gestione da Azure Marketplace
È possibile usare uno dei metodi seguenti per individuare e avviare l'installazione di una soluzione di gestione.

- Fare clic su **Scarica adesso** per una soluzione di gestione in [Azure Marketplace](#find-management-solutions).
- Nell'[elenco delle soluzioni per la sottoscrizione ](#list-installed-management-solutions) fare clic su **Aggiungi**. A destra di **Soluzioni di gestione** fare clic su **Altro**. Individuare la soluzione di gestione desiderata e fare clic su **Crea**.
- Nel portale di Azure selezionare **Crea una risorsa** > **Monitoraggio e gestione** > **Visualizza tutto**. A destra di **Soluzioni di gestione** fare clic su **Altro**. Individuare la soluzione di gestione desiderata e fare clic su **Crea**.

Quando viene avviato il processo di installazione, viene richiesto di fornire la configurazione necessaria che varia per ogni soluzione. Per tutte le soluzioni è necessario selezionare un'area di lavoro di Log Analytics in cui la soluzione verrà installata e in cui verranno raccolti i dati. Potrebbe inoltre essere necessario [specificare un account di Automazione](#log-analytics-workspace-and-automation-account), se richiesto per la soluzione.

### <a name="install-a-solution-from-the-community"></a>Installare una soluzione dalla community
I membri della community possono inviare le soluzioni di gestione come modelli di avvio rapido di Azure. È possibile installare direttamente queste soluzioni o scaricarle per installarle in un secondo momento.

1. Seguire la procedura descritta in [Area di lavoro di Log Analytics e account di Automazione](#log-analytics-workspace-and-automation-account) per collegare un'area di lavoro e un account.
2. Passare a [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). 
3. Cercare una soluzione adatta alle proprie esigenze.
4. Selezionare la soluzione nei risultati per visualizzarne i dettagli.
5. Fare clic sul pulsante **Distribuisci in Azure**.
6. Viene richiesto di fornire informazioni come il gruppo di risorse e la posizione, oltre ai valori per i parametri nella soluzione.
7. Fare clic su **Acquista** per installare la soluzione.


## <a name="log-analytics-workspace-and-automation-account"></a>Area di lavoro di Log Analytics e account di Automazione
Tutte le soluzioni di gestione richiedono un'[area di lavoro di Log Analytics](../log-analytics/log-analytics-manage-access.md) per archiviare i dati raccolti dalla soluzione e per ospitare le ricerche nei log e le visualizzazioni. Alcune soluzioni richiedono anche un [account di Automazione](../automation/automation-security-overview.md#automation-account-overview) per contenere i runbook e le relative risorse. L'area di lavoro e l'account devono soddisfare i requisiti indicati di seguito.

* Ogni installazione di una soluzione può usare solo un'area di lavoro di Log Analytics e un account di Automazione. È possibile installare la soluzione separatamente in più aree di lavoro.
* Se una soluzione richiede un account di Automazione, l'area di lavoro di Log Analytics e l'account di Automazione devono essere collegati tra loro. Un'area di lavoro di Log Analytics può essere collegata a un solo account di Automazione e un account di Automazione può essere collegato a una sola area di lavoro di Log Analytics.
* Per essere collegati, l'area di lavoro di Log Analytics e l'account di Automazione devono trovarsi nello stesso gruppo di risorse e nella stessa area. L'eccezione è rappresentata da un'area di lavoro nell'area Stati Uniti orientali e un account di Automazione nell'area Stati Uniti orientali 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Creazione di un collegamento tra un'area di lavoro di Log Analytics e un account di Automazione
La modalità con cui si specificano l'area di lavoro di Log Analytics e l'account di Automazione dipende dal metodo di installazione per la soluzione.

* Quando si installa una soluzione tramite Azure Marketplace, vengono richiesti un'area di lavoro e un account di Automazione. Se non sono già collegati, viene creato il collegamento tra di essi.
* Per le soluzioni esterne ad Azure Marketplace, è necessario collegare l'area di lavoro di Log Analytics e l'account di Automazione prima di installare la soluzione. A tale scopo, è possibile selezionare qualsiasi soluzione in Azure Marketplace e quindi selezionare l'area di lavoro di Log Analytics e l'account di Automazione. Non è necessario installare effettivamente la soluzione, perché il collegamento viene creato non appena si selezionano l'area di lavoro di Log Analytics e l'account di Automazione. Una volta creato il collegamento, è possibile usare l'area di lavoro di Log Analytics e l'account di Automazione per qualsiasi soluzione.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifica del collegamento tra un'area di lavoro di Log Analytics e un account di Automazione
È possibile verificare il collegamento tra un'area di lavoro di Log Analytics e un account Automazione mediante la procedura seguente.

1. Selezionare l'account di Automazione nel portale di Azure.
1. Scorrere fino alla sezione **Risorse correlate** del menu.
1. Se l'impostazione **Area di lavoro** è abilitata, questo account viene collegato a un'area di lavoro di Log Analytics. È possibile fare clic su **Area di lavoro** per visualizzare i dettagli dell'area di lavoro.

## <a name="remove-a-management-solution"></a>Rimuovere una soluzione di gestione
Per rimuovere una soluzione installata, individuarla nell'[elenco delle soluzioni installate](#list-installed-management-solutions). Fare clic sul nome della soluzione per aprire la pagina di riepilogo e quindi fare clic su **Elimina**.




## <a name="next-steps"></a>Passaggi successivi
* Ottenere un [elenco di soluzioni di gestione da Microsoft](monitoring-solutions-inventory.md).
* Informazioni su come [creare query](../log-analytics/log-analytics-log-searches.md) per analizzare i dati raccolti dalle soluzioni di gestione.

