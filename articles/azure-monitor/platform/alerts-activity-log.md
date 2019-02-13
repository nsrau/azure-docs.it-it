---
title: Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di Azure
description: Come creare avvisi del log attività tramite il portale di Azure, il modello di risorsa e PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: 2b90457ed939999b5163078750650c92a3516cca
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816578"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di Azure  

## <a name="overview"></a>Panoramica
Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso.

Si tratta di avvisi per le risorse di Azure e possono essere creati usando un modello di Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In genere si creano avvisi del log attività per ricevere una notifica quando si verificano modifiche specifiche nelle risorse presenti nella sottoscrizione di Azure. Questi avvisi sono spesso limitati a risorse o gruppi di risorse specifici. Ad esempio è possibile ricevere una notifica quando una macchina virtuale nel gruppo di risorse di esempio **myProductionResourceGroup** viene eliminata oppure quando nuovi ruoli vengono assegnati a un utente nella sottoscrizione.

> [!IMPORTANT]
> Non è possibile creare avvisi di notifica sull'integrità dei servizi tramite l'interfaccia per la creazione degli avvisi del log attività. Per altre informazioni relative alla creazione e all'uso delle notifiche sull'integrità dei servizi, vedere [Creare gli avvisi del log attività per le notifiche del servizio](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Portale di Azure

> [!NOTE]

>  Quando si creano le regole di avviso, verificare quanto segue:

> - La sottoscrizione nell'ambito non deve essere diversa dalla sottoscrizione in cui viene creato l'avviso.
- I criteri devono essere livello/stato/chiamante/gruppo di risorse/id risorsa/tipo di risorsa o categoria di eventi in cui è stato configurato l'avviso.
- Non sono presenti condizioni "anyOf" o condizioni nidificate nella configurazione degli avvisi JSON (in pratica, è consentito un solo allOf senza ulteriori allOf/anyOf).
- Quando la categoria è "administrative" (amministrativa). È necessario specificare nell'avviso almeno uno dei criteri precedenti. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.

### <a name="create-with-azure-portal"></a>Creare con il portale di Azure

Utilizzare la procedura seguente:

1. Nel portale di Azure selezionare **Monitoraggio** > **Avvisi**
2. Fare clic su **Nuova regola di avviso** in alto alla finestra **Avvisi**.

     ![nuova regola di avviso](media/alerts-activity-log/AlertsPreviewOption.png)

     Viene visualizzata la finestra **Crea regola**.

      ![nuove opzioni delle regole di avviso](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Nella condizione **Definisci avviso** fornire le informazioni seguenti e fare clic su **Fatto**.

    - **Destinazione avviso**: per visualizzare e selezionare la destinazione per il nuovo avviso, usare **Filtra per sottoscrizione** / **Filtra per tipo di risorsa** e selezionare la risorsa o un gruppo di risorse nell'elenco visualizzato.

    > [!NOTE]

    > Per il segnale del log attività è possibile selezionare una risorsa, un gruppo di risorse o un'intera sottoscrizione.

    **Visualizzazione di esempio di destinazione di avviso** ![Selezionare la destinazione](media/alerts-activity-log/select-target.png)

    - In **Criteri di destinazione** fare clic su **Aggiungi criteri**. Verranno visualizzati tutti i segnali disponibili per la destinazione, inclusi quelli appartenenti alle varie categorie di **Log attività** con il nome della categoria accodato al nome del **Servizio di monitoraggio**.

    - Selezionare il segnale nell'elenco visualizzato delle varie operazioni possibili per il tipo di **Log attività**.

    È possibile selezionare la sequenza temporale della cronologia di log e la logica degli avvisi corrispondente per il segnale di destinazione:

    **Schermata Aggiungi criteri**

    ![aggiungi criteri](media/alerts-activity-log/add-criteria.png)

    **Periodo cronologia**: è possibile tracciare gli eventi disponibili per l'operazione selezionata nelle ultime 6/12/24 ore oppure nell'ultima settimana.

    **Logica avvisi**:

     - **Livello evento**: il livello di gravità dell'evento. _Dettagliato_, _Informativo_ _Avviso_, _Errore_ o _Critico_.
     - **Stato**: Lo stato dell'evento. _Avviato_, _Operazione non riuscita_ o _Operazione completata_.
     - **Evento avviato da**: noto anche come il chiamante. L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

        Grafico del segnale di esempio con logica avvisi applicata:

        ![ criteri selezionati](media/alerts-activity-log/criteria-selected.png)

4. In **Definire i dettagli dell'avviso** fornire i dettagli seguenti:

    - **Nome regola di avviso**: nome della nuova regola di avviso
    - **Descrizione**: descrizione della nuova regola di avviso
    - **Salva avviso nel gruppo di risorse**: selezionare il gruppo di risorse in cui salvare la nuova regola.

5. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo di azioni](../../azure-monitor/platform/action-groups.md) e assegnarlo alla nuova regola. Per creare un nuovo gruppo, fare clic su **+ Nuovo gruppo**.

6. Per abilitare le regole dopo averle create, fare clic su **Sì** per **Abilita regola alla creazione**.
7. Fare clic su **Crea regola di avviso**.

    Viene creata la nuova regola di avviso per il log attività e viene visualizzato un messaggio di conferma nella parte superiore destra della finestra.

    È possibile abilitare, disabilitare, modificare o eliminare una regola. Altre informazioni sulla gestione delle regole del log attività.


In alternativa, una semplice analogia per comprendere le condizioni in cui è possibile creare le regole di avviso nel log attività è quella di esplorare o filtrare gli eventi tramite il [Log attività nel portale di Azure](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). In Monitoraggio di Azure - Log attività, un utente può filtrare o trovare l'evento necessario e quindi creare un avviso usando il pulsante **Aggiungi avviso del log attività**, quindi seguire i passaggi dal 4 in poi come indicato in precedenza nell'esercitazione.
    
 ![ Aggiungere un avviso dal log attività](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Visualizzare e gestire gli avvisi nel portale di Azure

1. Nel portale di Azure fare clic su **Monitoraggio** > **Avvisi**, quindi su **Gestisci le regole** nella parte superiore sinistra della finestra.

    ![ gestione delle regole di avviso](media/alerts-activity-log/manage-alert-rules.png)

    Viene visualizzato l'elenco delle regole disponibili.

2. Cercare la regola del log attività da modificare.

    ![ cercare le regole di avviso del log attività](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    È possibile usare i filtri disponibili: _Sottoscrizione_, _Gruppo di risorse_, _Risorsa_, _Tipo di segnale_ o _Stato_ per trovare la regola dell'attività che si vuole modificare.

    > [!NOTE]

    > È possibile modificare solo la **Descrizione**, i **Criteri di destinazione** e i **Gruppi di azioni**.

3.  Selezionare la regola e fare doppio clic per modificare le opzioni della regola. Apportare le modifiche necessarie e quindi fare clic su **Salva**.

    ![ gestione delle regole di avviso](media/alerts-activity-log/activity-log-rule-edit-page.png)

4.  È possibile disabilitare, abilitare o eliminare una regola. Selezionare l'opzione appropriata nella parte superiore della finestra, dopo aver selezionato la regola come descritto nel passaggio 2.


## <a name="azure-resource-template"></a>Modello di risorsa di Azure
Per creare un avviso del log attività usando un modello di Resource Manager, creare una risorsa di tipo `microsoft.insights/activityLogAlerts`. Compilare quindi tutte le proprietà correlate. Il modello seguente crea un avviso del log attività.

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
Per usare il JSON di esempio in questa procedura dettagliata, è possibile salvarlo ad esempio con il nome sampleActivityLogAlert.json. Può essere distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Possono essere necessari fino a 5 minuti prima che una nuova regola di avviso del log attività diventi attiva

## <a name="rest-api"></a>API REST 
L'[API degli avvisi dei log attività di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) è un'API REST completamente compatibile con l'API REST di Azure Resource Manager. Può pertanto essere usata con il cmdlet di Resource Manager tramite Powershell e l'interfaccia della riga di comando di Azure.

## <a name="powershell"></a>PowerShell
Di seguito viene illustrato l'utilizzo tramite il cmdlet di Azure Resource Manager in PowerShell per il modello di risorsa di esempio riportato in precedenza (sampleActivityLogAlert.json) nella sezione Modello di risorsa:
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```
Quando in sampleActivityLogAlert.parameters.json sono presenti i valori dei parametri necessari per la creazione della regola di avviso.

## <a name="cli"></a>CLI
Di seguito viene illustrato l'utilizzo tramite il comando di Azure Resource Manager nell'interfaccia della riga di comando di Azure per il modello di risorsa di esempio riportato in precedenza (sampleActivityLogAlert.json) nella sezione Modello di risorsa:

```azurecli
az group deployment create --resource-group myRG --template-file sampleActivityLogAlert.json --parameters @sampleActivityLogAlert.parameters.json
```
Il file *sampleActivityLogAlert.parameters.json* include i valori dei parametri necessari per la creazione della regola di avviso.


## <a name="next-steps"></a>Passaggi successivi

- [Schema dei webhook per i log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Panoramica dei log attività](../../azure-monitor/platform/activity-log-alerts.md) 
- Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).  
- Informazioni sulle [notifiche per l'integrità del servizio](../../azure-monitor/platform/service-notifications.md).
