---
title: Visualizzazione delle dipendenze in Azure Migrate
description: Viene fornita una panoramica dei calcoli di valutazione nel servizio Server assessment in Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 17ba06d6ac09f220b4343092292275a1cc315377
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489206"
---
# <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

Questo articolo descrive la funzionalità di visualizzazione delle dipendenze in Azure Migrate: server assessment.

La visualizzazione delle dipendenze consente di comprendere le dipendenze tra i computer che si desidera valutare e migrare. In genere si usa il mapping delle dipendenze quando si desidera valutare i computer con livelli di confidenza più elevati.

- In Azure Migrate: server Assessment, i computer vengono raccolti in gruppi per la valutazione. I gruppi sono in genere costituiti da computer di cui si vuole eseguire la migrazione insieme e la visualizzazione delle dipendenze consente di controllare le dipendenze dei computer, in modo che sia possibile raggruppare i computer in modo accurato.
- Con la visualizzazione è possibile individuare i sistemi interdipendenti che devono essere migrati insieme. È possibile stabilire se i sistemi in esecuzione sono ancora in uso o se è possibile rimuovere le autorizzazioni per i sistemi anziché migrare.
- La visualizzazione delle dipendenze contribuisce a garantire che non venga lasciato nulla ed evitare interruzioni delle sorprese durante la migrazione.
- Questa funzionalità è particolarmente utile se non si è in grado di riconoscere completamente i computer che fanno parte delle app e pertanto è necessario eseguirne la migrazione insieme ad Azure.


> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="agent-based-and-agentless"></a>Basata su agenti e senza agenti

Sono disponibili due opzioni per la distribuzione della visualizzazione delle dipendenze:

- **Visualizzazione delle dipendenze senza agente**: questa opzione è attualmente in anteprima. Non richiede l'installazione di agenti nei computer. 
    - L'operazione viene eseguita acquisendo i dati di connessione TCP da computer per i quali è abilitata. [Altre informazioni](how-to-create-group-machine-dependencies-agentless.md).
Dopo l'avvio dell'individuazione delle dipendenze, l'appliance raccoglie i dati dai computer a un intervallo di polling di cinque minuti.
    - Vengono raccolti i dati seguenti:
        - Connessioni TCP
        - Nomi dei processi con connessioni attive
        - Nomi delle applicazioni installate che eseguono i processi precedenti
        - No. delle connessioni rilevate a ogni intervallo di polling
- **Visualizzazione delle dipendenze basate su agente**: per usare la visualizzazione di dipendenza basata su agenti, è necessario scaricare e installare gli agenti seguenti in ogni computer locale che si vuole analizzare.  
    - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) deve essere installato in ogni computer. [Altre](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) informazioni su come installare l'agente MMA.
    - [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) deve essere installato in ogni computer. [Altre](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) informazioni su come installare Dependency Agent.
    - Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.

## <a name="agent-based-requirements"></a>Requisiti basati su agenti

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Cosa è necessario per distribuire la visualizzazione delle dipendenze?

Prima di distribuire la visualizzazione delle dipendenze, è necessario disporre di un progetto Azure Migrate, con lo strumento Azure Migrate: server Assessment aggiunto al progetto. La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali.

[Altre](how-to-assess.md) informazioni sull'aggiunta dello strumento e sulla distribuzione di un'appliance per i server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)o fisici.


### <a name="how-does-it-work"></a>Come funziona?

Azure Migrate usa la soluzione [mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) per la visualizzazione delle dipendenze.

- Per sfruttare la visualizzazione delle dipendenze, è necessario associare un'area di lavoro Log Analytics (nuova o esistente) con un progetto Azure Migrate.
- L'area di lavoro deve trovarsi nella stessa sottoscrizione in cui si crea il progetto Azure Migrate.
- Azure Migrate supporta le aree di lavoro che risiedono nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto. Si noti inoltre che l'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta.
- In Log Analytics, l'area di lavoro associata a Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.

    ![Navigare nell'area di lavoro Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Il servizio è a pagamento?

Per la visualizzazione delle dipendenze è necessario Mapping dei servizi e un'area di lavoro Log Analytics associata. 

- La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni. Questo è il giorno in cui è stata associata l'area di lavoro Log Analytics al progetto Azure Migrate.
- Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.
- L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà addebiti [log Analytics standard](https://azure.microsoft.com/pricing/details/log-analytics/) .
- All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, Mapping dei servizi utilizzo non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro.

Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Se sono presenti progetti creati prima di Azure Migrate disponibilità generale nel 28 febbraio 2018, è possibile che vengano addebitati costi aggiuntivi per l'Mapping dei servizi. Per assicurarsi di pagare solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili.



### <a name="how-do-i-manage-the-workspace"></a>Come si gestisce l'area di lavoro?

- Quando si registrano gli agenti nell'area di lavoro, è possibile utilizzare l'ID e la chiave forniti dal progetto Azure Migrate.
- È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.
- Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. È necessario [eliminarlo manualmente](../azure-monitor/platform/manage-access.md).
- Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.

## <a name="next-steps"></a>Passaggi successivi
- [Raggruppare i computer usando le dipendenze dei computer](how-to-create-group-machine-dependencies.md)
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) sulle domande frequenti sulla visualizzazione delle dipendenze.


