---
title: Abilitare Monitoraggio di Azure per le macchine virtuali con Criteri di Azure
description: Viene descritto come abilitare Monitoraggio di Azure per le macchine virtuali per più macchine virtuali di Azure o set di scalabilità di macchine virtuali usando criteri di Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 2c292ee601114a58e38b9e509efa53be2d3c93d6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328245"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Abilitare Monitoraggio di Azure per le macchine virtuali con Criteri di Azure
Questo articolo illustra come abilitare Monitoraggio di Azure per le macchine virtuali per macchine virtuali di Azure o macchine virtuali ibride connesse con Azure Arc (anteprima) usando criteri di Azure. Criteri di Azure consente di assegnare le definizioni dei criteri che installano gli agenti necessari per Monitoraggio di Azure per le macchine virtuali nell'ambiente Azure e abilitano automaticamente il monitoraggio per le macchine virtuali durante la creazione di ogni macchina virtuale. Monitoraggio di Azure per le macchine virtuali offre una funzionalità che consente di individuare e correggere le macchine virtuali non conformi nell'ambiente in uso. Usare questa funzionalità anziché lavorare direttamente con criteri di Azure.

Se non si ha familiarità con i criteri di Azure, è possibile ottenere una breve introduzione alla [distribuzione di monitoraggio di Azure su larga scala usando criteri di Azure](../platform/deploy-scale.md).

> [!NOTE]
> Per usare i criteri di Azure con i set di scalabilità di macchine virtuali di Azure o per usare direttamente i criteri di Azure per abilitare le macchine virtuali di Azure, vedere [distribuire monitoraggio di Azure su larga scala con criteri di Azure](../platform/deploy-scale.md#azure-monitor-for-vms-and-virtual-machine-agents).

## <a name="prerequisites"></a>Prerequisiti
- [Creare e configurare un'area di lavoro log Analytics](vminsights-configure-workspace.md).
- Vedere [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo della macchina virtuale o del set di scalabilità di macchine virtuali che si sta abilitando sia supportato. 


## <a name="azure-monitor-for-vms-initiative"></a>Monitoraggio di Azure per le macchine virtuali Initiative
Monitoraggio di Azure per le macchine virtuali fornisce le definizioni dei criteri predefiniti per installare l'agente di Log Analytics e l'agente di dipendenza nelle macchine virtuali di Azure. L'iniziativa **Enable monitoraggio di Azure per le macchine virtuali** include ognuna di queste definizioni di criteri. Assegnare questa iniziativa a un gruppo di gestione, a una sottoscrizione o a un gruppo di risorse per installare automaticamente gli agenti in qualsiasi macchina virtuale Windows o Linux di Azure in tale ambito.

## <a name="open-policy-coverage-feature"></a>Apri la funzionalità copertura criteri
Per accedere **monitoraggio di Azure per le macchine virtuali copertura dei criteri**, passare alle **macchine virtuali** nel menu **monitoraggio di Azure** nel portale di Azure. Selezionare **altre opzioni di onboarding** e quindi **abilitare** in **Abilita utilizzando i criteri**.

[![Scheda Introduzione a monitoraggio di Azure dalla macchina virtuale](./media/vminsights-enable-at-scale-policy/get-started-page.png)](./media/vminsights-enable-at-scale-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Crea nuova assegnazione
Se non si dispone già di un'assegnazione, crearne una nuova facendo clic su **assegna criterio**.

[![Crea assegnazione](media/vminsights-enable-at-scale-policy/create-assignment.png)](media/vminsights-enable-at-scale-policy/create-assignment.png#lightbox)

Si tratta della stessa pagina che consente di assegnare un'iniziativa in criteri di Azure, ad eccezione del fatto che è hardcoded con l'ambito selezionato e con la definizione **Enable monitoraggio di Azure per le macchine virtuali** Initiative. Facoltativamente, è possibile modificare il **nome dell'assegnazione** e aggiungere una **Descrizione**. Selezionare **esclusioni** se si desidera fornire un'esclusione per l'ambito. Ad esempio, l'ambito può essere un gruppo di gestione ed è possibile specificare una sottoscrizione in tale gruppo di gestione da escludere dall'assegnazione.

[![Assegna iniziativa](media/vminsights-enable-at-scale-policy/assign-initiative.png)](media/vminsights-enable-at-scale-policy/assign-initiative.png#lightbox)

Nella pagina **parametri** selezionare un'area di **lavoro log Analytics** che verrà utilizzata da tutte le macchine virtuali nell'assegnazione. Se si desidera specificare diverse aree di lavoro per diverse macchine virtuali, è necessario creare più assegnazioni, ognuna con il proprio ambito. 

   > [!NOTE]
   > Se l'area di lavoro non rientra nell'ambito dell'assegnazione, concedere le autorizzazioni di *Collaboratore di Log Analytics* all'ID entità di sicurezza dell'assegnazione dei criteri. Se non si esegue questa operazione, è possibile che venga visualizzato un errore di distribuzione come`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Area di lavoro](media/vminsights-enable-at-scale-policy/assignment-workspace.png)](media/vminsights-enable-at-scale-policy/assignment-workspace.png#lightbox)

Fare clic su **Verifica + crea** per esaminare i dettagli dell'assegnazione prima di fare clic su **Crea** per crearla. In questo momento non è possibile creare un'attività di correzione poiché è molto probabile che siano necessarie più attività di monitoraggio e aggiornamento per abilitare le macchine virtuali esistenti. Vedere [correggere i risultati di conformità](#remediate-compliance-results) di seguito.

## <a name="review-compliance"></a>Verifica conformità
Una volta creata un'assegnazione, è possibile esaminare e gestire la copertura per **abilitare monitoraggio di Azure per le macchine virtuali** Initiative nei gruppi di gestione e nelle sottoscrizioni. Verrà visualizzato il numero di macchine virtuali presenti in ognuno dei gruppi di gestione o delle sottoscrizioni e il relativo stato di conformità.

[![Pagina di gestione dei criteri di Monitoraggio di Azure per le macchine virtuali](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png#lightbox)


Nella tabella seguente viene fornita una descrizione delle informazioni contenute in questa vista.

| Funzione | Descrizione | 
|----------|-------------| 
| **Ambito** | Gruppo di gestione e sottoscrizioni a cui è stato o ereditato l'accesso con la possibilità di eseguire il drill-down attraverso la gerarchia del gruppo di gestione.|
| **Ruolo** | Ruolo nell'ambito, che può essere Reader, Owner o collaboratore. Questa operazione sarà vuota se si ha accesso alla sottoscrizione ma non al gruppo di gestione a cui appartiene. Questo ruolo determina i dati che è possibile visualizzare e le azioni che è possibile eseguire in termini di assegnazione di criteri o iniziative (proprietario), modifica o visualizzazione della conformità. |
| **Totale macchine virtuali** | Numero totale di macchine virtuali in tale ambito indipendentemente dallo stato. Per un gruppo di gestione, si tratta di una somma totale delle VM annidate nelle sottoscrizioni o nei gruppi di gestione figlio. |
| **Code coverage assegnazione** | Percentuale di macchine virtuali coperte dall'iniziativa. |
| **Stato di assegnazione** | **Operazione completata** : tutte le macchine virtuali nell'ambito dispongono degli agenti di log Analytics e dipendenza distribuiti.<br>**Avviso** : la sottoscrizione non è inclusa in un gruppo di gestione.<br>**Non avviato** : è stata aggiunta una nuova assegnazione.<br>**Lock** : non si dispone di privilegi sufficienti per il gruppo di gestione.<br>**Blank** : non esiste alcuna macchina virtuale o non è stato assegnato alcun criterio. |
| **Macchine virtuali conformi** | Numero di macchine virtuali conformi, ovvero il numero di macchine virtuali in cui sono installati sia l'agente Log Analytics che l'agente di dipendenza. Questa operazione sarà vuota se non sono presenti assegnazioni, nessuna VM nell'ambito o autorizzazioni non corrette. |
| **Conformità** | Il numero di conformità complessivo è la somma di risorse distinte conformi, divise per la somma di tutte le risorse distinte. |
| **Stato di conformità** | **Conforme** : tutte le macchine virtuali nelle macchine virtuali dell'ambito hanno gli agenti di log Analytics e di dipendenza distribuiti o qualsiasi nuova VM nell'ambito soggetto all'assegnazione non è stata ancora valutata.<br>**Non conforme** : sono presenti VM che sono state valutate, ma che non sono abilitate e che potrebbero richiedere la correzione.<br>**Non avviato** : è stata aggiunta una nuova assegnazione.<br>**Lock** : non si dispone di privilegi sufficienti per il gruppo di gestione.<br>**Blank** -Nessun criterio assegnato.  |


Quando si assegna l'iniziativa, l'ambito selezionato nell'assegnazione potrebbe essere l'ambito elencato o un sottoinsieme. Ad esempio, è possibile che sia stata creata un'assegnazione per una sottoscrizione (ambito dei criteri) e non un gruppo di gestione (ambito di code coverage). In questo caso, il valore di **code coverage assegnazione** indica le VM nell'ambito Initiative divise dalle macchine virtuali nell'ambito di code coverage. In un altro caso, è possibile che siano state escluse alcune macchine virtuali, gruppi di risorse o una sottoscrizione dall'ambito dei criteri. Se il valore è vuoto, significa che il criterio o l'iniziativa non esiste oppure non si dispone dell'autorizzazione. Le informazioni vengono fornite in **stato di assegnazione**.


## <a name="remediate-compliance-results"></a>Correggere i risultati di conformità
L'iniziativa verrà applicata alle macchine virtuali Man mano che vengono create o modificate, ma non verrà applicata alle macchine virtuali esistenti. Se l'assegnazione non Mostra la conformità del 100%, creare attività di monitoraggio e aggiornamento per valutare e abilitare le macchine virtuali esistenti, selezionare **Visualizza conformità** selezionando i puntini di sospensione (...).

[![Visualizza conformità](media/vminsights-enable-at-scale-policy/view-compliance.png)](media/vminsights-enable-at-scale-policy/view-compliance.png#lightbox)

Nella pagina **conformità** sono elencate le assegnazioni corrispondenti al filtro specificato e al fatto che siano conformi. Fare clic su un'assegnazione per visualizzarne i dettagli.

[![Conformità dei criteri per le macchine virtuali di Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png#lightbox)

Nella pagina **conformità Initiative** sono elencate le definizioni dei criteri nell'iniziativa e se ognuna è conforme.

[![Dettagli di conformità](media/vminsights-enable-at-scale-policy/compliance-details.png)](media/vminsights-enable-at-scale-policy/compliance-details.png#lightbox)

Fare clic su una definizione dei criteri per visualizzarne i dettagli. Di seguito sono riportati gli scenari in cui le definizioni dei criteri vengono visualizzate come non conformi:

* Log Analytics Agent o Dependency Agent non è stato distribuito. Creare un'attività di monitoraggio e aggiornamento per attenuare i rischi.
* L'immagine di macchina virtuale (sistema operativo) non è identificata nella definizione dei criteri. I criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato.
* Le macchine virtuali non si registrano nell'area di lavoro Log Analytics specificata. Alcune VM nell'ambito dell'iniziativa sono connesse a un'area di lavoro Log Analytics diversa da quella specificata nell'assegnazione dei criteri.

[![Dettagli di conformità dei criteri](media/vminsights-enable-at-scale-policy/policy-compliance-details.png)](media/vminsights-enable-at-scale-policy/policy-compliance-details.png#lightbox)

Per creare un'attività di monitoraggio e aggiornamento per attenuare i problemi di conformità, fare clic su **Crea attività di correzione**. 

[![Nuova attività di monitoraggio e aggiornamento](media/vminsights-enable-at-scale-policy/new-remediation-task.png)](media/vminsights-enable-at-scale-policy/new-remediation-task.png#lightbox)

Fare **clic su** Correggi per creare l'attività di monitoraggio e aggiornamento e quindi **correggere** per avviarla. Probabilmente sarà necessario creare più attività di correzione, una per ogni definizione di criteri. Non è possibile creare un'attività di correzione per un'iniziativa.

[![Soluzione](media/vminsights-enable-at-scale-policy/remediation.png)](media/vminsights-enable-at-scale-policy/remediation.png#lightbox)


Una volta completate le attività di correzione, le macchine virtuali devono essere conformi agli agenti installati e abilitati per Monitoraggio di Azure per le macchine virtuali. 

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali. 

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 
- Per identificare i colli di bottiglia e l'utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni delle VM di Azure](vminsights-performance.md). 
