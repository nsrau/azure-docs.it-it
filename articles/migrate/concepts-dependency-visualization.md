---
title: Visualizzazione delle dipendenze in Azure Migrate | Microsoft Docs
description: Viene fornita una panoramica dei calcoli di valutazione nel servizio Server assessment in Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 33594e09778b9a629645e12357e6bafe561ad35e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202907"
---
# <a name="dependency-visualization"></a>Visualizzazione dipendenze

Azure Migrate: Server Assessment valuta i gruppi di computer locali per la migrazione ad Azure. Per creare i gruppi, è possibile usare la funzionalità di visualizzazione delle dipendenze in server assessment. Questo articolo contiene informazioni su questa funzionalità.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="overview"></a>Panoramica

La visualizzazione delle dipendenze in server Assessment consente di creare gruppi con attendibilità elevata per la valutazione della migrazione. Usando la visualizzazione delle dipendenze, è possibile visualizzare le dipendenze di rete dei computer e identificare i computer correlati di cui è necessario eseguire la migrazione insieme ad Azure. Questa funzionalità è utile negli scenari in cui non sono completamente compatibili con le macchine che costituiscono l'applicazione e devono essere migrate insieme ad Azure.

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) la Azure migrate: Valutazione server.
- Assicurarsi di aver individuato i computer in Azure Migrate; a tale scopo, è possibile configurare un appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: Valutazione server. [Altre informazioni](migrate-appliance.md)

## <a name="how-does-it-work"></a>Come funziona?

Azure Migrate usa la soluzione [mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) per la visualizzazione delle dipendenze.
- Per sfruttare i vantaggi della visualizzazione delle dipendenze è possibile associare un'area di lavoro Log Analytics, nuova o esistente, a un progetto di Azure Migrate.
- È possibile creare o alleghi solo un'area di lavoro nella stessa sottoscrizione in cui viene creato il progetto Azure Migrate.
- Per alleghi un'area di lavoro Log Analytics a un progetto:
    1. Nella scheda **Server**, nel riquadro **Azure Migrate: Riquadro Server** Assessment, fare clic su **Panoramica**.
    2. In **Panoramica**fare clic sulla freccia in giù per espandere **Essentials**.
    3. Nell' **area di lavoro di OMS**fare clic su **richiede la configurazione**.
    4. In **Configura area di lavoro**, specificare se si desidera creare una nuova area di lavoro oppure utilizzarne una esistente:
    
    ![Aggiungi area di lavoro](./media/how-to-create-group-machine-dependencies/workspace.png)

- Durante l'associazione di un'area di lavoro si avrà la possibilità di creare una nuova area di lavoro o di collegarne una esistente:
  - Quando si crea una nuova area di lavoro è necessario specificare un nome per essa. È possibile scegliere l' [area](https://azure.microsoft.com/global-infrastructure/regions/) in cui verrà creata l'area di lavoro.
  - Quando si collega un'area di lavoro esistente, è possibile sceglierla tra tutte le aree di lavoro disponibili nella stessa sottoscrizione del progetto di migrazione. Sono elencate solo le aree di lavoro create in un'area geografica in cui [è supportato il mapping dei servizi](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Per poter collegare un'area di lavoro è necessario avere l'accesso in lettura all'area di lavoro.

  > [!NOTE]
  > Dopo aver collegato un'area di lavoro a un progetto, non è più possibile modificarla.

  > [!NOTE]
  > Azure Migrate supporta attualmente la creazione dell'area di lavoro OMS nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale. Se l'area di lavoro viene creata all'esterno di Azure Migrate in un'altra area, attualmente non può essere associata a un progetto Azure Migrate. 

- L'area di lavoro associata è contrassegnata con la chiave **Progetto di migrazione** e con il valore **Nome progetto**, che è possibile usare per eseguire la ricerca nel portale di Azure.
- Per passare all'area di lavoro associata al progetto è possibile passare alla sezione **Essentials** della pagina **Panoramica** del progetto e accedere all'area di lavoro

    ![Navigare nell'area di lavoro Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Per usare la visualizzazione delle dipendenze, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole analizzare.  

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) deve essere installato in ogni computer. [Altre](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) informazioni su come installare l'agente MMA.
- [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) deve essere installato in ogni computer. [Altre](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) informazioni su come installare Dependency Agent.
- Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.

Questi agenti non sono necessari sui computer da valutare, a meno che non si usi la visualizzazione delle dipendenze.

## <a name="do-i-need-to-pay-for-it"></a>Il servizio è a pagamento?

La funzionalità di visualizzazione delle dipendenze è disponibile senza costi aggiuntivi. L'uso della funzionalità di visualizzazione delle dipendenze in server assessment richiede Mapping dei servizi e richiede l'associazione di un'area di lavoro Log Analytics, nuova o esistente, con il progetto Azure Migrate. La funzionalità di visualizzazione delle dipendenze in server assessment è gratuita per i primi 180 giorni.

1. Per usare una soluzione diversa da Mapping dei servizi in questa area di lavoro Log Analytics verranno applicati gli addebiti [standard di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
2. Per supportare scenari di migrazione senza costi aggiuntivi, la soluzione Mapping dei servizi non genererà addebiti per i primi 180 giorni a partire dal giorno dell'associazione dell'area di lavoro Log Analytics al progetto di Azure Migrate. Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.

Quando si registrano agenti nell'area di lavoro, usare l'ID e la chiave specificati dal progetto nella pagina della procedura di installazione dell'agente.

All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. A seguito dell'eliminazione del progetto, l'utilizzo di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro Log Analytics.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze usa Mapping dei servizi tramite un'area di lavoro Log Analytics. Dal 28 febbraio 2018, con l'annuncio della disponibilità generale di Azure Migrate, la funzionalità è disponibile senza costi aggiuntivi. È necessario creare un nuovo progetto per poter usufruire dell'area di lavoro utilizzabile gratuitamente. Le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili, pertanto è consigliabile passare a un nuovo progetto.

Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Come si gestisce l'area di lavoro?

È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate. Non viene eliminato se si elimina il progetto Azure Migrate in cui è stato creato. Se non è più necessaria, [eliminarla](../azure-monitor/platform/manage-access.md) manualmente.

Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.

## <a name="next-steps"></a>Passaggi successivi
- [Raggruppare i computer usando le dipendenze dei computer](how-to-create-group-machine-dependencies.md)
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) sulle domande frequenti sulla visualizzazione delle dipendenze.
