---
title: Ridimensionare i processi di analisi di flusso
description: Questo articolo descrive come ridimensionare automaticamente il processo di analisi di flusso in base a una pianificazione o a valori predefiniti della metrica del processo
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: 07cbb28b98fcbac1932424c1c72f388813ec2400
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037563"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Ridimensionare i processi di analisi di flusso con automazione di Azure

È possibile ottimizzare il costo dei processi di analisi di flusso configurando la scalabilità automatica. La scalabilità automatica aumenta o diminuisce le unità di streaming (SUs) del processo in base alla modifica apportata al carico di input. Anziché eseguire il provisioning eccessivo del processo, è possibile aumentare o ridurre le prestazioni in base alle esigenze. Esistono due modi per configurare i processi per la scalabilità automatica:
1. **Pre-definire una pianificazione** quando si dispone di un carico di input prevedibile. Ad esempio, si prevede una frequenza maggiore di eventi di input durante il giorno e si desidera che il processo venga eseguito con più unità di streaming.
2. **Attivare le operazioni di scalabilità verticale e verticale in base alle metriche del processo** quando non si dispone di un carico di input prevedibile. È possibile modificare dinamicamente il numero di unità di streaming in base alle metriche del processo, ad esempio il numero di eventi di input o gli eventi di input con backlog.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare la scalabilità automatica per il processo, completare i passaggi seguenti.
1. Il processo è ottimizzato per avere una [topologia parallela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Se è possibile modificare la scala del processo mentre è in esecuzione, il processo ha una topologia parallela e può essere configurato per la scalabilità automatica.
2. [Creare un account di automazione di Azure](https://docs.microsoft.com/azure/automation/automation-create-standalone-account) con l'opzione "RunAsAccount" abilitata. Questo account deve avere le autorizzazioni per gestire i processi di analisi di flusso.

## <a name="set-up-azure-automation"></a>Configurare automazione di Azure
### <a name="configure-variables"></a>Configura variabili
Aggiungere le variabili seguenti all'interno dell'account di automazione di Azure. Queste variabili verranno usate nella manuali operativi descritta nei passaggi successivi.

| Nome | Type | valore |
| --- | --- | --- |
| **jobName** | string | Nome del processo di analisi di flusso che si vuole ridimensionare automaticamente. |
| **resourceGroupName** | string | Nome del gruppo di risorse in cui è presente il processo. |
| **subId** | string | ID sottoscrizione in cui è presente il processo. |
| **increasedSU** | Integer | Il valore di SU superiore a cui si vuole ridimensionare il processo in una pianificazione. Questo valore deve corrispondere a una delle opzioni di SU valide visualizzate nelle impostazioni di **scalabilità** del processo mentre è in esecuzione. |
| **decreasedSU** | Integer | Valore di SU inferiore a cui si vuole applicare la scalabilità del processo in una pianificazione. Questo valore deve corrispondere a una delle opzioni di SU valide visualizzate nelle impostazioni di **scalabilità** del processo mentre è in esecuzione. |
| **maxSU** | Integer | Il valore massimo SU cui si vuole ridimensionare il processo nei passaggi durante la scalabilità automatica in base al carico. Questo valore deve corrispondere a una delle opzioni di SU valide visualizzate nelle impostazioni di **scalabilità** del processo mentre è in esecuzione. |
| **minSU** | Integer | Il valore di SU minimo a cui si vuole ridimensionare il processo nei passaggi durante la scalabilità automatica in base al carico. Questo valore deve corrispondere a una delle opzioni di SU valide visualizzate nelle impostazioni di **scalabilità** del processo mentre è in esecuzione. |

![Aggiungere variabili in automazione di Azure](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Creare runbook
Il passaggio successivo consiste nel creare due manuali operativi di PowerShell. Uno per la scalabilità verticale e l'altro per le operazioni di ridimensionamento.
1. Nell'account di automazione di Azure passare a **manuali operativi** in **automazione processi** e selezionare **Crea Runbook**.
2. Denominare il primo *ScaleUpRunbook* runbook con il tipo impostato su PowerShell. Usare lo [script di PowerShell ScaleUpRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) disponibile in GitHub. Salvarlo e pubblicarlo.
3. Creare un altro Runbook denominato *ScaleDownRunbook* con il tipo PowerShell. Usare lo [script di PowerShell ScaleDownRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) disponibile in GitHub. Salvarlo e pubblicarlo.

![Ridimensionamento automatico manuali operativi in automazione di Azure](./media/autoscale/runbooks.png)

A questo punto si dispone di manuali operativi in grado di attivare automaticamente le operazioni di scalabilità verticale e verticale nel processo di analisi di flusso. Questi manuali operativi possono essere attivati usando una pianificazione predefinita oppure possono essere impostati in modo dinamico in base alle metriche del processo.

## <a name="autoscale-based-on-a-schedule"></a>Ridimensionare automaticamente in base a una pianificazione
Automazione di Azure consente di configurare una pianificazione per attivare la manuali operativi.
1. Nell'account di automazione di Azure selezionare **pianificazioni** in **risorse condivise**. Selezionare quindi **Aggiungi pianificazione**.
2. Ad esempio, è possibile creare due pianificazioni. Uno che rappresenta quando si desidera che il processo venga scalato in verticale e un altro che rappresenta quando si desidera che il processo venga ridimensionato. È possibile definire una ricorrenza per queste pianificazioni.

   ![Pianificazioni in Automazione di Azure](./media/autoscale/schedules.png)

3. Aprire il **ScaleUpRunbook** e quindi selezionare **pianificazioni** in **risorse**. È quindi possibile collegare il Runbook a una pianificazione creata nei passaggi precedenti. È possibile avere più pianificazioni collegate con lo stesso Runbook, che può essere utile quando si desidera eseguire la stessa operazione di ridimensionamento in momenti diversi del giorno.

![Pianificazione di manuali operativi in automazione di Azure](./media/autoscale/schedulerunbook.png)

1. Ripetere il passaggio precedente per **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Ridimensionamento automatico in base al carico
Potrebbero esserci casi in cui non è possibile stimare il carico di input. In questi casi, è più ottimale applicare la scalabilità verso l'alto o verso il basso in passaggi entro un limite minimo e massimo. È possibile configurare le regole di avviso nei processi di analisi di flusso per attivare manuali operativi quando le metriche del processo vanno oltre una soglia.
1. Nell'account di automazione di Azure creare altre due variabili integer denominate **mins** e **maxSU**. Consente di impostare i limiti entro i quali il processo verrà ridimensionato in passaggi.
2. Creare due nuovi manuali operativi. È possibile usare lo [script di PowerShell StepScaleUp](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) che aumenta il valore di SUs del processo in incrementi fino al valore di **maxSU** . È anche possibile usare lo [script di PowerShell StepScaleDown](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) che riduce il SUs del processo nei passaggi fino a quando non viene raggiunto il valore **mins** . In alternativa, è possibile usare manuali operativi della sezione precedente se sono presenti valori di unità di streaming specifici a cui si vuole applicare la scalabilità.
3. Nel processo di analisi di flusso selezionare **le regole di avviso** in **monitoraggio**. 
4. Creare due gruppi di azioni. Una da usare per l'operazione di scalabilità verticale e un'altra per l'operazione di riduzione. Selezionare **Gestisci azioni** e quindi fare clic su **Aggiungi gruppo di azioni**. 
5. Compilare i campi obbligatori. Scegliere **Runbook di automazione** quando si seleziona il **tipo di azione**. Selezionare il Runbook che si desidera attivare quando viene attivato l'avviso. Quindi, creare il gruppo di azioni.

   ![Creare un gruppo di azioni](./media/autoscale/create-actiongroup.png)
6. Creare una [**nuova regola di avviso**](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) nel processo. Specificare una condizione in base a una metrica di propria scelta. Per la definizione della logica di scalabilità automatica sono consigliate le metriche per gli eventi di [ *input*, l' *utilizzo su%* o *gli eventi di input con backlog* ](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics) . È inoltre consigliabile utilizzare la *granularità* e la *frequenza di valutazione di* 1 minuto quando si attivano le operazioni di scalabilità verticale. In questo modo si garantisce che il processo disponga di risorse sufficienti per gestire picchi elevati nel volume di input.
7. Selezionare il gruppo di azioni creato nell'ultimo passaggio e creare l'avviso.
8. Ripetere i passaggi da 2 a 4 per qualsiasi operazione di ridimensionamento aggiuntiva che si vuole attivare in base alla condizione delle metriche del processo.

È consigliabile eseguire i test di scalabilità prima di eseguire il processo nell'ambiente di produzione. Quando si esegue il test del processo in base a diversi carichi di input, si ha la necessità di comprendere il numero di unità di streaming necessarie per la velocità effettiva di input. Questo può indicare le condizioni definite nelle regole di avviso che attivano le operazioni di scalabilità verticale e verticale. 

## <a name="next-steps"></a>Passaggi successivi
* [Create parallelizable queries in Azure Stream Analytics](stream-analytics-parallelization.md) (Creare query eseguibili in parallelo in Analisi di flusso di Azure)
* [Ridimensionare processi di analisi di flusso di Azure per aumentare la velocità effettiva](stream-analytics-scale-jobs.md)
