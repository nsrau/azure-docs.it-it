---
title: Creare, visualizzare e gestire gli avvisi del log attività in monitoraggio di Azure
description: Creare avvisi del log attività usando il portale di Azure, un modello di Azure Resource Manager e Azure PowerShell.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 06/25/2019
ms.openlocfilehash: 6c133f99ce31d4994753aba11cee90cfc1b5afc9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552808"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log attività tramite monitoraggio di Azure  

## <a name="overview"></a>Panoramica
Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso.

Questi avvisi sono per le risorse di Azure e possono essere creati usando un modello di Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In genere, si creano avvisi del log attività per ricevere notifiche quando si verificano modifiche specifiche alle risorse nella sottoscrizione di Azure. Gli avvisi sono spesso limitati a risorse o gruppi di risorse specifici. Ad esempio, potrebbe essere necessario ricevere una notifica quando viene eliminata una macchina virtuale nel gruppo di risorse di esempio **myProductionResourceGroup** . In alternativa, potrebbe essere necessario ricevere una notifica se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.

> [!IMPORTANT]
> Non è possibile creare avvisi relativi alla notifica sull'integrità del servizio tramite l'interfaccia per la creazione di avvisi del log attività. Per ulteriori informazioni su come creare e utilizzare le notifiche sull'integrità del servizio, vedere [ricevere gli avvisi del log attività per le notifiche sull'integrità del servizio](alerts-activity-log-service-notifications.md).

Quando si creano regole di avviso, verificare quanto segue:

- La sottoscrizione nell'ambito non è diversa dalla sottoscrizione in cui viene creato l'avviso.
- I criteri devono essere il livello, lo stato, il chiamante, il gruppo di risorse, l'ID risorsa o la categoria di eventi del tipo di risorsa in cui è configurato l'avviso.
- Non esiste alcuna condizione "anyOf" o condizioni annidate nel file JSON di configurazione dell'avviso. Fondamentalmente, è consentita una sola condizione "allOf" senza ulteriori condizioni "allOf" o "anyOf".
- Quando la categoria è "amministrativa", è necessario specificare almeno uno dei criteri precedenti nell'avviso. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.


## <a name="azure-portal"></a>Portale di Azure

È possibile usare la portale di Azure per creare e modificare le regole di avviso del log attività. L'esperienza è integrata con un log attività di Azure per garantire la creazione di avvisi senza problemi per eventi specifici di interesse.

### <a name="create-with-the-azure-portal"></a>Creare con la portale di Azure

Utilizzare la procedura riportata di seguito.

1. Nella portale di Azure selezionare **monitoraggio**  > **avvisi**.
2. Selezionare **nuova regola di avviso** nell'angolo superiore sinistro della finestra **avvisi** .

     ![Nuova regola di avviso](media/alerts-activity-log/AlertsPreviewOption.png)

     Viene visualizzata la finestra **Crea regola**.

      ![Nuove opzioni della regola di avviso](media/alerts-activity-log/create-new-alert-rule-options.png)

3. In **Definisci condizione di avviso**, specificare le informazioni seguenti e selezionare **fine**:

   - **Destinazione avviso:** Per visualizzare e selezionare la destinazione per il nuovo avviso, usare **Filtra per sottoscrizione**  / **filtrare per tipo di risorsa**. Selezionare la risorsa o il gruppo di risorse dall'elenco visualizzato.

     > [!NOTE]
     > 
     > È possibile selezionare solo [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) risorsa rilevata, il gruppo di risorse o un'intera sottoscrizione per un segnale del log attività. 

     **Visualizzazione di esempio di destinazione di avviso**

     ![Selezionare la destinazione](media/alerts-activity-log/select-target.png)

   - In **criteri di destinazione**selezionare **Aggiungi criteri**. Vengono visualizzati tutti i segnali disponibili per la destinazione, inclusi quelli di diverse categorie di **log attività**. Il nome della categoria viene aggiunto al nome del **servizio di monitoraggio** .

   - Selezionare il segnale nell'elenco visualizzato delle varie operazioni possibili per il tipo di **Log attività**.

     È possibile selezionare la sequenza temporale della cronologia di log e la logica degli avvisi corrispondente per il segnale di destinazione:

     **Schermata Aggiungi criteri**

     ![Aggiungi criteri](media/alerts-activity-log/add-criteria.png)

     - **Tempo cronologia**: gli eventi disponibili per l'operazione selezionata possono essere tracciati nelle ultime 6, 12 o 24 ore o nell'ultima settimana.

     - **Logica avvisi**:

       - **Livello evento**: livello di gravità dell'evento: _dettagliato_, _informativo_, _avviso_, _errore_o _critico_.
       - **Stato**: stato dell'evento: _avviato_, _non riuscito_o _riuscito_.
       - **Evento avviato da**: noto anche come chiamante. L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

       Questo grafico di segnale di esempio presenta la logica di avviso applicata:

       ![Criteri selezionati](media/alerts-activity-log/criteria-selected.png)

4. In **Definisci dettagli avviso**specificare i dettagli seguenti:

    - **Nome regola di avviso**: il nome della nuova regola di avviso.
    - **Descrizione**: la descrizione per la nuova regola di avviso.
    - **Salva avviso nel gruppo di risorse**: selezionare il gruppo di risorse in cui si vuole salvare la nuova regola.

5. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo di azioni](../../azure-monitor/platform/action-groups.md) e assegnarlo alla nuova regola. Per creare un nuovo gruppo, selezionare **+ nuovo gruppo**.

6. Per abilitare le regole dopo averle create, selezionare **Sì** per l'opzione **Abilita regola al momento della creazione** .
7. Selezionare **Crea regola di avviso**.

    Viene creata la nuova regola di avviso per il log attività e viene visualizzato un messaggio di conferma nell'angolo superiore destro della finestra.

    È possibile abilitare, disabilitare, modificare o eliminare una regola. Altre informazioni su come gestire le regole del log attività.


Una semplice analogia per comprendere le condizioni in cui è possibile creare regole di avviso in un log attività consiste nell'esplorare o filtrare gli eventi tramite il [log attività nel portale di Azure](activity-log-view.md#azure-portal). Nella schermata **monitoraggio di Azure-log attività** è possibile filtrare o trovare l'evento necessario, quindi creare un avviso usando il pulsante **Aggiungi avviso del log attività** . Seguire quindi i passaggi da 4 a 7, come illustrato in precedenza.
    
 ![Aggiungi avviso dal log attività](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visualizzare e gestire nella portale di Azure

1. Nella portale di Azure selezionare **monitoraggio**  > **avvisi**. Selezionare **Gestisci regole di avviso** nell'angolo superiore sinistro della finestra.

    ![Gestire le regole di avviso](media/alerts-activity-log/manage-alert-rules.png)

    Viene visualizzato l'elenco delle regole disponibili.

2. Cercare la regola del log attività da modificare.

    ![Cerca regole di avviso del log attività](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Per trovare la regola di attività che si vuole modificare, è possibile usare i filtri, la _sottoscrizione_, il _gruppo di risorse_, la _risorsa_, il _tipo di segnale_o _lo stato_disponibili.

   > [!NOTE]
   > 
   > È possibile modificare solo la **Descrizione**, i **criteri di destinazione**e i gruppi di **azioni**.

3. Selezionare la regola e fare doppio clic per modificare le opzioni della regola. Apportare le modifiche necessarie e quindi selezionare **Salva**.

   ![Gestire le regole di avviso](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. È possibile abilitare, disabilitare o eliminare una regola. Selezionare l'opzione appropriata nella parte superiore della finestra dopo aver selezionato la regola come descritto nel passaggio 2.


## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Per creare un avviso del log attività usando un modello di Azure Resource Manager, è necessario creare una risorsa di tipo `microsoft.insights/activityLogAlerts`. Compilare quindi tutte le proprietà correlate. Di seguito è riportato un modello che crea un avviso del log attività:

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
Il codice JSON di esempio precedente può essere salvato come, ad esempio, sampleActivityLogAlert. JSON allo scopo di questa procedura dettagliata e può essere distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Potrebbero essere necessari fino a 5 minuti affinché la nuova regola di avviso del log attività diventi attiva.

## <a name="rest-api"></a>API REST 
L' [API per gli avvisi del log attività di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) è un'API REST. È completamente compatibile con l'API REST di Azure Resource Manager. Può essere usato tramite PowerShell usando il cmdlet Gestione risorse o l'interfaccia della riga di comando di Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuire il modello di Gestione risorse con PowerShell
Per usare PowerShell per distribuire il modello di Gestione risorse di esempio illustrato nella sezione [modello Azure Resource Manager](#azure-resource-manager-template) precedente, usare il comando seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

dove sampleActivityLogAlert. Parameters. JSON contiene i valori specificati per i parametri necessari per la creazione della regola di avviso.

### <a name="use-activity-log-powershell-cmdlets"></a>Usare i cmdlet di PowerShell per il log attività

Per gli avvisi del log attività sono disponibili cmdlet di PowerShell dedicati:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): crea un nuovo avviso del log attività o aggiorna un avviso del log attività esistente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): ottiene una o più risorse per gli avvisi del log attività.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Abilita un avviso del log attività esistente e ne imposta i tag.
- [Disable-AzActivityLogAlert: Disabilita](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert)un avviso del log attività esistente e ne imposta i tag.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): rimuove un avviso del log attività.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per la gestione delle regole di avviso del log attività sono disponibili comandi dedicati dell'interfaccia della riga di comando di Azure nel set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert).

Per creare una nuova regola di avviso del log attività, usare i comandi seguenti in questo ordine:

1. [AZ monitor Activity-Log Alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): crea una nuova risorsa della regola di avviso del log attività.
1. [AZ monitor Activity-Log Alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): aggiungere l'ambito per la regola di avviso del log attività creata.
1. [AZ monitor Activity-Log Alert Action-Group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): aggiungere un gruppo di azione alla regola di avviso del log attività.

Per recuperare una risorsa della regola di avviso del log attività, usare il comando dell'interfaccia della riga di comando di Azure [AZ monitor Activity-Log Alert Show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Per visualizzare tutte le risorse della regola di avviso del log attività in un gruppo di risorse, usare [AZ monitor Activity-Log Alert List](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Le risorse della regola di avviso del log attività possono essere rimosse usando il comando dell'interfaccia della riga di comando di Azure [AZ monitor Activity-Log Alert Delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sullo [schema webhook per i log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leggi una [Panoramica dei log attività](../../azure-monitor/platform/activity-log-alerts.md).
- Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).  
- Informazioni sulle [notifiche per l'integrità del servizio](../../azure-monitor/platform/service-notifications.md).
