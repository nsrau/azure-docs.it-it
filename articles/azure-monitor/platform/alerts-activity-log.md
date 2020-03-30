---
title: Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di AzureCreate, view, and manage activity log alerts in Azure Monitor
description: Creare avvisi del log attività usando il portale di Azure, un modello di Azure Resource Manager e Azure PowerShell.Create activity log alerts by using the Azure portal, an Azure Resource Manager template, and Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132399"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log attività tramite Monitoraggio di AzureCreate, view, and manage activity log alerts by using Azure Monitor  

## <a name="overview"></a>Panoramica

Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso.

Questi avvisi sono per le risorse di Azure e possono essere creati usando un modello di Azure Resource Manager.These alerts are for Azure resources and can be created by using an Azure Resource Manager template. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In genere, si creano avvisi del log attività per ricevere notifiche quando si verificano modifiche specifiche alle risorse nella sottoscrizione di Azure.Typically, you create activity log alerts to receive notifications when specific changes occur to resources in your Azure subscription. L'ambito degli avvisi è spesso limitato a specifici gruppi di risorse o risorse. Ad esempio, è possibile ricevere una notifica quando una macchina virtuale nel gruppo di risorse di esempio **myProductionResourceGroup** viene eliminata. In alternativa, è possibile ricevere una notifica se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.

> [!IMPORTANT]
> Gli avvisi per la notifica di integrità del servizio non possono essere creati tramite l'interfaccia per la creazione di avvisi del log attività. Per altre informazioni su come creare e usare le notifiche di integrità del servizio, vedere Ricevere avvisi del log attività nelle notifiche di integrità del [servizio.](alerts-activity-log-service-notifications.md)

Quando si creano regole di avviso, verificare quanto segue:

- La sottoscrizione nell'ambito non è diversa dalla sottoscrizione in cui viene creato l'avviso.
- I criteri devono essere il livello, lo stato, il chiamante, il gruppo di risorse, l'ID risorsa o la categoria di eventi del tipo di risorsa in cui è configurato l'avviso.
- Non esiste alcuna condizione "anyOf" o condizioni annidate nel codice JSON di configurazione dell'avviso. Fondamentalmente, è consentita una sola condizione "allOf" senza ulteriori condizioni "allOf" o "anyOf".
- Quando la categoria è "amministrativa", è necessario specificare almeno uno dei criteri precedenti nell'avviso. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.

## <a name="azure-portal"></a>Portale di Azure

È possibile usare il portale di Azure per creare e modificare le regole di avviso del log attività. L'esperienza è integrata con un log attività di Azure per garantire la creazione di avvisi senza soluzione di continuità per eventi specifici di interesse.

### <a name="create-with-the-azure-portal"></a>Creare con il portale di AzureCreate with the Azure portal

Usare la procedura seguente.

1. Nel portale di Azure selezionare **Monitora** > **avvisi.**
2. Selezionare Nuova regola di avviso nell'angolo superiore sinistro della finestra Avvisi.Select **New alert rule** in the upper-left corner of the **Alerts** window.

     ![Nuova regola di avviso](media/alerts-activity-log/AlertsPreviewOption.png)

     Viene visualizzata la finestra **Crea regola**.

      ![Nuove opzioni delle regole di avviso](media/alerts-activity-log/create-new-alert-rule-options.png)

3. In **Definisci condizione di avviso**, fornire le informazioni seguenti e selezionare **Fatto**:

   - **Destinazione avviso:** Per visualizzare e selezionare la destinazione per il nuovo avviso, utilizzare **Filtra per sottoscrizione** / **Filtra per tipo di risorsa**. Selezionare la risorsa o il gruppo di risorse dall'elenco visualizzato.

     > [!NOTE]
     > 
     > È possibile selezionare solo la risorsa di rilevamento di [Azure Resource Manager,](../../azure-resource-manager/management/overview.md) un gruppo di risorse o un'intera sottoscrizione per un segnale del log attività. 

     **Visualizzazione di esempio di destinazione di avviso**

     ![Selezionare la destinazione](media/alerts-activity-log/select-target.png)

   - In **Criteri di destinazione**selezionare Aggiungi **criteri**. Vengono visualizzati tutti i segnali disponibili per la destinazione, inclusi quelli di varie categorie di **Registro attività.** Il nome della categoria viene aggiunto al nome del **servizio di monitoraggio.**

   - Selezionare il segnale nell'elenco visualizzato delle varie operazioni possibili per il tipo di **Log attività**.

     È possibile selezionare la sequenza temporale della cronologia di log e la logica degli avvisi corrispondente per il segnale di destinazione:

     **Schermata Aggiungi criteri**

     ![Aggiungi criteri](media/alerts-activity-log/add-criteria.png)

     - **Tempo storico**: Gli eventi disponibili per l'operazione selezionata possono essere tracciati nelle ultime 6, 12 o 24 ore o nell'ultima settimana.

     - **Logica di avviso**:

       - **Livello evento**: Livello di gravità dell'evento: Dettagliato , _Informativo_, _Informational_ _Avviso_, _Errore_o _Critico_.
       - **Stato**: lo stato dell'evento: _Avviato_, _Non riuscito_o _Operazione completata_.
       - **Evento avviato da**: noto anche come chiamante. L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

       Questo grafico di segnale di esempio ha la logica di avviso applicata:This sample signal graph has the alert logic applied:

       ![Criteri selezionati](media/alerts-activity-log/criteria-selected.png)

4. In **Definire i dettagli dell'avviso**specificare i dettagli seguenti:

    - **Nome regola di avviso:** nome per la nuova regola di avviso.
    - **Descrizione**: Descrizione della nuova regola di avviso.
    - **Salva avviso nel gruppo**di risorse: selezionare il gruppo di risorse in cui si vuole salvare la nuova regola.

5. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo](../../azure-monitor/platform/action-groups.md) di azioni e assegnarlo alla nuova regola. Per creare un nuovo gruppo, selezionare **Nuovo gruppo**.

6. Per abilitare le regole dopo averle create, selezionare **Sì** per l'opzione Abilita regola al momento della **creazione.**
7. Selezionare **Crea regola di avviso**.

    Viene creata la nuova regola di avviso per il log attività e viene visualizzato un messaggio di conferma nell'angolo superiore destro della finestra.

    È possibile abilitare, disabilitare, modificare o eliminare una regola. Ulteriori informazioni su come gestire le regole del log attività.


Una semplice analogia per comprendere le condizioni in cui è possibile creare regole di avviso in un log attività consiste nell'esplorare o filtrare gli eventi tramite il log attività nel portale di [Azure.](activity-log-view.md#azure-portal) Nella schermata **Monitoraggio di Azure - Log attività** è possibile filtrare o trovare l'evento necessario e quindi creare un avviso usando il pulsante Aggiungi avviso log **attività.** Quindi seguire i passaggi da 4 a 7 come illustrato in precedenza.
    
 ![Aggiungi avviso dal registro attività](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visualizzare e gestire nel portale di AzureView and manage in the Azure portal

1. Nel portale di Azure selezionare **Monitora** > **avvisi.** Selezionare **Gestisci regole** di avviso nell'angolo superiore sinistro della finestra.

    ![Gestire le regole di avviso](media/alerts-activity-log/manage-alert-rules.png)

    Viene visualizzato l'elenco delle regole disponibili.

2. Cercare la regola del log attività da modificare.

    ![Regole di avviso del log attività di ricerca](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    È possibile utilizzare i filtri disponibili, _Sottoscrizione_, _Gruppo risorse_, _Risorsa_, Tipo _di segnale_o _Stato_, per trovare la regola di attività che si desidera modificare.

   > [!NOTE]
   > 
   > È possibile modificare solo i **gruppi** **Descrizione**, Criteri di **destinazione**e Gruppi di azioni .

3. Selezionare la regola e fare doppio clic per modificare le opzioni della regola. Apportare le modifiche necessarie e quindi selezionare **Salva**.

   ![Gestire le regole di avviso](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. È possibile abilitare, disabilitare o eliminare una regola. Selezionare l'opzione appropriata nella parte superiore della finestra dopo aver selezionato la regola come descritto nel passaggio 2.


## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Per creare una regola di avviso del log attività usando un `microsoft.insights/activityLogAlerts`modello di Azure Resource Manager, creare una risorsa di tipo . Compilare quindi tutte le proprietà correlate. Ecco un modello che crea una regola di avviso del log attività:Here's a template that creates an activity log alert rule:

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
Il codice JSON di esempio precedente può essere salvato, ad esempio, come sampleActivityLogAlert.json allo scopo di questa procedura utente e può essere distribuito tramite Azure Resource Manager nel portale di Azure.The previous sample JSON can be saved as, for example, sampleActivityLogAlert.json for the purpose of this walk-through and can be deployed by using [Azure Resource Manager in the Azure portal](../../azure-resource-manager/templates/deploy-portal.md).

I campi seguenti sono le opzioni che è possibile usare nel modello di Azure Resource Manager per i campi delle condizioni: si noti che "Integrità risorse", "Advisor" e "Integrità dei servizi" dispongono di campi di proprietà aggiuntivi per i campi speciali. 
1. resourceId: l'ID risorsa della risorsa interessata nell'evento del log attività in cui deve essere generato l'avviso.
2. category: la categoria di nell'evento del registro attività. Ad esempio: Amministrativo, ServiceHealth, ResourceHealth, Autoscale, Sicurezza, Raccomandazione, Politica.
3. chiamante: l'indirizzo di posta elettronica o l'identificatore di Azure Active Directory dell'utente che ha eseguito il funzionamento dell'evento del log attività.
4. level: livello dell'attività nell'evento del log attività su cui deve essere generato l'avviso. Ad esempio: Critico, Errore, Avviso, Informativo, Dettagliato.
5. operationName: il nome dell'operazione nell'evento del registro attività. Ad esempio: Microsoft.Resources/deployments/write
6. resourceGroup: nome del gruppo di risorse per la risorsa interessata nell'evento del log attività.
7. resourceProvider: [spiegazione](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)dei tipi e dei provider di risorse di Azure . Per un elenco che esegue il mapping dei provider di risorse ai servizi di Azure, vedere Provider di risorse per i servizi di Azure.For a list that maps resource providers to Azure services, see [Resource providers for Azure services.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)
8. status: stringa che descrive lo stato dell'operazione nell'evento attività. Ad esempio: Avviato, In corso, Riuscito, Non riuscito, Attivo, Risolto
9. subStatus: in genere il codice di stato HTTP della chiamata REST corrispondente, ma può includere anche altre stringhe che descrivono uno stato secondario.   Ad esempio: OK (codice di stato HTTP: 200), Creato (codice di stato HTTP: 201), Accettato (codice di stato HTTP: 202), Nessun contenuto (codice di stato HTTP: 204), Richiesta non valida (codice di stato HTTP: 400), Non trovato (codice di stato HTTP: 404), Conflitto (codice di stato HTTP: 409), Server interno Errore (codice di stato HTTP: 500), servizio non disponibile (codice di stato HTTP: 503), Timeout gateway (codice di stato HTTP: 504).
10. resourceType: il tipo della risorsa interessata dall'evento. Ad esempio: Microsoft.Resources/deployments

Ad esempio:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Maggiori dettagli sui campi del registro attività che [potete](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)trovare qui .



> [!NOTE]
> Potrebbero essere nuovi 5 minuti prima che la nuova regola di avviso del log attività diventi attiva.

## <a name="rest-api"></a>API REST 
[L'API Avvisi log attività](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) di Monitoraggio di Azure è un'API REST. È completamente compatibile con l'API REST di Azure Resource Manager.It's fully compatible with the Azure Resource Manager REST API. Può essere usato tramite PowerShell usando il cmdlet Resource Manager o l'interfaccia della riga di comando di Azure.It can be used via PowerShell by using the Resource Manager cmdlet or the Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuire il modello di Resource Manager con PowerShellDeploy the Resource Manager template with PowerShell
Per usare PowerShell per distribuire il modello di Resource Manager di esempio illustrato nella sezione precedente del modello di [Azure Resource Manager,](#azure-resource-manager-template) usare il comando seguente:To use PowerShell to deploy the sample Resource Manager template shown in the previous Azure Resource Manager template section, use the following command:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

dove sampleActivityLogAlert.parameters.json contiene i valori forniti per i parametri necessari per la creazione della regola di avviso.

### <a name="use-activity-log-powershell-cmdlets"></a>Utilizzare i cmdlet di PowerShell del log attività

Per gli avvisi del log attività sono disponibili cmdlet di PowerShell dedicati:

- [Set-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert)crea un nuovo avviso del log attività o aggiorna un avviso del log attività esistente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Ottiene una o più risorse di avviso del log attività.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Abilita un avviso del log attività esistente e ne imposta i tag.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): disabilita un avviso del log attività esistente e ne imposta i tag.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): rimuove un avviso del log attività.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per la gestione delle regole di avviso del log attività sono disponibili comandi dedicati dell'interfaccia della riga di comando di Azure nel set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert).

Per creare una nuova regola di avviso del log attività, utilizzare i comandi seguenti nell'ordine indicato:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Creare una nuova risorsa regola di avviso del log attività.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Aggiungere l'ambito per la regola di avviso del log attività creato.
1. [az monitor activity-log alert action-group:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)aggiungere un gruppo di azioni alla regola di avviso del log attività.

Per recuperare una risorsa regola di avviso del log attività, usare il comando dell'interfaccia della riga di comando di Azure [che a monitor per il log attività.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) Per visualizzare tutte le risorse della regola di avviso del log attività in un gruppo di risorse, usare [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Le risorse della regola di avviso del log attività possono essere rimosse usando il comando dell'interfaccia della riga di comando di Azure che utilizza l'eliminazione degli avvisi del log attività di Monitoraggio [attività.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sullo [schema webhook per i log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leggere [una panoramica dei log attività](../../azure-monitor/platform/activity-log-alerts.md).
- Ulteriori informazioni sui [gruppi di azioni](../../azure-monitor/platform/action-groups.md).  
- Informazioni sulle notifiche di integrità del [servizio](../../azure-monitor/platform/service-notifications.md).
