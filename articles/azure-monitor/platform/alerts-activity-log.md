---
title: Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di Azure
description: Creare avvisi del log di attività usando il portale di Azure, un modello Azure Resource Manager e Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705285"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log attività usando monitoraggio di Azure  

## <a name="overview"></a>Panoramica
Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso.

Questi avvisi sono per le risorse di Azure e possono essere creati usando un modello Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In genere, si creano attività gli avvisi del log di ricevere notifiche quando si verificano modifiche specifiche per le risorse nella sottoscrizione di Azure. Gli avvisi sono spesso limitati a risorse o gruppi di risorse specifico. Potrebbe ad esempio, si desidera ricevere una notifica quando qualsiasi macchina virtuale nel gruppo di risorse di esempio **myProductionResourceGroup** viene eliminato. In alternativa, si potrebbe voler ricevere una notifica se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.

> [!IMPORTANT]
> Gli avvisi nella notifica di integrità del servizio non è possibile creare tramite l'interfaccia per la creazione dell'avviso del log attività. Per altre informazioni su come creare e usare le notifiche sull'integrità del servizio, vedere [ricevere gli avvisi del log attività nelle notifiche del servizio integrità](alerts-activity-log-service-notifications.md).

Quando si creano le regole di avviso, verificare quanto segue:

- La sottoscrizione nell'ambito non è diversa dalla sottoscrizione in cui viene creato l'avviso.
- I criteri devono essere il livello, stato, chiamante, gruppo di risorse, ID di risorsa o categoria di eventi di tipo risorsa in cui viene configurato l'avviso.
- Non sono condizioni "anyOf" o condizioni nidificate nella configurazione degli avvisi JSON. In pratica, è consentita solo una condizione "allOf" senza ulteriore "allOf" o condizioni "anyOf".
- Quando la categoria è impostata su "amministrativa", è necessario specificare almeno uno dei criteri precedenti nell'avviso. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.


## <a name="azure-portal"></a>Portale di Azure

È possibile usare il portale di Azure per creare e modificare le regole di avviso del log attività. L'esperienza è integrato con un log attività di Azure per garantire facile la creazione dell'avviso per eventi specifici di interesse.

### <a name="create-with-the-azure-portal"></a>Creare con il portale di Azure

Utilizzare la procedura seguente.

1. Nel portale di Azure, selezionare **Monitor** > **avvisi**.
2. Selezionare **nuova regola di avviso** nell'angolo superiore sinistro della **avvisi** finestra.

     ![Nuova regola di avviso](media/alerts-activity-log/AlertsPreviewOption.png)

     Viene visualizzata la finestra **Crea regola**.

      ![nuove opzioni di regola di avviso](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Sotto **definire una condizione di avviso**, specificare le informazioni seguenti e selezionare **eseguita**:

   - **Destinazione avviso:** Per visualizzare e selezionare la destinazione per il nuovo avviso, usare **Filtra per sottoscrizione** / **Filtra per tipo di risorsa**. Selezionare la risorsa o gruppo di risorse dall'elenco visualizzato.

     > [!NOTE]
     > 
     > È possibile selezionare una risorsa, gruppo di risorse o un'intera sottoscrizione per un segnale del log attività.

     **Visualizzazione di esempio di destinazione di avviso**

     ![Selezionare la destinazione](media/alerts-activity-log/select-target.png)

   - Sotto **criteri di destinazione**, selezionare **Aggiungi criteri**. Vengono visualizzati tutti i segnali disponibili per la destinazione, inclusi quelli appartenenti alle categorie diverse di **Log attività**. Il nome della categoria viene aggiunto per il **servizio di monitoraggio** nome.

   - Selezionare il segnale nell'elenco visualizzato delle varie operazioni possibili per il tipo di **Log attività**.

     È possibile selezionare la sequenza temporale della cronologia di log e la logica degli avvisi corrispondente per il segnale di destinazione:

     **Schermata Aggiungi criteri**

     ![Aggiungi criteri](media/alerts-activity-log/add-criteria.png)

     - **Periodo cronologia**: È possibile tracciare gli eventi disponibili per l'operazione selezionata nelle ultime 6, 12 o 24 ore o nell'ultima settimana.

     - **Logica avvisi**:

       - **Livello evento**: Il livello di gravità dell'evento: Dettagliato, _Informativo_ _Avviso_, _Errore_ o _Critico_.
       - **Stato**: Lo stato dell'evento: Avviato, _Operazione non riuscita_ o _Operazione completata_.
       - **Evento avviato da**: Noto anche come il chiamante. L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

       Questo grafico del segnale di esempio include la logica avvisi applicata:

       ![criteri selezionati](media/alerts-activity-log/criteria-selected.png)

4. Sotto **definire i dettagli dell'avviso**, fornire i dettagli seguenti:

    - **Nome regola di avviso**: Il nome della nuova regola di avviso.
    - **Descrizione**: Descrizione per la nuova regola di avviso.
    - **Salva avviso nel gruppo di risorse**: Selezionare il gruppo di risorse in cui si desidera salvare la nuova regola.

5. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo di azione](../../azure-monitor/platform/action-groups.md) e assegnarlo alla nuova regola. Per creare un nuovo gruppo, selezionare **+ nuovo gruppo**.

6. Per abilitare le regole dopo averli creati, selezionare **Yes** per il **Abilita regola alla creazione** opzione.
7. Selezionare **Crea regola di avviso**.

    Viene creata la nuova regola di avviso per il log attività e un messaggio di conferma viene visualizzato nell'angolo superiore destro della finestra.

    È possibile abilitare, disabilitare, modificare o eliminare una regola. Altre informazioni su come gestire regole del log attività.


Una semplice analogia per le condizioni di comprensione in cui le regole di avviso possono essere create in un log attività consiste nell'esplorare o filtrare gli eventi tramite il [log attività nel portale di Azure](activity-log-view.md#azure-portal). Nel **monitoraggio di Azure - log attività** dello schermo, è possibile filtrare o cercare l'evento necessario e quindi creare un avviso usando la **Aggiungi avviso del log attività** pulsante. Seguire quindi i passaggi da 4 a 7 come precedentemente illustrati.
    
 ![aggiungere un avviso dal log attività](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visualizzare e gestire nel portale di Azure

1. Nel portale di Azure, selezionare **Monitor** > **avvisi**. Selezionare **gestire le regole di avviso** nell'angolo superiore sinistro della finestra.

    ![Gestire le regole di avviso](media/alerts-activity-log/manage-alert-rules.png)

    Viene visualizzato l'elenco delle regole disponibili.

2. Cercare la regola del log attività da modificare.

    ![Cerca regole di avviso del log attività](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    È possibile usare i filtri disponibili _abbonamento_, _gruppo di risorse_, _risorsa_, _dal tipo di segnale_, o _stato_ per trovare la regola dell'attività che si desidera modificare.

   > [!NOTE]
   > 
   > È possibile modificare solo **Description**, **criteri di destinazione**, e **gruppi di azioni**.

3. Selezionare la regola e fare doppio clic per modificare le opzioni delle regole. Apportare le modifiche necessarie e quindi selezionare **salvare**.

   ![Gestire le regole di avviso](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. È possibile abilitare, disabilitare o eliminare una regola. Dopo aver selezionato la regola, come descritto nel passaggio 2, selezionare l'opzione appropriata nella parte superiore della finestra.


## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Per creare un avviso del log attività usando un modello di Azure Resource Manager, si crea una risorsa del tipo `microsoft.insights/activityLogAlerts`. Compilare quindi tutte le proprietà correlate. Ecco un modello che crea un avviso del log attività:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
L'esempio precedente JSON possono essere salvati come, ad esempio, sampleActivityLogAlert.json ai fini di questa procedura dettagliata e possono essere distribuite tramite [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Potrebbe richiedere fino a 5 minuti per il nuovo log regola di avviso attività diventi attivo.

## <a name="rest-api"></a>API REST 
Il [API di avvisi del Log attività di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) è un'API REST. È completamente compatibile con il REST API di Azure Resource Manager. Può essere utilizzato tramite PowerShell usando il cmdlet di Resource Manager o il comando di Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuire il modello di Resource Manager con PowerShell
Usare PowerShell per distribuire il modello di Resource Manager di esempio illustrato nella precedente [modello di Azure Resource Manager](#azure-resource-manager-template) sezione, usare il comando seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

in cui il sampleActivityLogAlert.parameters.json contiene i valori specificati per i parametri necessari per la creazione della regola di avviso.

### <a name="use-activity-log-powershell-cmdlets"></a>Log attività di usare i cmdlet di PowerShell

Per gli avvisi del log attività sono disponibili cmdlet di PowerShell dedicati:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Crea un nuovo avviso del log attività o aggiorna un avviso esistente del log attività.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Ottiene uno o più attività risorse degli avvisi del log.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Abilita un avviso del log attività esistenti e imposta i tag.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Disabilita un avviso del log attività esistenti e imposta i tag.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Rimuove un avviso del log attività.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per la gestione delle regole di avviso del log attività sono disponibili comandi dedicati dell'interfaccia della riga di comando di Azure nel set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert).

Per creare una nuova regola avviso del log attività, usare i comandi seguenti nell'ordine indicato:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Creare una nuova risorsa di regola di avviso del log attività.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Aggiungi ambito per la regola di avviso di log di attività creata.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Aggiungere un gruppo di azioni per la regola di avviso del log attività.

Per recuperare una risorsa di regola di avviso del log attività, usare il comando di Azure CLI [az monitor log attività Mostra](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Per visualizzare tutte le risorse di regola di avviso del log attività in un gruppo di risorse, usare [elenco degli avvisi del log attività di monitoraggio az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Risorse di regola di avviso del log attività possono essere rimossi usando il comando di Azure CLI [eliminazione degli avvisi del log attività di monitoraggio az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Passaggi successivi

- Scopri [sullo schema webhook per i log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Lettura un' [panoramica dei log attività](../../azure-monitor/platform/activity-log-alerts.md).
- Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).  
- Informazioni sulle [notifiche per l'integrità del servizio](../../azure-monitor/platform/service-notifications.md).
