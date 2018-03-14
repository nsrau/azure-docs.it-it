---
title: Visualizzazione delle dipendenze in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: bcbb2ace6686e4052149a5dde1ed837a16c36bad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

Il servizio [Azure Migrate](migrate-overview.md) valuta i gruppi di computer locali per la migrazione ad Azure. Per raggruppare i computer, è possibile usare la visualizzazione delle dipendenze. Questo articolo contiene informazioni su questa funzionalità.


## <a name="overview"></a>Panoramica

La visualizzazione delle dipendenze in Azure Migrate consente di creare gruppi per valutare la migrazione in modo più attendibile. Usando questa funzionalità è possibile visualizzare le dipendenze di rete di computer specifici o all'interno di un gruppo. In questo modo si ha la sicurezza che nessuna funzionalità venga persa (o nessun computer dimenticato) nel corso del processo di migrazione, quando app e carichi di lavoro vengono eseguiti su più computer.  

## <a name="how-does-it-work"></a>Come funziona?

Per visualizzare le dipendenze, Azure Migrate usa la soluzione [Elenco dei servizi](../operations-management-suite/operations-management-suite-service-map.md) in [Log Analytics](../log-analytics/log-analytics-overview.md).
- Quando si crea un progetto di migrazione di Azure, nella sottoscrizione viene creata un'area di lavoro di OMS Log Analytics.
- Il nome dell'area di lavoro è quello specificato per il progetto di migrazione, preceduto dal prefisso **migrate-** e, facoltativamente, seguito da un suffisso numerico. 
- Passare all'area di lavoro di Log Analytics dalla sezione **Informazioni di base** della pagina **Panoramica**.
- L'area di lavoro creata è contrassegnata con la chiave **MigrateProject** e il valore del **nome del progetto**. È possibile usare questi valori per eseguire ricerche nel portale di Azure.  

    ![Area di lavoro di Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Per usare la visualizzazione delle dipendenze, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole analizzare.  

## <a name="do-i-need-to-pay-for-it"></a>Il servizio è a pagamento?

Azure Migrate è disponibile senza costi aggiuntivi. L'uso delle funzionalità di visualizzazione delle dipendenze di Azure Migrate richiede Mapping dei servizi. Alla creazione di un progetto di Azure Migrate, il servizio genererà automaticamente una nuova area di lavoro di Log Analytics.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze usa Mapping dei servizi tramite un'area di lavoro di Log Analytics. Dal 28 febbraio 2018, con l'annuncio della disponibilità generale di Azure Migrate, la funzionalità è disponibile senza costi aggiuntivi. È necessario creare un nuovo progetto per poter usufruire dell'area di lavoro utilizzabile gratuitamente. Le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili, pertanto è consigliabile passare a un nuovo progetto.

1. Per l'uso di qualsiasi soluzione diversa da Mapping dei servizi entro questa area di lavoro di Log Analytics verranno applicati gli addebiti standard di Log Analytics. 
2. Per supportare scenari di migrazione senza costi aggiuntivi, la soluzione Mapping dei servizi non genererà addebiti per i primi 180 giorni a partire dalla creazione di un progetto di Azure Migrate. Successivamente verranno applicati gli addebiti standard.
3. Solo l'area di lavoro creata nell'ambito della creazione del progetto sarà disponibile per l'uso gratuito.

Quando si registrano agenti nell'area di lavoro, usare l'ID e la chiave specificati dal progetto nella pagina della procedura di installazione dell'agente. Non è possibile usare un'area di lavoro esistente e associarla al progetto di Azure Migrate.

All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. A seguito dell'eliminazione del progetto, l'utilizzo di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro di Log Analytics.

Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/pricing/details/azure-migrate/). 

## <a name="how-do-i-manage-the-workspace"></a>Come si gestisce l'area di lavoro?

È possibile usare l'area di lavoro di Log Analytics all'esterno di Azure Migrate. L'area di lavoro non viene eliminata se si rimuove il progetto di migrazione in cui è stata creata. Se non è più necessaria, [eliminarla](../log-analytics/log-analytics-manage-access.md) manualmente.

Non eliminare l'area di lavoro creata da Azure Migrate a meno che non si rimuova il progetto di migrazione. Se si esegue questa operazione, le dipendenze non funzioneranno come previsto.

## <a name="next-steps"></a>Passaggi successivi

[Raggruppare i computer usando le dipendenze dei computer](how-to-create-group-machine-dependencies.md)