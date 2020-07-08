---
title: Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di Azure
description: Creare avvisi del log attività usando il portale di Azure, un modello di Azure Resource Manager e Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 242192118d59f972cebe2837d74c34310cac74aa
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056260"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log attività usando Monitoraggio di Azure  

## <a name="overview"></a>Panoramica

Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso.

Si tratta di avvisi per le risorse di Azure e possono essere creati usando un modello di Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In genere si creano avvisi del log attività per ricevere una notifica quando si verificano specifici cambiamenti nelle risorse presenti nella sottoscrizione di Azure. Gli avvisi sono spesso limitati a particolari risorse o gruppi di risorse. Potrebbe ad esempio essere utile ricevere una notifica quando viene eliminata una macchina virtuale nel gruppo di risorse di esempio **myProductionResourceGroup** o se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.

> [!IMPORTANT]
> Non è possibile creare avvisi di notifica sull'integrità dei servizi tramite l'interfaccia per la creazione degli avvisi del log attività. Per altre informazioni sulla creazione e l'uso delle notifiche sull'integrità dei servizi, vedere [Creare gli avvisi del log attività per le notifiche del servizio](alerts-activity-log-service-notifications.md).

Quando si creano le regole di avviso, verificare quanto segue:

- La sottoscrizione nell'ambito non deve essere diversa dalla sottoscrizione in cui viene creato l'avviso.
- I criteri devono essere livello, stato, chiamante, gruppo di risorse, ID risorsa, tipo di risorsa o categoria di eventi in cui è stato configurato l'avviso.
- Nel codice JSON della configurazione dell'avviso non sono presenti condizioni "anyOf" o condizioni nidificate. In pratica, è consentito un solo "allOf" senza ulteriori condizioni "allOf" o "anyOf".
- Se la categoria è impostata su "Administrative", nell'avviso è necessario specificare almeno uno dei criteri precedenti. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.

## <a name="azure-portal"></a>Portale di Azure

È possibile usare il portale di Azure per creare e modificare le regole di avviso del log attività. L'esperienza è integrata con un log attività di Azure per garantire la massima semplicità nella creazione di avvisi per specifici eventi di interesse.

### <a name="create-with-the-azure-portal"></a>Creare con il portale di Azure

Seguire la procedura indicata di seguito.

1. Nel portale di Azure selezionare **Monitoraggio** > **Avvisi**.
2. Selezionare **Nuova regola di avviso** nell'angolo superiore sinistro della finestra **Avvisi**.

     ![Nuova regola di avviso](media/alerts-activity-log/AlertsPreviewOption.png)

     Viene visualizzata la finestra **Crea regola**.

      ![Nuove opzioni delle regole di avviso](media/alerts-activity-log/create-new-alert-rule-options.png)

3. In **Definire la condizione dell'avviso** inserire le informazioni seguenti e selezionare **Fatto**:

   - **Destinazione avviso**: Per visualizzare e selezionare la destinazione per il nuovo avviso, usare **Filtra per sottoscrizione** / **Filtra per tipo di risorsa**. Selezionare la risorsa o il gruppo di risorse dall'elenco visualizzato.

     > [!NOTE]
     > 
     > Per un segnale del log attività è possibile selezionare solo una risorsa verificata di [Azure Resource Manager](../../azure-resource-manager/management/overview.md), un gruppo di risorse o un'intera sottoscrizione. 

     **Visualizzazione di esempio di destinazione di avviso**

     ![Selezionare la destinazione](media/alerts-activity-log/select-target.png)

   - In **Criteri di destinazione**selezionare **Aggiungi criteri**. Vengono visualizzati tutti i segnali disponibili per la destinazione, inclusi quelli delle varie categorie di **Log attività**. Il nome della categoria è accodato al nome del **servizio di monitoraggio**.

   - Selezionare il segnale nell'elenco visualizzato delle varie operazioni possibili per il tipo di **Log attività**.

     È possibile selezionare la sequenza temporale della cronologia di log e la logica degli avvisi corrispondente per il segnale di destinazione:

     **Schermata Aggiungi criteri**

     ![Aggiungi criteri](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Per avere regole efficaci e di qualità elevata, è necessario aggiungere almeno un'altra condizione alle regole con il segnale "All Administrative". 
     > Nell'ambito della definizione dell'avviso è necessario compilare uno degli elenchi a discesa: "Livello evento", "Stato" o "Avviato da" e la regola sarà più specifica.

     - **Periodo cronologia**: è possibile tracciare gli eventi disponibili per l'operazione selezionata nelle ultime 6, 12 o 24 ore oppure nell'ultima settimana.

     - **Logica avvisi**:

       - **Livello evento**: il livello di gravità dell'evento: _Dettagliato_, _Informativo_ _Avviso_, _Errore_ o _Critico_.
       - **Stato**: lo stato dell'evento: _Avviato_, _Operazione non riuscita_ o _Operazione completata_.
       - **Evento avviato da**: noto anche come "chiamante". L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

       Questo è un grafico del segnale di esempio con la logica avvisi applicata:

       ![Criteri selezionati](media/alerts-activity-log/criteria-selected.png)

4. In **Definire i dettagli dell'avviso** fornire i dettagli seguenti:

    - **Nome regola di avviso**: nome della nuova regola di avviso.
    - **Descrizione**: descrizione della nuova regola di avviso.
    - **Salva avviso nel gruppo di risorse**: selezionare il gruppo di risorse in cui salvare la nuova regola.

5. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo di azioni](../../azure-monitor/platform/action-groups.md) e assegnarlo alla nuova regola. Per creare un nuovo gruppo, selezionare **+ Nuovo gruppo**.

6. Per abilitare le regole dopo averle create, selezionare **Sì** per l'opzione **Abilita regola alla creazione**.
7. Selezionare **Crea regola di avviso**.

    Viene creata la nuova regola di avviso per il log attività e nell'angolo superiore destro viene visualizzato un messaggio di conferma.

    È possibile abilitare, disabilitare, modificare o eliminare una regola. Leggere altre informazioni su come gestire le regole del log attività.


Una semplice analogia per comprendere le condizioni in base a cui è possibile creare regole di avviso in un log attività è quella di esplorare o filtrare gli eventi tramite il [log attività nel portale di Azure](activity-log-view.md#azure-portal). Nella schermata **Monitoraggio di Azure - Log attività** è possibile filtrare o trovare l'evento necessario e quindi creare un avviso usando il pulsante **Aggiungi avviso del log attività**. Seguire quindi i passaggi da 4 a 7 come illustrato in precedenza.
    
 ![Aggiungi avviso dal log attività](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visualizzare e gestire gli avvisi nel portale di Azure

1. Nel portale di Azure selezionare **Monitoraggio** > **Avvisi**. Selezionare **Gestisci regole di avviso** nell'angolo superiore sinistro della finestra.

    ![Gestire le regole di avviso](media/alerts-activity-log/manage-alert-rules.png)

    Viene visualizzato l'elenco delle regole disponibili.

2. Cercare la regola del log attività da modificare.

    ![Cercare le regole di avviso del log attività](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    È possibile usare i filtri disponibili, _Sottoscrizione_, _Gruppo di risorse_, _Risorsa_, _Tipo di segnale_ o _Stato_ per trovare la regola dell'attività che si vuole modificare.

   > [!NOTE]
   > 
   > È possibile modificare solo la **Descrizione**, i **Criteri di destinazione** e i **Gruppi di azioni**.

3. Selezionare la regola e fare doppio clic per modificare le opzioni. Apportare le modifiche necessarie e quindi selezionare **Salva**.

   ![Gestire le regole di avviso](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. È possibile abilitare, disabilitare o eliminare una regola. Selezionare l'opzione appropriata nella parte superiore della finestra, dopo aver selezionato la regola come descritto nel passaggio 2.


## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Per creare una regola di avviso del log attività usando un modello di Azure Resource Manager, creare una risorsa di tipo `microsoft.insights/activityLogAlerts`. Compilare quindi tutte le proprietà correlate. Il modello seguente crea una regola di avviso del log attività:

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
Il codice JSON di esempio precedente può essere salvato, ad esempio con il nome sampleActivityLogAlert.json ai fini di questa procedura dettagliata, e distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Si noti che è possibile definire gli avvisi del log attività di livello più alto è Subscription.
  > Il che significa che non è possibile definire un avviso per due sottoscrizioni, pertanto la definizione deve essere un avviso per ogni sottoscrizione.

I campi seguenti sono le opzioni che è possibile usare nel modello di Azure Resource Manager per i campi delle condizioni: Si noti che "Resource Health", "Advisor" e "Service Health" hanno campi di proprietà aggiuntivi per i propri campi speciali. 
1. resourceId:  ID risorsa della risorsa interessata nell'evento del log attività su cui deve essere generato l'avviso.
2. category: categoria nell'evento del log attività. Ad esempio: Administrative, ServiceHealth, ResourceHealth, Autoscale, Security, Recommendation, Policy.
3. caller: indirizzo di posta elettronica o identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione dell'evento del log attività.
4. level: livello dell'attività nell'evento del log attività su cui deve essere generato l'avviso. Ad esempio: Critical, Error, Warning, Informational, Verbose.
5. operationName: nome dell'operazione nell'evento del log attività. Ad esempio: Microsoft.Resources/deployments/write
6. resourceGroup: nome del gruppo di risorse della risorsa interessata nell'evento del log attività.
7. resourceProvider: [spiegazione dei provider e dei tipi di risorse di Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Per un elenco con il mapping dei provider di risorse ai servizi di Azure, vedere [provider di risorse per i servizi di Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: stringa che descrive lo stato dell'operazione nell'evento attività. Ad esempio: Started, In Progress, Succeeded, Failed, Active, Resolved
9. subStatus: In genere il codice di stato HTTP della chiamata REST corrispondente, ma può includere anche altre stringhe che descrivono uno stato secondario.   Ad esempio: OK (codice di stato HTTP: 200), Creata (codice di stato HTTP: 201), Accettata (codice di stato HTTP: 202), Nessun contenuto (codice di stato HTTP: 204), Richiesta non valida (codice di stato HTTP: 400), Non trovata (codice di stato HTTP: 404), Conflitto (codice di stato HTTP: 409), Errore interno del server (codice di stato HTTP: 500), Servizio non disponibile (codice di stato HTTP: 503), Timeout gateway (codice di stato HTTP: 504).
10. resourceType: tipo di risorsa che è stata interessata dall'evento. Ad esempio: Microsoft.Resources/deployments

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
Maggiori informazioni sui campi del log attività sono disponibili [qui](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Possono essere necessari fino a 5 minuti prima che una nuova regola di avviso del log attività diventi attiva.

## <a name="rest-api"></a>API REST 
L'[API Avvisi del log attività di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) è un'API REST. È completamente compatibile con l'API REST di Azure Resource Manager. Può essere usata con il cmdlet di Resource Manager tramite PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuire il modello di Resource Manager con PowerShell
Per usare PowerShell per distribuire il modello di Resource Manager di esempio illustrato nella sezione [Modello di Azure Resource Manager](#azure-resource-manager-template) precedente, usare il comando seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

dove sampleActivityLogAlert.parameters.json contiene i valori dei parametri necessari per la creazione della regola di avviso.

### <a name="use-activity-log-powershell-cmdlets"></a>Usare i cmdlet di PowerShell per il log attività

Per gli avvisi del log attività sono disponibili cmdlet di PowerShell dedicati:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): crea un nuovo avviso del log attività o aggiorna un avviso del log attività esistente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): recupera una o più risorse di avviso del log attività.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): abilita un avviso del log attività esistente e ne imposta i tag.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): disabilita un avviso del log attività esistente e ne imposta i tag.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): rimuove un avviso del log attività.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per la gestione delle regole di avviso del log attività sono disponibili comandi dedicati dell'interfaccia della riga di comando di Azure nel set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert).

Per creare una nuova regola di avviso del log attività, usare i comandi seguenti nell'ordine indicato:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): creare una nuova risorsa di regola di avviso del log attività.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): aggiungere l'ambito per la regola di avviso del log attività creata.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): aggiungere un gruppo di azioni alla regola di avviso del log attività.

Per recuperare una risorsa di regola di avviso del log attività, usare il comando [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) dell'interfaccia della riga di comando di Azure. Per visualizzare tutte le risorse di regola di avviso del log attività in un gruppo di risorse, usare [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Le risorse di regola di avviso del log attività possono essere rimosse usando il comando [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) dell'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sullo [schema dei webhook per i log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leggere una [panoramica dei log attività](../../azure-monitor/platform/activity-log-alerts.md).
- Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).  
- Informazioni sulle [notifiche per l'integrità del servizio](../../azure-monitor/platform/service-notifications.md).
