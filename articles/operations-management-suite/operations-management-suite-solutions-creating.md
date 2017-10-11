---
title: Creare una soluzione di gestione in OMS | Documentazione Microsoft
description: "Le soluzioni di gestione estendono la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere all'area di lavoro OMS.  In questo articolo vengono fornite informazioni dettagliate su come creare soluzioni di gestione da usare nel proprio ambiente o da rendere disponibili per i propri clienti."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ea959f1e95ea46c07eec3afa9d46bfeb72ca3e4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="design-and-build-a-management-solution-in-operations-management-suite-oms-preview"></a>Progettare e creare una soluzione di gestione in Operations Management Suite (OMS) (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.

Le [soluzioni di gestione](operations-management-suite-solutions.md) estendono la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere all'area di lavoro OMS.  Questo articolo presenta un processo di base per progettare e compilare una soluzione di gestione adatta ai requisiti più comuni.  Se non si ha familiarità con la creazione di soluzioni di gestione, è possibile usare questo processo come punto di partenza e quindi usare i concetti per soluzioni più complesse come l'evolversi delle esigenze.

## <a name="what-is-a-management-solution"></a>Che cos'è una soluzione di gestione?

Le soluzioni di gestione contengono risorse OMS e Azure che interagiscono per raggiungere un determinato scenario di monitoraggio.  Vengono implementate come [modelli di gestione delle risorse](../azure-resource-manager/resource-manager-template-walkthrough.md) conteneneti informazioni dettagliate su come installare e configurare le risorse contenute quando si installa la soluzione.

La strategia di base consiste nell'avviare la soluzione di gestione compilando i singoli componenti nell'ambiente Azure.  Dopo che la funzionalità opera correttamente, è possibile avviare la creazione del pacchetto in un [file di soluzione di gestione](operations-management-suite-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Progettare la soluzione
Nel diagramma seguente è illustrato il modello più comune per una soluzione di gestione.  I diversi componenti in questo modello vengono descritti di seguito.

![Panoramica della soluzione OMS](media/operations-management-suite-solutions/solution-overview.png)


### <a name="data-sources"></a>Origini dati
Il primo passaggio nella progettazione di una soluzione consiste nel determinare i dati richiesti dall'archivio di Log Analytics.  Questi dati possono essere raccolti da un'[origine dati](../log-analytics/log-analytics-data-sources.md) o da [un'altra soluzione](operations-management-suite-solutions.md) oppure la soluzione potrebbe dover fornire il processo di raccolta.

Esistono diversi modi per raccogliere le origini dati nel repository di Log Analytics, come descritto in [Origini dati in Log Analytics](../log-analytics/log-analytics-data-sources.md).  Alcuni esempi sono gli eventi nel registro eventi di Windows o generati da Syslog, oltre ai contatori delle prestazioni per client Windows e Linux.  È possibile anche raccogliere dati dalle risorse di Azure raccolte da Monitoraggio di Azure.  

Se si richiedono dati non accessibili tramite una delle origini dati disponibili, è possibile usare l'[API dell'agente di raccolta dati HTTP](../log-analytics/log-analytics-data-collector-api.md) che consente di scrivere dati nell'archivio di Log Analytics da qualsiasi client in grado di chiamare un'API REST.  Il mezzo più comune di raccolta dati personalizzati in una soluzione di gestione è creare un [runbook in Automazione di Azure](../automation/automation-runbook-types.md) che raccoglie i dati richiesti dalle risorse di Azure o da origini esterne e usa l'API dell'agente di raccolta dati per scrivere nel repository.  

### <a name="log-searches"></a>Ricerche log
Le [ricerche nei log](../log-analytics/log-analytics-log-searches.md) vengono usate per estrarre e analizzare i dati nel repository di Log Analytics.  Vengono usate da visualizzazioni e avvisi, oltre a consentire all'utente di eseguire analisi ad hoc dei dati nel repository.  

È necessario definire le query che si ritiene risulteranno utili all'utente, anche se non vengono usate da tutte le visualizzazioni o gli avvisi.  Queste saranno disponibili come ricerche salvate nel portale ed è possibile anche includerle in una [parte della visualizzazione Elenco di query](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) nella visualizzazione personalizzata.

### <a name="alerts"></a>Avvisi
[Gli avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md) identificano i problemi tramite le [ricerche nei log](#log-searches) a fronte dei dati nel repository.  Inviano una notifica all'utente o eseguono automaticamente un'azione in risposta. Occorre identificare le diverse condizioni di avviso per l'applicazione e includere le regole di avviso corrispondenti nel file della soluzione.

Se il problema può essere risolto potenzialmente con un processo automatizzato, si creerà in genere un runbook in Automazione di Azure per eseguire la correzione.  La maggior parte dei servizi di Azure può essere gestita con [cmdlet](/powershell/azure/overview) che il runbook sfrutta per eseguire queste funzionalità.

Se la soluzione richiede funzionalità esterne in risposta a un avviso, è possibile usare una [risposta di webhook](../log-analytics/log-analytics-alerts-actions.md).  Ciò consente di chiamare un servizio Web esterno che invia informazioni dall'avviso.

### <a name="views"></a>Visualizzazioni
Le visualizzazioni in Log Analytics vengono usate per visualizzare i dati dal repository di Log Analytics.  Ogni soluzione conterrà in genere una singola visualizzazione con un [riquadro](../log-analytics/log-analytics-view-designer-tiles.md) che viene visualizzato nel dashboard principale dell'utente.  La visualizzazione può contenere un numero qualsiasi di [parti di visualizzazione](../log-analytics/log-analytics-view-designer-parts.md) per fornire visualizzazioni differenti dei dati raccolti per l'utente.

[Creare visualizzazioni personalizzate usando Progettazione viste](../log-analytics/log-analytics-view-designer.md) che è possibile esportare in un secondo momento per l'inclusione nel file della soluzione.  


## <a name="create-solution-file"></a>Creare il file di soluzione
Dopo avere configurato e testato i componenti che faranno parte della soluzione, è possibile [creare il file di soluzione](operations-management-suite-solutions-solution-file.md).  Si implementeranno i componenti della soluzione in un [modello di Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) che include le [risorse della soluzione](operations-management-suite-solutions-solution-file.md#solution-resource) con relazioni ad altre risorse nel file.  


## <a name="test-your-solution"></a>Testare la soluzione personalizzata
Durante lo sviluppo della soluzione, è necessario installarla e testarla nell'area di lavoro.  È possibile farlo usando uno dei metodi disponibili per [testare e installare i modelli di Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Pubblicare la soluzione
Dopo avere completato e testato la soluzione, è possibile renderla disponibile ai clienti tramite le seguenti origini.

- **Modelli di avvio rapido di Azure**.  [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) è un set di modelli di Resource Manager messi a disposizione dalla community tramite GitHub.  È possibile rendere disponibile la soluzione seguendo le informazioni nella [Contribution Guide](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) consente di distribuire e vendere la soluzione agli altri sviluppatori, agli ISV e ai professionisti IT.  Informazioni su come pubblicare la soluzione in Azure Marketplace sono disponibili in [Come pubblicare e gestire un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare un file di soluzione](operations-management-suite-solutions-solution-file.md) per la soluzione di gestione.
* Informazioni dettagliate sulla [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Cercare i [modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates) per esempi di modelli di Resource Manager.