---
title: Distribuire monitoraggio di Azure
description: Distribuire le funzionalità di monitoraggio di Azure su larga scala usando criteri di Azure.
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 4be403f8efc8e328548b6ef38b36be78a8fb96d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678699"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Distribuire monitoraggio di Azure su larga scala usando criteri di Azure
Mentre alcune funzionalità di monitoraggio di Azure sono configurate una volta o un numero limitato di volte, altre devono essere ripetute per ogni risorsa che si vuole monitorare. Questo articolo descrive i metodi per l'uso di criteri di Azure per implementare monitoraggio di Azure su larga scala per garantire che il monitoraggio sia configurato in modo coerente e accurato per tutte le risorse di Azure.

Ad esempio, è necessario creare un'impostazione di diagnostica per tutte le risorse di Azure esistenti e per ogni nuova risorsa creata. È inoltre necessario che un agente sia installato e configurato ogni volta che si crea una macchina virtuale. È possibile usare metodi come PowerShell o l'interfaccia della riga di comando per eseguire queste azioni, perché questi metodi sono disponibili per tutte le funzionalità di monitoraggio di Azure. Utilizzando criteri di Azure, è possibile applicare la logica che eseguirà automaticamente la configurazione appropriata ogni volta che si crea o si modifica una risorsa.


## <a name="azure-policy"></a>Criteri di Azure
Questa sezione fornisce una breve introduzione a [criteri di Azure](../../governance/policy/overview.md) che consente di valutare e applicare gli standard aziendali nell'intera sottoscrizione o gruppo di gestione di Azure con il minimo sforzo. Per informazioni dettagliate, vedere la [documentazione di criteri di Azure](../../governance/policy/overview.md) .

Con criteri di Azure è possibile specificare i requisiti di configurazione per tutte le risorse create e identificare le risorse non conformi, bloccare la creazione delle risorse o aggiungere la configurazione richiesta. Funziona intercettando le chiamate per creare una nuova risorsa o per modificare una risorsa esistente. Può rispondere con tali effetti come negare la richiesta se non corrisponde alle proprietà previste in una definizione dei criteri, contrassegnarla per la mancata conformità o distribuire una risorsa correlata. È possibile correggere le risorse esistenti con una **deployIfNotExists** o **modificare** la definizione dei criteri.

Criteri di Azure è costituito dagli oggetti nella tabella seguente. Per una spiegazione più dettagliata di ognuno, vedere [oggetti Criteri di Azure](../../governance/policy/overview.md#azure-policy-objects) .

| Elemento | Descrizione |
|:---|:---|
| Definizione di criteri | Descrive le condizioni di conformità delle risorse e l'effetto da eseguire se viene soddisfatta una condizione. Questo può essere costituito da tutte le risorse di un determinato tipo o solo da risorse che corrispondono a determinate proprietà. L'effetto potrebbe consistere nel contrassegnare semplicemente la risorsa per la conformità o per distribuire una risorsa correlata. Le definizioni dei criteri vengono scritte usando JSON come descritto in [struttura di definizione di criteri di Azure](../../governance/policy/concepts/definition-structure.md). Gli effetti sono descritti in [comprendere gli effetti dei criteri di Azure](../../governance/policy/concepts/effects.md).
| Iniziativa di criteri | Gruppo di definizioni dei criteri da applicare insieme. Ad esempio, è possibile avere una definizione dei criteri per inviare i log delle risorse a un'area di lavoro di Log Analytics e un'altra per inviare i log delle risorse a hub eventi. Creare un'iniziativa che includa le definizioni dei criteri e applicare l'iniziativa alle risorse anziché alle singole definizioni dei criteri. Le iniziative vengono scritte usando JSON come descritto in [struttura di Azure Policy Initiative](../../governance/policy/concepts/initiative-definition-structure.md). |
| Assegnazione | Una definizione o un'iniziativa di criteri non diventa effettiva fino a quando non viene assegnata a un ambito. Ad esempio, assegnare un criterio a un gruppo di risorse per applicarlo a tutte le risorse create in tale risorsa o applicarlo a una sottoscrizione per applicarlo a tutte le risorse nella sottoscrizione.  Per altri dettagli, vedere [struttura di assegnazione dei criteri di Azure](../../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Definizioni di criteri predefiniti per Monitoraggio di Azure
Criteri di Azure include diverse definizioni predefinite correlate a monitoraggio di Azure. È possibile assegnare queste definizioni dei criteri alla sottoscrizione esistente o utilizzarle come base per creare definizioni personalizzate. Per un elenco completo delle politiche incorporate nella categoria **monitoraggio** , vedere [definizioni dei criteri predefiniti di criteri di Azure per monitoraggio di Azure](../policy-samples.md).

Per visualizzare le definizioni dei criteri predefinite correlate al monitoraggio, eseguire le operazioni seguenti:

1. Passare a **criteri di Azure** nella portale di Azure.
2. Selezionare le **definizioni**.
3. Per **tipo**selezionare *predefinito* e per **categoria**, selezionare *monitoraggio*.

  ![Definizioni dei criteri predefiniti](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
[Le impostazioni di diagnostica](../platform/diagnostic-settings.md) raccolgono i log delle risorse e le metriche dalle risorse di Azure a più posizioni, in genere in un'area di lavoro log Analytics che consente di analizzare i dati con le [query di log](../log-query/log-query-overview.md) e gli [avvisi del log](alerts-log.md). Usare i criteri per creare automaticamente un'impostazione di diagnostica ogni volta che si crea una risorsa.

Ogni tipo di risorsa di Azure dispone di un set univoco di categorie che devono essere elencate nell'impostazione di diagnostica. Per questo motivo, per ogni tipo di risorsa è necessaria una definizione di criteri separata. Alcuni tipi di risorse dispongono di definizioni di criteri predefinite che è possibile assegnare senza modifiche. Per altri tipi di risorse, è necessario creare una definizione personalizzata.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Definizioni di criteri predefiniti per Monitoraggio di Azure
Sono disponibili due definizioni di criteri predefinite per ogni tipo di risorsa, una da inviare all'area di lavoro Log Analytics e un'altra a hub eventi. Se è necessaria una sola posizione, assegnare il criterio per il tipo di risorsa. Se sono necessari entrambi, assegnare entrambe le definizioni dei criteri per la risorsa.

Nell'immagine seguente, ad esempio, vengono illustrate le definizioni dei criteri predefiniti per le impostazioni di diagnostica per Data Lake Analytics.

  ![Definizioni dei criteri predefiniti](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Definizioni dei criteri personalizzati
Per i tipi di risorsa che non dispongono di un criterio predefinito, è necessario creare una definizione di criteri personalizzata. Questa operazione può essere eseguita manualmente nel portale di Azure copiando un criterio predefinito esistente e quindi modificando per il tipo di risorsa. È tuttavia più efficiente creare il criterio a livello usando uno script nella PowerShell Gallery.

Lo script [create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) crea i file di criteri per un tipo di risorsa specifico che è possibile installare usando PowerShell o l'interfaccia della riga di comando. Utilizzare la procedura seguente per creare una definizione dei criteri personalizzata per le impostazioni di diagnostica.


1. Assicurarsi che [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) sia installato.
2. Installare lo script con il comando seguente:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Eseguire lo script usando i parametri per specificare dove inviare i log. Verrà richiesto di specificare una sottoscrizione e un tipo di risorsa. Ad esempio, per creare una definizione dei criteri che invii a Log Analytics area di lavoro e a hub eventi, usare il comando seguente.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. In alternativa, è possibile specificare una sottoscrizione e un tipo di risorsa nel comando. Ad esempio, per creare una definizione dei criteri che invii a Log Analytics area di lavoro e a hub eventi per i database di SQL Server di Azure, usare il comando seguente.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Lo script crea cartelle separate per ogni definizione di criterio, ognuna contenente tre file denominati azurepolicy, JSON, azurepolicy.rules.json azurepolicy.parameters.json. Se si desidera creare il criterio manualmente nella portale di Azure, è possibile copiare e incollare il contenuto di azurepolicy.jsin poiché include l'intera definizione di criteri. Usare gli altri due file con PowerShell o l'interfaccia della riga di comando per creare la definizione dei criteri da una riga di comando.

    Gli esempi seguenti illustrano come installare la definizione dei criteri da PowerShell e dall'interfaccia della riga di comando. Ogni include i metadati per specificare una categoria di **monitoraggio** per raggruppare la nuova definizione dei criteri con le definizioni dei criteri predefinite.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Iniziativa
Anziché creare un'assegnazione per ogni definizione di criterio, una strategia comune consiste nel creare un'iniziativa che includa le definizioni dei criteri per creare le impostazioni di diagnostica per ogni servizio di Azure. Creare un'assegnazione tra l'iniziativa e un gruppo di gestione, una sottoscrizione o un gruppo di risorse a seconda della modalità di gestione dell'ambiente. Questa strategia offre i vantaggi seguenti:

- Creare una singola assegnazione per l'iniziativa anziché più assegnazioni per ogni tipo di risorsa. Usare la stessa iniziativa per più gruppi di monitoraggio, sottoscrizioni o gruppi di risorse.
- Modificare l'iniziativa quando è necessario aggiungere un nuovo tipo di risorsa o destinazione. Ad esempio, i requisiti iniziali potrebbero essere l'invio di dati solo a un'area di lavoro Log Analytics, ma in un secondo momento si vuole aggiungere Hub eventi. Modificare l'iniziativa anziché creare nuove assegnazioni.

Per informazioni dettagliate sulla creazione di un'iniziativa, vedere [creare e assegnare una definizione di iniziativa](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) . Considerare le seguenti indicazioni:

- Impostare la **categoria** su **monitoraggio** per raggrupparla con le definizioni di criteri predefinite e personalizzate correlate.
- Anziché specificare i dettagli per l'area di lavoro Log Analytics e l'hub eventi per la definizione dei criteri inclusa nell'iniziativa, usare un parametro Initiative comune. In questo modo è possibile specificare facilmente un valore comune per tutte le definizioni dei criteri e modificare il valore se necessario.

![Definizione di iniziativa](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Assegnazione 
Assegnare l'iniziativa a un gruppo di gestione di Azure, a una sottoscrizione o a un gruppo di risorse a seconda dell'ambito delle risorse da monitorare. Un [gruppo di gestione](../../governance/management-groups/overview.md) è particolarmente utile per i criteri di ambito, soprattutto se l'organizzazione dispone di più sottoscrizioni.

![Assegnazione di iniziativa](media/deploy-scale/initiative-assignment.png)

Utilizzando i parametri Initiative, è possibile specificare l'area di lavoro o qualsiasi altro dettaglio una volta per tutte le definizioni dei criteri nell'iniziativa. 

![Parametri delle iniziative](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Soluzione
L'iniziativa verrà applicata a ogni macchina virtuale creata. Un' [attività di correzione](../../governance/policy/how-to/remediate-resources.md) distribuisce le definizioni dei criteri nell'iniziativa alle risorse esistenti, in modo che sia possibile creare le impostazioni di diagnostica per tutte le risorse già create. Quando si crea l'assegnazione usando il portale di Azure, è possibile creare un'attività di correzione nello stesso momento. Per informazioni dettagliate sulla correzione, vedere [correggere le risorse non conformi con i criteri di Azure](../../governance/policy/how-to/remediate-resources.md) .

![Monitoraggio e aggiornamento delle iniziative](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) è lo strumento principale di monitoraggio di Azure per il monitoraggio delle macchine virtuali. L'abilitazione di Monitoraggio di Azure per le macchine virtuali installa sia l'agente Log Analytics che Dependency Agent. Anziché eseguire manualmente queste attività, usare criteri di Azure per assicurarsi che ogni macchina virtuale sia configurata durante la creazione.

Monitoraggio di Azure per le macchine virtuali include due iniziative predefinite, denominate **enable monitoraggio di Azure per le macchine virtuali** e **Enable monitoraggio di Azure per i set di scalabilità di macchine virtuali**. Queste iniziative includono un set di definizioni dei criteri necessari per installare l'agente di Log Analytics e l'agente di dipendenza necessario per l'abilitazione di Monitoraggio di Azure per le macchine virtuali. 

Anziché creare assegnazioni per queste iniziative usando l'interfaccia di criteri di Azure, Monitoraggio di Azure per le macchine virtuali include una funzionalità che consente di controllare il numero di macchine virtuali in ogni ambito per determinare se l'iniziativa è stata applicata. È quindi possibile configurare l'area di lavoro e creare le assegnazioni richieste usando tale interfaccia.

Per informazioni dettagliate su questo processo, vedere [abilitare monitoraggio di Azure per le macchine virtuali usando criteri di Azure](../insights/vminsights-enable-at-scale-policy.md).

![Criteri di Monitoraggio di Azure per le macchine virtuali](../platform/media/deploy-scale/vminsights-policy.png)


## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su [criteri di Azure](../../governance/policy/overview.md).
- Altre informazioni sulle [impostazioni di diagnostica](diagnostic-settings.md).
