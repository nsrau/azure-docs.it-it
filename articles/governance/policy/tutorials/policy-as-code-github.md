---
title: 'Esercitazione: Implementare Criteri di Azure come codice con GitHub'
description: Questa esercitazione illustra come implementare un flusso di lavoro di Criteri di Azure come codice con il comando di esportazione, le azioni GitHub e i flussi di lavoro GitHub
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325872"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Esercitazione: Implementare Criteri di Azure come codice con GitHub

Un flusso di lavoro di Criteri di Azure come codice consente di gestire le definizioni e le assegnazioni dei criteri come codice, controllare il ciclo di vita dell'aggiornamento di tali definizioni e automatizzare la convalida dei risultati di conformità. In questa esercitazione si apprenderà come usare le funzionalità di Criteri di Azure con GitHub per creare un processo del ciclo di vita. Queste attività includono:

> [!div class="checklist"]
> - Esportare le definizioni e le assegnazioni dei criteri in GitHub
> - Eseguire il push degli oggetti criteri aggiornati in GitHub ad Azure
> - Attivare un'analisi di conformità dall'azione GitHub

Se si vuole assegnare un criterio per identificare lo stato di conformità corrente delle risorse esistenti, gli articoli di avvio rapido spiegano come fare.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Vedere [Progettare flussi di lavoro di Criteri di Azure come codice](../concepts/policy-as-code.md) per informazioni sugli schemi progettuali usati in questa esercitazione.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Esportare oggetti Criteri di Azure dal portale di Azure

Per esportare una definizione di criteri dal portale di Azure, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri** .

1. Selezionare **Definizioni** a sinistra nella pagina Criteri di Azure.

1. Usare il pulsante **Esporta definizioni** oppure selezionare i puntini di sospensione nella riga di una definizione di criteri, quindi selezionare **Esporta definizione** .

1. Selezionare il pulsante **Accedi con GitHub** . Se non è ancora stata eseguita l'autenticazione con GitHub per autorizzare Criteri di Azure a esportare la risorsa, verificare di quale accesso ha bisogno l' [azione GitHub](https://github.com/features/actions) nella nuova finestra visualizzata e selezionare **Autorizza AzureGitHubActions** per continuare con il processo di esportazione. Al termine, la nuova finestra si chiude automaticamente.

1. Nella scheda **Informazioni di base** impostare le opzioni seguenti, quindi selezionare la scheda **Criteri** o il pulsante **Avanti: Criteri** nella parte inferiore della pagina.

   - **Filtro per repository** : impostare su _Repository personali_ per visualizzare solo i repository di cui si è proprietari o su _Tutti i repository_ per visualizzare tutti i repository a cui è stato concesso l'accesso all'azione GitHub.
   - **Repository** : impostare sul repository in cui si vogliono esportare le risorse di Criteri di Azure.
   - **Ramo** : impostare il ramo nel repository. L'uso di un ramo diverso da quello predefinito è un modo efficace per convalidare gli aggiornamenti prima di eseguire ulteriori unioni nel codice sorgente.
   - **Directory** : _cartella di livello radice_ in cui esportare le risorse di Criteri di Azure. Le sottocartelle di questa directory vengono create in base alle risorse esportate.

1. Nella scheda **Criteri** impostare l'ambito per la ricerca selezionando i puntini di sospensione e scegliendo una combinazione di gruppi di gestione, sottoscrizioni o gruppi di risorse.
   
1. Usare il pulsante **Aggiungi definizione/i dei criteri** per cercare nell'ambito gli oggetti da esportare. Nella finestra che si aprirà a lato selezionare ogni oggetto da esportare. Filtrare la selezione in base alla casella di ricerca o al tipo. Dopo aver selezionato tutti gli oggetti da esportare, usare il pulsante **Aggiungi** in fondo alla pagina.

1. Per ogni oggetto selezionato selezionare le opzioni di esportazione desiderate, ad esempio _Solo definizione_ o _Definizione e assegnazione/i_ , per una definizione di criteri. Selezionare quindi la scheda **Rivedi ed esporta** o il pulsante **Avanti: Rivedi ed esporta** nella parte inferiore della pagina.

   > [!NOTE]
   > Se si sceglie l'opzione _Definizione e assegnazione/i_ , vengono esportate solo le assegnazioni dei criteri all'interno dell'ambito impostato dal filtro al momento dell'aggiunta della definizione di criteri.

1. Nella scheda **Rivedi ed esporta** controllare che i dettagli corrispondano e quindi usare il pulsante **Esporta** nella parte inferiore della pagina.

1. Controllare il repository GitHub, il ramo e la _cartella di livello radice_ per verificare che le risorse selezionate siano state esportate nel controllo del codice sorgente.

Le risorse di Criteri di Azure vengono esportate nella struttura seguente all'interno del repository GitHub selezionato e della _cartella di livello radice_ :

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Eseguire il push degli oggetti criteri aggiornati in GitHub ad Azure

1. Al momento dell'esportazione di oggetti criteri, viene creato anche un file di [flusso di lavoro GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) denominato `.github/workflows/manage-azure-policy-<randomLetters>.yml` per iniziare.

   > [!NOTE]
   > Il file di flusso di lavoro GitHub viene creato ogni volta che si usa il comando di esportazione. Ogni istanza del file è specifica delle opzioni durante l'azione di esportazione.

1. Questo file di flusso di lavoro usa l'azione [Gestisci Criteri di Azure](https://github.com/marketplace/actions/manage-azure-policy) per eseguire di nuovo in Criteri di Azure il push delle modifiche apportate agli oggetti criteri esportati nel repository GitHub. Per impostazione predefinita, l'azione considera e sincronizza solo i file diversi da quelli esistenti in Azure. Si può anche usare il parametro `assignments` nell'azione per sincronizzare solo le modifiche apportate a file di assegnazione specifici. Questo parametro può essere usato per applicare le assegnazioni di criteri solo per un ambiente specifico. Per altre informazioni, vedere il [file Leggimi sull'azione di gestione di Criteri di Azure](https://github.com/Azure/manage-azure-policy).

1. Per impostazione predefinita, il flusso di lavoro deve essere attivato manualmente. A questo scopo, usare **Actions** in GitHub, selezionare il flusso di lavoro `manage-azure-policy-<randomLetters>`, selezionare **Run workflow** (Esegui flusso di lavoro) e quindi di nuovo **Run workflow** .

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Screenshot dell'interfaccia Web di GitHub con la scheda Actions, il flusso di lavoro e il pulsante Run workflow evidenziati.":::

   > [!NOTE]
   > Il file di flusso di lavoro deve trovarsi nel ramo predefinito per poter essere rilevato ed eseguito manualmente.

1. Il flusso di lavoro sincronizza le modifiche apportate agli oggetti criteri con Azure e restituisce lo stato nei log.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Screenshot dell'interfaccia Web di GitHub con la scheda Actions, il flusso di lavoro e il pulsante Run workflow evidenziati.":::

1. Il flusso di lavoro aggiunge anche dettagli in `properties.metadata` degli oggetti di Criteri di Azure per consentire di tenerne traccia.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Screenshot dell'interfaccia Web di GitHub con la scheda Actions, il flusso di lavoro e il pulsante Run workflow evidenziati.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Attivare le analisi di conformità con l'azione GitHub

Usando l'[azione di analisi della conformità di Criteri di Azure](https://github.com/marketplace/actions/azure-policy-compliance-scan) è possibile attivare un'analisi di valutazione della conformità su richiesta dal [flusso di lavoro di GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) su una o più risorse, gruppi di risorse o sottoscrizioni e modificare il percorso del flusso di lavoro in base allo stato di conformità di tali risorse. Si può anche configurare il flusso di lavoro per l'esecuzione a un'ora pianificata per ottenere lo stato di conformità più recente in un momento appropriato. Facoltativamente, questa azione GitHub può anche generare un report sullo stato di conformità delle risorse analizzate per un'ulteriore analisi o a scopo di archiviazione.

L'esempio seguente esegue un'analisi della conformità per una sottoscrizione. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Verifica

In questa esercitazione sono state eseguite le attività seguenti:

> [!div class="checklist"]
> - Sono state esportate le definizioni e le assegnazioni dei criteri in GitHub
> - È stato eseguito il push degli oggetti criteri aggiornati in GitHub ad Azure
> - È stata attivata un'analisi di conformità dall'azione GitHub

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle strutture delle definizioni di criteri, vedere:

> [!div class="nextstepaction"]
> [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md)