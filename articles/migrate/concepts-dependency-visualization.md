---
title: Visualizzazione delle dipendenze in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: raynew
ms.openlocfilehash: 04ae28ca566e97570ec64e78d3408ea8bd1e3d42
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010321"
---
# <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

Il servizio [Azure Migrate](migrate-overview.md) valuta i gruppi di computer locali per la migrazione ad Azure. Per creare gruppi è possibile usare la funzionalità di visualizzazione delle dipendenze in Azure Migrate. Questo articolo contiene informazioni su questa funzionalità.


## <a name="overview"></a>Panoramica

La visualizzazione delle dipendenze in Azure Migrate consente di creare gruppi con affidabilità elevata per valutare la migrazione. Usando la visualizzazione delle dipendenze è possibile visualizzare le dipendenze di rete dei computer e identificare i computer correlati che dovevano essere migrati insieme ad Azure. Questa funzionalità è utile negli scenari in cui non sono completamente compatibili con le macchine che costituiscono l'applicazione e devono essere migrate insieme ad Azure.

## <a name="how-does-it-work"></a>Come funziona?

Per visualizzare le dipendenze, Azure Migrate usa la soluzione [Elenco dei servizi](../operations-management-suite/operations-management-suite-service-map.md) in [Log Analytics](../log-analytics/log-analytics-overview.md).
- Per sfruttare i vantaggi della visualizzazione delle dipendenze è possibile associare un'area di lavoro di Log Analytics, nuova o esistente, a un progetto di Azure Migrate.
- È possibile creare o collegare solo un'area di lavoro nella stessa sottoscrizione in cui viene creato il progetto di migrazione.
- Per collegare un'area di lavoro di Log Analytics a un progetto, passare alla sezione **Essentials** della pagina **Panoramica** del progetto e fare clic su **Configurazione richiesta**

    ![Associare un'area di lavoro di Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Quando si crea una nuova area di lavoro è necessario specificare un nome per essa. L'area di lavoro viene quindi creata in una regione della stessa [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) come progetto di migrazione.
- L'area di lavoro associata è contrassegnata con il tasto **progetto di migrazione** della chiave e con il valore **project name**, che è possibile usare per eseguire la ricerca nel portale di Azure.
- Per passare all'area di lavoro associata al progetto è possibile passare alla sezione **Essentials** della pagina **Panoramica** del progetto e accedere all'area di lavoro

    ![Navigare nell'area di lavoro di Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Per usare la visualizzazione delle dipendenze, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole analizzare.  

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) deve essere installato in ogni computer.
- [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) deve essere installato in ogni computer.
- Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.

Questi agenti non sono necessari sui computer da valutare, a meno che non si usi la visualizzazione delle dipendenze.

## <a name="do-i-need-to-pay-for-it"></a>Il servizio è a pagamento?

Azure Migrate è disponibile senza costi aggiuntivi. L'uso della funzionalità di visualizzazione delle dipendenze in Azure Migrate richiede il Mapping dei servizi e di associare un'area di lavoro di Log Analytics, nuova o esistente, al progetto di Azure Migrate. La funzionalità di visualizzazione delle dipendenze in Azure Migrate è gratuita per i primi 180 giorni in Azure Migrate.

1. Per usare una soluzione diversa da Mapping dei servizi in questa area di lavoro di Log Analytics verranno applicati gli addebiti [standard di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
2. Per supportare scenari di migrazione senza costi aggiuntivi, la soluzione Mapping dei servizi non genererà addebiti per i primi 180 giorni a partire dal giorno dell'associazione dell'area di lavoro di Log Analytics al progetto di Azure Migrate. Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.

Quando si registrano agenti nell'area di lavoro, usare l'ID e la chiave specificati dal progetto nella pagina della procedura di installazione dell'agente.

All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. A seguito dell'eliminazione del progetto, l'utilizzo di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro di Log Analytics.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze usa Mapping dei servizi tramite un'area di lavoro di Log Analytics. Dal 28 febbraio 2018, con l'annuncio della disponibilità generale di Azure Migrate, la funzionalità è disponibile senza costi aggiuntivi. È necessario creare un nuovo progetto per poter usufruire dell'area di lavoro utilizzabile gratuitamente. Le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili, pertanto è consigliabile passare a un nuovo progetto.

Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Come si gestisce l'area di lavoro?

È possibile usare l'area di lavoro di Log Analytics all'esterno di Azure Migrate. L'area di lavoro non viene eliminata se si rimuove il progetto di migrazione in cui è stata creata. Se non è più necessaria, [eliminarla](../log-analytics/log-analytics-manage-access.md) manualmente.

Non eliminare l'area di lavoro creata da Azure Migrate a meno che non si rimuova il progetto di migrazione. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.

## <a name="next-steps"></a>Passaggi successivi
- [Raggruppare i computer usando le dipendenze dei computer](how-to-create-group-machine-dependencies.md)
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sulle domande frequenti sulla visualizzazione delle dipendenze.
