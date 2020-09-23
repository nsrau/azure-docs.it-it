---
title: Creare attività di automazione per gestire e monitorare le risorse di Azure
description: Configurare attività automatiche che consentono di gestire le risorse di Azure e monitorare i costi creando flussi di lavoro eseguiti in app per la logica di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 93c796fd16dde8c238265d16a96b9cfa4a254ea9
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996338"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Gestire le risorse di Azure e monitorare i costi creando attività di automazione (anteprima)

> [!IMPORTANT]
> Questa funzionalità è disponibile in anteprima pubblica, viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per semplificare la gestione [delle risorse di Azure](../azure-resource-manager/management/overview.md#terminology) , è possibile creare attività di gestione automatizzate per una risorsa o un gruppo di risorse specifico usando i modelli di attività di automazione, che variano in base al tipo di risorsa. Per un [account di archiviazione di Azure](../storage/common/storage-account-overview.md), ad esempio, è possibile configurare un'attività di automazione che invii il costo mensile per l'account di archiviazione. Per una [macchina virtuale di Azure](https://azure.microsoft.com/services/virtual-machines/), è possibile creare un'attività di automazione che attiva o disattiva tale macchina virtuale in base a una pianificazione predefinita.

Dietro le quinte, un'attività di automazione è in realtà un flusso di lavoro in esecuzione nel servizio app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e viene fatturato con le stesse [tariffe tariffarie](https://azure.microsoft.com/pricing/details/logic-apps/) e il [modello di determinazione prezzi](../logic-apps/logic-apps-pricing.md). Dopo aver creato l'attività, è possibile visualizzare e modificare il flusso di lavoro sottostante aprendo l'attività nella finestra di progettazione dell'app per la logica. Quando un'attività completa almeno un'esecuzione, è possibile esaminare lo stato, la cronologia, gli input e gli output per ogni esecuzione.

Ecco i modelli di attività attualmente disponibili in questa versione di anteprima:

| Tipo di risorsa | Modelli di attività di automazione |
|---------------|---------------------------|
| Gruppi di risorse di Azure | **Quando viene eliminata la risorsa** |
| In tutte le risorse di Azure | **Invia costo mensile per la risorsa** |
| Macchine virtuali di Azure | Inoltre: <p>- **Spegnere la macchina virtuale** <br>- **Avvia macchina virtuale** |
| Account di archiviazione di Azure | Inoltre: <p>- **Elimina BLOB obsoleti** |
|||

Questo articolo illustra come completare le attività seguenti:

* [Creare un'attività di automazione](#create-automation-task) per una risorsa di Azure specifica.

* [Esaminare la cronologia di un'attività](#review-task-history), che include lo stato di esecuzione, gli input, gli output e altre informazioni cronologiche.

* [Modificare l'attività](#edit-task) in modo da poter aggiornare l'attività o personalizzare il flusso di lavoro sottostante dell'attività nella finestra di progettazione dell'app per la logica.

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Differenze tra le attività di automazione e automazione di Azure

Attualmente, è possibile creare un'attività di automazione solo a livello di risorsa, visualizzare la cronologia delle esecuzioni dell'attività e modificare il flusso di lavoro dell'app per la logica sottostante dell'attività, che è alimentato dal servizio app per la [logica di Azure](../logic-apps/logic-apps-overview.md) .

[Automazione di Azure](../automation/automation-intro.md) è un servizio di automazione e configurazione basato sul cloud che supporta una gestione coerente negli ambienti Azure e non Azure. Il servizio include l' [automazione dei processi per l'orchestrazione dei processi](../automation/automation-intro.md#process-automation) tramite [manuali operativi](../automation/automation-runbook-execution.md), la gestione della configurazione con [rilevamento delle modifiche e l'inventario](../automation/change-tracking.md), la gestione degli aggiornamenti, le funzionalità condivise e le funzionalità eterogenee. Automazione fornisce il controllo completo durante la distribuzione, l'attività e la rimozione delle autorizzazioni di carichi di lavoro e risorse.

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Risorsa di Azure che si vuole gestire. Questo articolo usa come esempio un account di archiviazione di Azure.

* Un account Office 365 se si vuole seguire l'esempio, che invia un messaggio di posta elettronica usando Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Creare un'attività di automazione

1. Nella [portale di Azure](https://portal.azure.com)trovare la risorsa che si vuole gestire.

1. Nel menu delle risorse scorrere fino alla sezione **automazione** e selezionare **attività**

   ![Screenshot che mostra il portale di Azure e un menu delle risorse dell'account di archiviazione in cui la voce di menu "attività" è selezionata nella sezione "automazione".](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. Nel riquadro **attività** selezionare **Aggiungi** per poter selezionare un modello di attività.

   ![Screenshot che mostra il riquadro "attività" dell'account di archiviazione in cui è selezionata la barra degli strumenti "Aggiungi"](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. In **Seleziona un modello**nel riquadro **Aggiungi un'attività** selezionare il modello per l'attività che si desidera creare e selezionare **Avanti: autenticazione**.

   Questo esempio continua selezionando il modello di attività **Invia costo mensile per le risorse** .

   ![Screenshot che mostra le selezioni, "Invia il costo mensile per la risorsa" e "Avanti: autenticazione"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. In **autenticazione**, nella sezione **connessioni** Selezionare **Crea** per ogni connessione, in modo da poter fornire le credenziali di autenticazione per la connessione. I tipi di connessioni in ogni attività variano in base all'attività.

   ![Screenshot che mostra l'opzione "Crea" selezionata per la connessione Azure Resource Manager](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Quando viene richiesto, accedere con le credenziali dell'account Azure.

   ![Screenshot che mostra la selezione, "Accedi"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Ogni connessione autenticata correttamente ha un aspetto simile all'esempio seguente:

   ![Screenshot che mostra la connessione creata correttamente](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Dopo aver autenticato tutte le connessioni necessarie, selezionare **Avanti: configurazione**.

1. In **configurazione**specificare un nome per l'attività e qualsiasi altra informazione necessaria per l'attività. Al termine, selezionare **Crea**.

   > [!NOTE]
   > Non è possibile modificare il nome dell'attività dopo la creazione, quindi considerare un nome che si applica ancora se si [modifica il flusso di lavoro sottostante](#edit-task-workflow). Le modifiche apportate al flusso di lavoro sottostante si applicano solo all'attività creata, non al modello di attività.
   >
   > Ad esempio, se si rinomina l'attività `Send monthly cost` , ma in seguito si modifica il flusso di lavoro sottostante per l'esecuzione settimanale, non è possibile modificare il nome dell'attività in `Send weekly cost` .

   Le attività che inviano notifiche tramite posta elettronica richiedono un indirizzo di posta elettronica.

   ![Screenshot che mostra le informazioni necessarie per l'attività selezionata](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   L'attività creata, che è automaticamente attiva e in esecuzione, viene ora visualizzata nell'elenco **attività di automazione** .

   ![Screenshot che mostra l'elenco attività di automazione](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Se l'attività non viene visualizzata immediatamente, provare ad aggiornare l'elenco attività o attendere un po' prima di eseguire l'aggiornamento. Sulla barra degli strumenti selezionare **Aggiorna**.

   Dopo l'esecuzione dell'attività selezionata, viene visualizzato un messaggio di posta elettronica simile all'esempio seguente:

   ![Screenshot che mostra la notifica tramite posta elettronica inviata dall'attività](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Esaminare la cronologia delle attività

Per visualizzare la cronologia di un'attività delle esecuzioni insieme ai relativi stati, input, output e altre informazioni, attenersi alla procedura seguente:

1. Nella [portale di Azure](https://portal.azure.com)trovare la risorsa con la cronologia attività che si desidera esaminare.

1. Nel menu della risorsa, in **Impostazioni**, selezionare **attività di automazione**.

1. Nell'elenco attività trovare l'attività che si desidera esaminare. Nella colonna **esecuzioni** di tale attività selezionare **Visualizza**.

   ![Screenshot che mostra un'attività e l'opzione "View" selezionata](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Il riquadro **Cronologia esecuzioni** Mostra tutte le esecuzioni per l'attività insieme ai relativi stati, ora di inizio, identificatori e durate di esecuzione.

   ![Screenshot che mostra le esecuzioni di un'attività, i relativi stati e altre informazioni](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Di seguito sono riportati i possibili stati per un'esecuzione:

   | Stato | Descrizione |
   |--------|-------------|
   | **Annullato** | L'attività è stata annullata durante l'esecuzione. |
   | **Non riuscito** | Nell'attività è presente almeno un'azione non riuscita, ma non è stata eseguita alcuna azione successiva per gestire l'errore. |
   | **Running** | L'attività è attualmente in esecuzione. |
   | **Completato** | Tutte le azioni hanno avuto esito positivo. Un'attività può comunque terminare correttamente se un'azione non è riuscita, ma esisteva un'azione successiva per gestire l'errore. |
   | **Attesa** | L'esecuzione non è stata ancora avviata e viene sospesa perché un'istanza precedente dell'attività è ancora in esecuzione. |
   |||

   Per altre informazioni, vedere [cronologia delle esecuzioni di revisione](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Per visualizzare gli Stati e altre informazioni per ogni passaggio di un'esecuzione, selezionare Esegui.

   Viene aperto il riquadro **esecuzione App** per la logica che mostra il flusso di lavoro sottostante che è stato eseguito.

   * Un flusso di lavoro inizia sempre con un [*trigger*](../connectors/apis-list.md#triggers-actions). Per questa attività, il flusso di lavoro inizia con il trigger di [ **ricorrenza** ](../connectors/connectors-native-recurrence.md).

   * Ogni passaggio Mostra lo stato e la durata dell'esecuzione. Per l'esecuzione di passaggi con durate di 0 secondi sono necessari meno di un secondo.

   ![Screenshot che mostra ogni passaggio dell'esecuzione, dello stato e della durata dell'esecuzione](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Per esaminare gli input e gli output per ogni passaggio, selezionare il passaggio, che consente di espandersi.

   Questo esempio Mostra gli input per il trigger di ricorrenza, che non ha output perché il trigger specifica solo quando viene eseguito il flusso di lavoro e non fornisce output per le azioni successive da elaborare.

   ![Screenshot che mostra il trigger espanso e gli input](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Al contrario, l'azione **Invia un messaggio di posta elettronica** ha input da azioni precedenti nel flusso di lavoro e negli output.

   ![Screenshot che mostra un'azione espansa, input e output](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Per informazioni su come creare flussi di lavoro automatizzati personalizzati in modo che sia possibile integrare app, dati, servizi e sistemi oltre al contesto delle attività di automazione per le risorse di Azure, vedere [Guida introduttiva: creare il primo flusso di lavoro di integrazione usando app per la logica di Azure-portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Modificare l'attività

Per modificare un'attività, sono disponibili le opzioni seguenti:

* [Modificare l'attività "inline"](#edit-task-inline) in modo che sia possibile modificare le proprietà dell'attività, ad esempio le informazioni di connessione o le informazioni di configurazione, ad esempio l'indirizzo di posta elettronica.

* [Modificare il flusso di lavoro sottostante dell'attività](#edit-task-workflow) nella finestra di progettazione dell'app per la logica.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Modificare l'attività inline

1. Nella [portale di Azure](https://portal.azure.com)individuare la risorsa che contiene l'attività che si desidera aggiornare.

1. In **automazione**selezionare **attività**dal menu della risorsa.

1. Nell'elenco attività trovare l'attività che si desidera aggiornare. Aprire il menu puntini di sospensione (**..**.) dell'attività e selezionare **modifica inline**.

   ![Screenshot che mostra il menu puntini di sospensione aperti e l'opzione selezionata "modifica inline"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Per impostazione predefinita, viene visualizzata la scheda **autenticazione** con le connessioni esistenti.

1. Per aggiungere nuove credenziali di autenticazione o selezionare credenziali di autenticazione esistenti diverse per una connessione, aprire il menu puntini di sospensione (**..**.) della connessione e selezionare **Aggiungi nuova connessione** o, se disponibili, credenziali di autenticazione diverse.

   ![Screenshot che mostra la scheda autenticazione, le connessioni esistenti e il menu puntini di sospensione selezionati](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Per aggiornare altre proprietà dell'attività, selezionare **Avanti: configurazione**.

   Per l'attività in questo esempio, l'unica proprietà disponibile per la modifica è l'indirizzo di posta elettronica.

   ![Screenshot che mostra la scheda di configurazione](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Al termine, selezionare **Salva**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Modificare il flusso di lavoro sottostante dell'attività

Quando si modifica il flusso di lavoro sottostante per un'attività di automazione, le modifiche interessano solo l'istanza di attività creata e non il modello che crea l'attività. Dopo aver apportato e salvato le modifiche, il nome specificato per l'attività originale potrebbe non descrivere accuratamente l'attività, quindi potrebbe essere necessario ricreare l'attività con un nome diverso.

> [!TIP]
> Come procedura consigliata, clonare il flusso di lavoro sottostante in modo che sia possibile modificare la versione copiata. In questo modo, è possibile eseguire e testare le modifiche nella copia mentre l'attività di automazione originale continua a funzionare ed essere eseguita senza rischiare interruzioni o interrompere le funzionalità esistenti. Dopo aver completato le modifiche e aver verificato che la nuova versione venga eseguita correttamente, è possibile disabilitare o eliminare l'attività di automazione originale e usare la versione clonata per l'attività di automazione. I passaggi seguenti includono informazioni su come clonare il flusso di lavoro.

1. Nella [portale di Azure](https://portal.azure.com)individuare la risorsa che contiene l'attività che si desidera aggiornare.

1. In **automazione**selezionare **attività**dal menu della risorsa.

1. Nell'elenco attività trovare l'attività che si desidera aggiornare. Aprire il menu puntini di sospensione (**..**.) dell'attività e selezionare **Apri in app per la logica**.

   ![Screenshot che mostra il menu puntini di sospensione aperti e l'opzione selezionata "Apri in app per la logica"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Il flusso di lavoro sottostante dell'attività viene aperto nel servizio app per la logica di Azure e Mostra il riquadro **Panoramica** in cui è possibile visualizzare la stessa cronologia delle esecuzioni disponibile per l'attività.

   ![Screenshot che mostra l'attività nella visualizzazione app per la logica di Azure con il riquadro Panoramica selezionato](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Per aprire il flusso di lavoro sottostante nella finestra di progettazione dell'app per la logica, scegliere **progettazione app**per la logica dal menu dell'app per la logica.

   ![Screenshot che mostra l'opzione di menu "progettazione app per la logica" selezionata e l'area di progettazione con il flusso di lavoro sottostante](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   È ora possibile modificare le proprietà per il trigger e le azioni del flusso di lavoro, nonché per modificare il trigger e le azioni che definiscono il flusso di lavoro stesso. Tuttavia, come procedura consigliata, seguire i passaggi per clonare il flusso di lavoro in modo che sia possibile apportare le modifiche in una copia mentre il flusso di lavoro originale continua a funzionare.

1. Per clonare il flusso di lavoro e modificare invece la versione copiata, attenersi alla seguente procedura:

   1. Nel menu flusso di lavoro dell'app per la logica selezionare **Panoramica**.

   1. Nella barra degli strumenti del riquadro Panoramica selezionare **clona**.

   1. Nel riquadro di creazione dell'app per la logica, in **nome**, immettere un nuovo nome per il flusso di lavoro dell'app per la logica copiato.

      Ad eccezione **dello stato dell'app**per la logica, le altre proprietà non sono disponibili per la modifica. 
      
   1. In **stato app**per la logica selezionare **disabilitato** in modo che il flusso di lavoro clonato non venga eseguito mentre si apportano le modifiche. È possibile abilitare il flusso di lavoro quando si è pronti per testare le modifiche.

   1. Al termine del provisioning del flusso di lavoro clonato, il flusso di lavoro viene individuato e aperto nella finestra di progettazione dell'app per la logica.

1. Per visualizzare le proprietà per il trigger o un'azione, espandere il trigger o l'azione.

   Ad esempio, è possibile modificare il trigger di ricorrenza per l'esecuzione settimanale, anziché mensile.

   ![Screenshot che mostra il trigger di ricorrenza espansa con l'elenco di frequenza aperto per mostrare le opzioni di frequenza disponibili](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Per altre informazioni sul trigger di ricorrenza, vedere [creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md). Per altre informazioni su altri trigger e azioni che è possibile usare, vedere [connettori per app per la logica di Azure](../connectors/apis-list.md).

1. Per salvare le modifiche, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   ![Screenshot che mostra la barra degli strumenti della finestra di progettazione e il comando "Save" selezionato](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Per testare ed eseguire il flusso di lavoro aggiornato, sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**.

   Al termine dell'esecuzione, nella finestra di progettazione vengono visualizzati i dettagli dell'esecuzione del flusso di lavoro.

   ![Screenshot che mostra i dettagli dell'esecuzione del flusso di lavoro nella finestra di progettazione](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Per disabilitare il flusso di lavoro in modo che l'attività non continui l'esecuzione, vedere [gestire le app per la logica nella portale di Azure](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Inviare feedback

Ci piacerebbe sapere. Per segnalare bug, fornire commenti e suggerimenti o porre domande su questa funzionalità di anteprima, [contattare il team di app per la logica di Azure](mailto:logicapps@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire app per la logica nella portale di Azure](../logic-apps/manage-logic-apps-with-azure-portal.md)
